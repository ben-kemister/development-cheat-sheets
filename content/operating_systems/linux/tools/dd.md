---
title: dd
tags:
- unix
- dd
- copy
- iso
---

[dd](https://www.geeksforgeeks.org/dd-command-linux/) is a command-line utility for Unix, the primary purpose of which is to convert and copy files.
<!--more-->

## To create CDROM/DVD Backup

`dd` command allows you to create an iso file from a source file. So we can insert the CD/DVD and enter `dd` command to create an iso file of the disks content.

```shell
dd if=/dev/sr0 of=DVD_1_DISC_1.iso bs=2048 status=progress
```

> Note that a better tool for restoring/backing up damaged DVDs is [ddrescure](ddrescue).


