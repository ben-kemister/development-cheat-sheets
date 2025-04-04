---
title: WSL
date: 2025-04-04T15:58:18+11:00
tags:
- windows
- linux
- wsl
- wsl2
---

This page contains information about the use of the [Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/about) 
(WSL).
<!--more-->

## Sub Pages & Topics

{{% children sort="title" %}}

## Basic Commands

| Command                                 | Description                                                                                                                         | 
|-----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| `wsl --version`                         | [Check WSL version](https://learn.microsoft.com/en-us/windows/wsl/basic-commands#check-wsl-version)                                 |
| `wsl -l`                                | [List the installed distributions](https://learn.microsoft.com/en-us/windows/wsl/basic-commands#list-available-linux-distributions) |
| `wsl --set-default <Distribution Name>` | [Set default Linux distribution](https://learn.microsoft.com/en-us/windows/wsl/basic-commands#set-default-linux-distribution)       |

## Matching `wsl` output

Inexplicably, `wsl --list` produces UTF-16LE-encoded ("Unicode"-encoded) output.

v0.64 and above of `wsl.exe` (verify via the first line output by `wsl.exe --version`) now supports setting the `WSL_UTF8`
environment variable to 1 to make `wsl.exe` output UTF-8-encoded output, for example

```powershell
$env:WSL_UTF8 = 1
wsl --list --running | Select-String -Pattern MyDistro
```

## Linux commands

You can run a linux/bash command using WSL as follows:

```shell
wsl bash -c "ls -la"
total 2344360
drwxrwxrwx 1 user user       4096 Mar 19 08:23  .
dr-xr-xr-x 1 user user       4096 Dec  8 09:03  ..
```

## Accessing host files

The host (Windows) files can be access from the `/mnt/c/` mount.

For Windows 11, the users' directory can be found at `/mnt/c/Users/<USERID>`

### Symlinks can help with tools!

You can use symlinks to share configuration between tools that are used in both Windows and (wsl) Linux.

For example the configuration for the [kubectl](../../tools/kubernetes/kubectl) tool typically resides within the users'
`.kube` directory.

To share the configuration so that it works the same on Windows and Linux you can create a symlink on the linux system
which points to the folder on the Windows (host).

```shell
ln -s /mnt/c/Users/<USERID>/.kube ~/.kube
```
