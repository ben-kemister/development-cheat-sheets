---
title: Alpine
tags:
    - operating_system
    - linux
    - alpine
---

[Alpine Linux](https://www.alpinelinux.org/) is a Linux distribution designed to be small, simple, and secure. 
<!--more-->
It uses musl, BusyBox, and OpenRC instead of the more commonly used glibc, GNU Core Utilities, and systemd.

Due to its small size it is often used as the base for Docker images.

## Adding packages

Alpine uses `apk` to install packages, but the syntax is similar to `apt` and `apt-get`.
The example below shows how to install some packages:

```shell
# Install rysnc and inotifywait
apk update; apk add inotify-tools rsync
```

### Within Docker image

If you are building a Docker image you should remove any apk caches from the image.
So a line from a `Dockerfile` might look something like:

```shell
RUN /bin/sh -c set -eux; apk update; apk add inotify-tools rsync ; rm -rf /var/cache/apk/*
```

## Users & Groups

The commands to add users and groups in Alpine linux are a little different to the _standard_ linux command used in debian 
based distributions.

### Create a Group

```shell
# Create the group, ignoring any errors if a group with the same ID already exists
addgroup -g $PGID -S backup-group || true
```

### Create a User
* [Setting up a new user](https://wiki.alpinelinux.org/wiki/Setting_up_a_new_user)

```shell
# Create the user with a given UID
adduser --uid $PUID --system backup-user 
```
    
### Add a user to a group

```shell
$ addgroup --help
BusyBox v1.29.3 (2019-01-24 07:45:07 UTC) multi-call binary.

Usage: addgroup [-g GID] [-S] [USER] GROUP

Add a group or add a user to a group

        -g GID  Group id
        -S      Create a system group
```
`
```shell
# Add a user to a group (given the Group ID)
addgroup backup-user $(getent group $PGID | awk -F ':' '{ print $1 }')
```
    
    