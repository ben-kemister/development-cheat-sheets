---
title: WSL2
tags:
- windows
- linux
- wsl
- wsl2
---

This page contains information about the use of the Windows Subsystem for Linux (WSL).
<!--more-->

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
