---
title: MySql
tags:
- database
- mysql
---

[MySQL](https://www.mysql.com/) is an open-source relational database management system.
<!--more-->

## Topic Specific pages

{{% children sort="title" description="true" %}}

## CLI Client

### Connecting to a server

You can connect to an existing MySQL server with the `mysql` client using the syntax `mysql -u <USER_ID> -h <DATABAS_SERVER> -p`.

For example:

```shell
bash-5.1$ mysql -u root -h guacamole-mysql-cluster-0 -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 213831
Server version: 9.0.1 MySQL Community Server - GPL

Copyright (c) 2000, 2024, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> SHOW databases;
+-------------------------------+
| Database                      |
+-------------------------------+
| guacamole_db                  |
| information_schema            |
| mysql                         |
| mysql_innodb_cluster_metadata |
| performance_schema            |
| sys                           |
+-------------------------------+
6 rows in set (0.00 sec)

mysql>
```

See [here](https://dev.mysql.com/doc/refman/8.4/en/mysql-commands.html) for a list of the mysql client commands.

> To exit from the mysql command line tool use `exit`
> ```shell
> mysql> exit
> Bye
> bash-5.1$
> ```

### Executing a Command

```shell
mysql -h <MYSQL-SERVER-HOST> -u<USER> -p<PASSWORD> -e 'SHOW databases;'

```

### Running a script

```shell
mysql -h <MYSQL-SERVER-HOST> -u<USER> -p<PASSWORD> < init/1-init.sql
```