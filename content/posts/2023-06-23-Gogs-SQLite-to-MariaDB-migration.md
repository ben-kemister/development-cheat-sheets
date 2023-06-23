---
title: "Migrating Gogs from SQLite to MariaDB"
draft: false
tags:
 - gogs
 - db
 - sqlite
 - mariadb
 - git
---

This page documents my efforts to migrate my Gogs database from SQLite to MariaDB. 
<!--more-->
I had been using the default sqlite DB for my configuration, however I started getting _database locked_ errors.
The best advice that I could find was to migrate to a production ready database like MySQL.

> I run my gogs instance on a k3s kubernetes cluster as a _StatefulSet_.
> 
> As such the instructions below run any gogs commands within the gogs container of the `gogs-0` pod.

## Backup current database 

Open a terminal and run:

```shell
#Get a terminal within the container
$ sudo k3s kubectl exec -it gogs-0 -- /bin/bash

# Switch to the `git` user (the one running the gogs container)
$ su git

# Create the directory where you want to create the backup and change to it
$ mkdir /data/gogs/data/backup
$ cd /data/gogs/data/backup

# And backup the database
$ /app/gogs/gogs backup --database-only

2023/06/22 23:39:10 [ INFO] Backup root directory: /tmp/gogs-backup-1214859535
2023/06/22 23:39:10 [ INFO] Packing backup files to: gogs-backup-20230622233910.zip
2023/06/22 23:39:11 [ INFO] Backup succeed! Archive is located at: gogs-backup-20230622233910.zip
```

## Create new Database

After you have installed & setup MySQL/MariaDB, then run the 
[mysql.sql](https://github.com/gogs/gogs/blob/main/scripts/mysql.sql) script. 
This will create a database called ``gogs``.

## Create a gogs DB user

I used **phpMyAdmin** to create the gogs database user:

1. User accounts --> Add user account
2. Fill in the user details:
    * Username: gogs
    * Hostname: Any host
    * Password: <GOGS_DB_USER_PASSWORD>
    * Database for user account: Grant all privileges to gogs

> If you are creating the user via SQL you can use something like:
> ```mysql-sql
> GRANT USAGE ON *.* TO `gogs`@`%` IDENTIFIED BY PASSWORD '<SOME_PASSWORD>';
> 
> GRANT ALL PRIVILEGES ON `gogs`.* TO `gogs`@`%`;
> ```

## Update Database Configuration

> I found that the comments embedded in the [gogs GitHub repo](https://github.com/gogs/gogs/blob/main/conf/app.ini) 
> were the best source of information on how to configure the database.

Update the `/data/gogs/conf/app.ini` config file with the database connection details:
```text
[database]
TYPE = mysql
HOST = <YOUR_MARIA_DB_SERVER>:3307
NAME     = gogs
USER     = gogs
PASSWORD = `<GOGS_DB_USER_PASSWORD>`
```

## Restore from backup

```shell
#Get a terminal within the container
$ sudo k3s kubectl exec -it gogs-0 -- /bin/bash

# Switch to the `git` user (the one running the gogs container)
$ su git

# Change to the backup dir
$ cd /data/gogs/data/backup

# And restore the database
$ /app/gogs/gogs restore --database-only --from="gogs-backup-20230622233910.zip" --config="/data/gogs/conf/app.ini" 

2023/06/23 21:16:38 [ INFO] Restore backup from: gogs-backup-20230622233910.zip
2023/06/23 21:17:10 [ INFO] Restore succeed!
```

## Done!

I restarted the Gogs kubernetes pod just to be safe, but after loggin in I found everything was as it had been.

Additionally, the ``database locked`` issue that I was encountering before no longer appears : )

## Links

* [How to backup, restore and migrate](https://web.archive.org/web/20180521083825/https://discuss.gogs.io/t/how-to-backup-restore-and-migrate/991)
* [Gogs Installation instructions](https://gogs.io/docs/installation)
* [Gogs default configuration (with handy comments)](https://github.com/gogs/gogs/blob/main/conf/app.ini)