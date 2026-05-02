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

## Copy a file/directory (`cp`)

Use the `cp` command to copy files and directories.

Summary of Useful Options:

| Option | Description                                                      |
|--------|------------------------------------------------------------------| 
| `-i`   | Interactive: Prompts for confirmation before overwriting.        |
| `-u`   | Update: Only copies if the source is newer than the destination. |
| `-v`   | Verbose: Displays the name of each file as it is copied.         | 
| `-p`   | Preserve: Keeps file attributes like timestamps and permissions. | 

### Copy specific file

To copy a specific file in a directory using Bash, use the `cp` command followed by the source path and the destination path.
The general syntax is: `cp [options] source_file destination`

To copy to a different folder: 
```shell
cp file.txt /path/to/destination/
```

To copy and rename: 
```shell
cp file.txt /path/to/destination/new_name.txt
```

### Copy ALL files

To copy all _visible_ files from a source directory to a destination, use the wildcard operator:
```shell
cp /path/to/source/* /path/to/destination/
```

To copy all files and subdirectories recursively, use the `-r` flag:
```shell
cp -r /path/to/source/* /path/to/destination/
```

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

## Get the directory from a path (`basename`)

```shell
FULL_PATH=/tmp/my-dir
LAST_DIR=$(basename $FULL_PATH)
echo $LAST_DIR
```
```text
my-dir
```
