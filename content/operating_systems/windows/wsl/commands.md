---
title: commands
tags:
- windows
- wsl
- commands
---

This page contains information about how to run (bash) commands in WSL.
<!--more-->

## Default distribution

You can run a linux/bash command using (the default) WSL distribution with:

```shell
wsl bash -c "ls -la"
total 2344360
drwxrwxrwx 1 user user       4096 Mar 19 08:23  .
dr-xr-xr-x 1 user user       4096 Dec  8 09:03  ..
```

## Using a specific distribution

To run a command with a specific WSL distribution include the `--distribution <DISTRIBUTION_NAME>` (or `-D <DISTRIBUTION_NAME>`) argument.

For example:

```shell
wsl --distribution podman-machine-default bash -c "ls -la"
total 2344360
drwxrwxrwx 1 user user       4096 Mar 19 08:23  .
dr-xr-xr-x 1 user user       4096 Dec  8 09:03  ..
```

## As a specific user

To run a command as a specific user (within the distribution) include the `--user <UID>` (or `-u <UID>`) argument.

For example:

```shell
wsl --distribution podman-machine-default -u root bash -c "ls -la"
total 2344360
drwxrwxrwx 1 user user       4096 Mar 19 08:23  .
dr-xr-xr-x 1 user user       4096 Dec  8 09:03  ..
```