---
title: Linux
tags:
    - operating_system
    - linux
---

[Linux](https://en.wikipedia.org/wiki/Linux) is a family of open-source Unix-like operating systems based on the Linux kernel, 
an operating system kernel first released on September 17, 1991, by Linus Torvalds.
<!--more-->
Linux is typically packaged as a Linux distribution, which includes the kernel and supporting system software and libraries, many of which are provided by the GNU Project.

## cgroups

[cgroups](https://www.redhat.com/sysadmin/cgroups-part-one) is a Linux kernel feature that limits, accounts for, and isolates the resource usage of a collection of processes. 
Engineers at Google started the work on this feature in 2006 under the name "process containers"

## Get a List of All Users using the `/etc/passwd` File

Local user information is stored in the `/etc/passwd` file. 
Each line in this file represents login information for one user. To open the file you can either use cat or less:
```shell
less /etc/passwd
```

## Which shell am I using?

Use the `ps -p $$` command, for example:

```shell
$ ps -p $$
```
Outputs:

```text
    PID TTY          TIME CMD
   4133 pts/1    00:00:00 bash
```

## Sub Pages & Topics

{{% children sort="title" %}}