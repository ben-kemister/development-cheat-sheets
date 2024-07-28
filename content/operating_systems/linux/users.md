---
title: "Users"
tags:
- linux
- users
---

Information about how to create and modify users or groups in linux operating systems
<!--more-->


## Create a new user (and add them to a group)

```shell
# Create user
$ sudo useradd temp_user

# Set password
$ sudo passwd temp_user

# Add the user to a group
$ sudo usermod -a -G groupname temp_user
```

## Delete a user
```shell
sudo userdel temp_user
```

## Change a users UID

```shell
# View the current UID
$ id some_user

uid=1034(some_user) gid=100(users) ...

# Change the UID
sudo usermod -u 1024 some_user
```

## Group ID

To find out the GID (Group ID) for a group name use:
```sh
getent group 124
# mysql:x:124:

getent group mysql
# mysql:x:124:
```