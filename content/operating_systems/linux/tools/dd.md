---
title: dd
tags:
- unix
- dd
- copy
---

[dd](https://www.geeksforgeeks.org/dd-command-linux/) is a command-line utility for Unix, the primary purpose of which is to convert and copy files.
<!--more-->

## To create CDROM/DVD Backup

dd command allows you to create an iso file from a source file. So we can insert the CD/DVD and enter `dd` command to create an iso file of the disks content.

```shell
dd if=/dev/sr0 of=DVD_1_DISC_1.iso bs=2048 status=progress
```

## GNU ddrescue

[GNU `ddrescue`](https://community.linuxmint.com/software/view/gddrescue) is a newer attempt to improve upon Kurt Garloff's `dd_rescue` (and the original `dd`).

One advantage of GNU `ddrescue` is that it backs up most data faster, by first skipping bad blocks (which are slow to recover) 
and coming back to them only after good blocks have been backed up. 

One disadvantage of GNU `ddrescue` is that it doesn't support piped output, which means you can't compress the output image with `gzip` or `lzop`.

### Ubuntu packages

Unfortunately, the package names in the Ubuntu repositories are confusing; notably GNU `ddrescue` is packages under `gddrescue`! 

```shell
sudo apt install gddrescue
```
```shell
$ ddrescue --version
GNU ddrescue 1.23
Copyright (C) 2018 Antonio Diaz Diaz.
License GPLv2+: GNU GPL version 2 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
```

### Rescue a DVD/CD-ROM

Instructions based on [ddrescue documentation](https://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Optical-media)

Example 1: Rescue a DVD in `/dev/sr0`.
```shell
# First run, Skip the scraping phase. Avoids spending a lot of time trying to rescue the most difficult parts of the file.
ddrescue -n -b2048 /dev/sr0 DVD.iso mapfile

# Second run, 
ddrescue -d -r1 -b2048 /dev/sr0 DVD.iso mapfile
(if bad-sector size is zero, cdimage now contains a complete image of the CD-ROM and you can write it to a blank CD-ROM)
```
