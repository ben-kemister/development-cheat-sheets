---
title: File Operations
tags:
 - sh
 - bash
 - linux
 - file
 - directory
---

Handy sh/bash commands for working with Files and Directories on Linux systems.
<!--more-->
If you are looking for Linux file concepts see [File Concepts](../../operating_systems/linux/file_concepts)

## Renaming a file (`mv`)

Use the ``mv`` command to rename a file, for example:
```shell
mv oldname.txt newname.txt
```

## Changing file permissions (`chmod`)

To change the permissions for a file/folder use `chmod [permission] [file_name]`, for example:
```shell
chmod 0777 public_file
```

To change the permission recursively add the `-R` or `--recursive` arguments, for example:
```shell
chmod -R 755 /path/to/directory
```

## Get filename from a path (`basename`)

```shell
full_name=/tmp/file.txt
base_name=$(basename ${full_name})
echo ${base_name}
```
```text
file.txt
```

## Get the parent directory (`dirname`)

```shell
dir=/home/smith/Desktop/Test
parentdir="$(dirname "$dir")"
```
