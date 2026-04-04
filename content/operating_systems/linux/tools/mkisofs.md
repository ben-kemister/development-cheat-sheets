---
title: mkisofs
tags:
- iso
- dvd
---

[mkisofs](https://linux.die.net/man/8/mkisofs) is a utility that creates an ISO 9660 image from files on disk.
<!--more-->

## Usage

```shell
mkisofs -o DVD_DISC.iso -dvd-video -V MY_DISC2 "/directory/containing/VIDEO_TS/folder/"
```

## Handy Options

* `-o FILE`, `-output FILE` - Set output file name
* `-V ID`, `-volid ID` - Set Volume ID
* `-dvd-video` - Generate DVD-Video compliant UDF file system
* `-quiet` - Run quietly

## Links & References

* [How to Create an ISO File in Linux](https://www.wikihow.com/Create-an-ISO-File-in-Linux)