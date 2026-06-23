---
title: files
tags:
- windows
- wsl
- files
---

This page contains information about how to access files in WSL.
<!--more-->

## Accessing WSL files

### Windows --> WSL Distribution

You can access the files of the (WSL) distribution at `\\wsl$\<DISTRIBUTION_NAME>\<path>`.

So you can copy a file from Windows into the `podman-machine-default` distribution with:
```shell
cp "local.file" \\wsl$\podman-machine-default\tmp\local.file
```

If you need to copy a file into the WSL as `root` you can use `wsl -u root -d <WSL_DISTRO_NAME> cp "/mnt/c/path/to/windows/file.txt" "/root/destination/file.txt"`
For example: 
```powershell
wsl -u root -d podman-machine-default cp "/mnt/c/some/path/local.file" "/etc/some/root/folder/local.file"
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

## Echo multiline --> WSL file

To echo multiple lines onto a file on the WSL system use:
```powershell
$text = @"
Line 1 of your text
Line 2 of your text
Line 3 of your text
"@

$text | wsl -e bash -c "cat > filename/txt"
```

> Note the `-e` in this context stands for **Execute**.