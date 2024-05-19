---
title: "mkfs"
tags:
- linux
- partition
- filesystem
---

[mkfs](https://linux.die.net/man/8/mkfs) is a command used to format a block storage device with a specific file system. 
<!--more-->
The command is part of Unix and Unix-like operating systems.

## Which Filesystem?

Shamelessly borrowed from [here](https://blog.purestorage.com/purely-informational/xfs-vs-ext4-which-linux-file-system-is-better/#:~:text=If%20you%20have%20large%20files,and%20work%20with%20smaller%20files.).

> If you have large files, XFS is the best choice. Because XFS can perform input and output simultaneously, users and front-end applications store and retrieve data more quickly. 
> The ext4 file system is faster when you have limited CPU bandwidth and work with smaller files. 

## Create/Apply a Filesystem

The syntax for mkfs is `mkfs.<filesystem> [partion/logical volume]` for example:

```shell
sudo mkfs.ext4 /dev/vg_01/lv01
```

```shell
$ sudo mkfs.ext4 /dev/ubuntu-vg-2/ubuntu-lv-2
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 244189184 4k blocks and 61054976 inodes
Filesystem UUID: 4bce446c-b17a-40c0-a428-ddb64e41bd14
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968,
        102400000, 214990848

Allocating group tables: done
Writing inode tables: done
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: done
```

> Remember that you will need to set a [mount point](./mount) to effectively use the new filesytem.