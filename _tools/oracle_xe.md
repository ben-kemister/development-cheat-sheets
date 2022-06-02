---
tool: oracle_xe
name: Oracle XE (Express Edition)
tags:
 - oracle
 - database
 - sql
--- 

Oracle XE (Express Edition) is the same powerful Oracle Database that enterprises rely on worldwide, packaged for simple download, ease-of-use, and a full-featured experience. You get an Oracle Database to use in any environment, plus the ability to embed and redistribute – all completely free!
<!--more-->

## Listener

The *listener* is a separate database server process that runs locally on the database server or remotely.

![Oracle listener]({{site.baseurl}}/assets/images/oracle-listener.png)

When a client request comes, the listener first receives it. And then the listener establishes a connection between the client and the database instance.

Once the client is connected to the database instance successfully, the listener hands over the client connection to the server process.

If the listener stops running, you cannot connect to the Oracle Database anymore. However, all the existing connections will not be affected.

### Listener commands

* `lsnrctl services` - checks the listener services that are running
* `lsnrctl stat` - prints the status of the listener
* `lsnrctl` - Starts the LSNRCTL interactive command line, more details can be found with `help`

## listerer.ora & tnsnames.ora

These files are used by the **listener** to determine what DB services to expose and the details of the host and port.

They are typically located in the `$ORACLE_HOME/network/admin/` folder.

    Windows sometimes does not set the ORACLE_HOME variable, in this case have a look in the path variable to see if there is an entry there

Take note that these are files are automatically generated as a part of the Oracle XE installation process and may need to be updated to change the `HOST` variable to something else (often to `HOST = localhost`).
