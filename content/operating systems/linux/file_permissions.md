---
title: Linux file permissions
tags:
 - sh
 - bash
 - linux
 - file
 - permissions
---

File permissions are core to the security model used by Linux systems.
<!--more-->
They determine who can access files and directories on a system and how.

## Changing file permissions

To change the permissions for a file/folder use `chmod [permission] [file_name]`, for example:
```shell
chmod 0777 public_file
```

## Explaining file permissions

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


