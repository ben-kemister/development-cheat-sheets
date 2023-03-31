---
layout: page_with_tags
title:  "Oracle XE 11g to 19c Upgrade"
tags:
    - oracle
    - database
    - sql
---

This post captures the steps that I went through to upgrade my Oracle XE 11g database, which I use for some of my development projects, to Oracle XE version 19c.
<!--more-->

The process below was based on the instructions outlined [here](https://docs.oracle.com/en/database/oracle/oracle-database/18/xeinw/exporting-and-importing-data-oracle-database-xe-11.2-and-18c.html)

## Remove unwanted data/schemas

``` cmd
sqlplus / AS SYSDBA
SQL> DROP USER HR CASCADE;
SQL> exit
```

## Exporting Data

To export data from your 11.2 XE database, perform the following steps:

Create a dump folder, run the following command from your Windows command prompt:
`mkdir C:\temp\dump`

Connect to the 11.2 XE database as user SYS using the SYSDBA privilege.

Create directory object DUMP_DIR and grant READ and WRITE privileges on the DUMP_DIR directory to the SYSTEM user.

``` cmd
sqlplus / AS SYSDBA
SQL> CREATE DIRECTORY DUMP_DIR AS 'C:\temp\dump';
SQL> GRANT READ, WRITE ON DIRECTORY DUMP_DIR TO SYSTEM;
SQL> exit
```

Create a parameters file `params.par` for the export parameters, this avoids having to deal with escaping characters on the command line:

``` txt
full=Y
EXCLUDE=SCHEMA:"LIKE 'APEX_%'",SCHEMA:"LIKE 'FLOWS_%'"
directory=DUMP_DIR
dumpfile=DB11G.dmp
logfile=expdpDB11G.log
```

Export data from your 11.2 XE database to the dump folder.

``` cmd
expdp system/system_password 
# for example
expdp system/password parfile=export_params.par 
```

## Uninstall and Install new Version

Uninstall Oracle Database XE 11.2 if installation of 18c XE is planned on the same system.

Install Oracle Database XE 18c.

## Import the data (into 18c)

To import data in your _new_ 18c XE database, perform the following steps:

Connect to the 18c XE database as user SYS using the SYSDBA privilege.

Create directory object DUMP_DIR and grant READ and WRITE privileges on the directory to the SYSTEM user.

``` cmd
sqlplus / AS SYSDBA
SQL> ALTER SESSION SET CONTAINER=XEPDB1;
SQL> CREATE DIRECTORY DUMP_DIR AS 'C:\temp\dump';
SQL> GRANT READ, WRITE ON DIRECTORY DUMP_DIR TO SYSTEM;
SQL> exit
```

Update/create a file `params.par` for the import parameters, this avoids having to deal with escaping characters on the command line.
> Note: the only change from the import parameter file is the `logfile=impdpDB11G.log` line

``` txt
full=Y
EXCLUDE=SCHEMA:"LIKE 'APEX_%'",SCHEMA:"LIKE 'FLOWS_%'"
directory=DUMP_DIR
dumpfile=DB11G.dmp
logfile=impdpDB11G.log
```

Import data to the 18c XE database from the dump folder.

``` cmd
impdp system/system_password@localhost:listnerport/xepdb1 parfile=params.par
# For example
impdp system/password@localhost:1521/xepdb1 parfile=params.par
```

You can ignore the following errors:

- ORA-39083: Object type TABLESPACE:"SYSAUX" failed to create with error
- ORA-31685: Object type USER:"SYS" failed due to insufficient privileges
- ORA-39083: Object type PROCACT_SYSTEM failed to create with error
- ORA-01917: user or role 'APEX_040000' does not exist
- ORA-31684 "already exists" errors

## Update connection details

If your database connection used the database SID you will need to change this to use the _Service Name_ of the pluggable database.

For example if your previous jdbc connection url was:
`jdbc:oracle:thin:@hostname:port:SID`
this will need to be updated to:
`jdbc:oracle:thin:@hostname:port/service`

### Identifying the Service name (if required)

You can check the listener services that are running to identify the Service name you should be using with the command:
`lsnrctl services`

## Listener service failing to start (Windows)

I encountered an issue on an older Windows PC which prevented the `OracleOraDB18Home1TNSListener` service to start automatically when the system was rebooted/restarted.

I believe this was due a default timeout that windows has for services, if this is exceeded (like when there are a lot of processes starting on an older PC) it prevents the service from starting correctly.

To fix this I changed the **Startup type** to **Automatic (Delayed Start)** which delays the start of the service (defaults to 120 seconds), to when there is less load on the PC.
