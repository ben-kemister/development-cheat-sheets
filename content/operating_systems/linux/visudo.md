---
title: visudo
tags:
- operating_system
- linux
- ubuntu
- sudo
---

[visudo](https://linux.die.net/man/8/visudo) is a special tool in Unix/Linux for safely updating the /etc/sudoers file, 
used by the sudo command for providing and managing privileged access.
<!--more-->

## Opening a particular sudoers file

```shell
sudo visudo /etc/sudoers.d/my_custom
```

## How to stop prompting for password for a single command/program

Add the following line to the end of the sudoers file:

```text
userid ALL = (root) NOPASSWD: /path/to/program_1, /path/to/program_2
```

For example:
```shell
smithy ALL = (root) NOPASSWD: /usr/bin/mount, /usr/bin/umount
```

## How to Stop Linux From Asking for Your sudo Password

Launch `visudo`: 
```shell
sudo visudo
```

Add a line using the following syntax `<USERNAME> ALL=(ALL) NOPASSWD: ALL`, for example for the username 'smith':

```text
smith   ALL=(ALL) NOPASSWD: ALL
```
