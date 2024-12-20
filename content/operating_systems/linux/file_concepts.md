---
title: Files
tags:
 - sh
 - bash
 - linux
 - file
 - permissions
---

File and file permissions are core to the security model used by Linux systems.
<!--more-->
They determine who can access files and directories on a system and how.

If you are looking for commands/tools that can be used on files see [File Commands](../../languages/sh/file_commands) 

## What is a file?

On a UNIX system, everything is a file; if something is not a file, it is a process.

## Links

### Sym(bolic) link

A symbolic (or soft) link is similar to a Shortcut in the Windows operating system. It is pointer to another file on
the system (noting that it does not reference the actual data on disc like a hard link does).

To create a symlink use the syntax `ln -s <FILE_TO_POINT_TO> <LINK_PATH>`

To remove a symlink use `unlink <PATH_TO_SYMLINK`

> You can also remove a symlink using the `rm` command. But be careful not to delete the actual file by accident!

### Hard link

The syntax to create a hard link is `ln {source} {link}`.

For example:

```shell
ln /path/to/source /path/to/link
ln target link
ln target directory
```
To verify soft or hard links on Linux, run: `ls -l source link`

## File Permissions

### Changing file permissions (chmod)

To change the permissions for a file/folder use `chmod [permission] [file_name]`, for example:
```shell
chmod 0777 public_file
```

To change the permission recursively add the `-R` or `--recursive` arguments, for example:
```shell
chmod -R 755 /path/to/directory
```

### Explaining file permissions

Every file and folder contains 8-bit data that controls the permissions. In its basic binary form, 000 means that no permissions of any form are granted.

When you set a “Read” permission, it adds 4-bit to the data, making it “100” (in binary format) or a “4” in the usual decimal format. Setting a “Write” permission will add 2-bit to the data, making it “010” and “2” in decimal form. Lastly, setting an “Execute” permission adds 1-bit to the data, which will result in “001,” or “1” in decimal form. In short:

* Read is equivalent to “4.”
* Write is equivalent to “2.”
* Execute is equivalent to “1.”*

In a nutshell, setting permissions is basic math. For example, to set “Read and Write” permissions, we combine 4 and 2 to get 6. Of course, there are other permutations:

* 0: No permission
* 1: Execute
* 2: Write
* 3: Write and Execute
* 4: Read
* 5: Read and Execute
* 6: Read and Write
* 7: Read, Write, and Execute
* A complete set of file permissions assigns the first digit to the Owner, the second digit to the Group, and the third to Others. Here are some of the commonly used permissions:

    - `755` This set of permissions is commonly used by web servers. The owner has all the permissions to read, write and execute. Everyone else can read and execute but cannot make changes to the file.
    - `644` Only the owner can read and write. Everyone else can only read. No one can execute this file.
    - `655` Only the owner can read and write and cannot execute the file. Everyone else can read and execute and cannot modify the file.
      As for 777, this means every user can Read, Write, and Execute. Because it grants full permissions, it should be used with care. However, in some cases, you’ll need to set the 777 permissions before you can upload any file to the server.

For more [information on 0777](https://www.maketecheasier.com/file-permissions-what-does-chmod-777-means/)


