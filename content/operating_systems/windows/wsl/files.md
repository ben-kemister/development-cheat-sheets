---
title: files
tags:
- windows
- wsl
- files
---

This page contains information about how to accessing files in WSL.
<!--more-->

## Accessing WSL files

You can access the files of the (WSL) distribution at `\\wsl$\<DISTRIBUTION_NAME>\<path>`.

For example, you can copy a file into the `podman-machine-default` distribution with:
```shell
cp "local.file" \\wsl$\podman-machine-default\tmp\local.file
```

## Accessing Windows files

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

