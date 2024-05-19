---
title: "fdisk"
tags:
- linux
- disk
- partition
---

`fdisk` is a command-line utility for disk partitioning.
<!--more-->

## List partitions

Use `sudo fdisk -l [<disk>...]` to list partition table(s).

```shell
# List all partition on the system
sudo fdisk -l
```
or for a single disc
```shell
$ sudo fdisk -l /dev/sda
Disk /dev/sda: 931.51 GiB, 1000204886016 bytes, 1953525168 sectors
Disk model: ST1000LM014-1EJ1
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: 69D56044-0BD5-5844-8622-39EA05FF4F60

Device     Start        End    Sectors   Size Type
/dev/sda1   2048 1953525134 1953523087 931.5G Linux filesystem
```

## Create a partition

Select the disc were you want to create the partition with `fdisk`.
```shell
sudo fdisk /dev/sda
```

To create a partition, use the `n` command.
```shell
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
```
Then follow the prompts to select the details of the partition.

To write the changes to the disc use the `w` command.
```shell
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

## Delete a partition

Select the disc containing the partition with `fdisk`.
```shell
sudo fdisk /dev/sda
```

To delete the partition, run the `d` command in the `fdisk` command-line utility.
```shell
Command (m for help): d
Selected partition 1
Partition 1 has been deleted.
```

(Optional) You can preview your changes with the `p` command.
```shell
Command (m for help): p
Disk /dev/sda: 931.51 GiB, 1000204886016 bytes, 1953525168 sectors
Disk model: ST1000LM014-1EJ1
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: 69D56044-0BD5-5844-8622-39EA05FF4F60
```

To write the changes to the disc use the `w` command.
```shell
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

(Optional) You can verify this using `lsblk` using:
```shell
$ lsblk -f /dev/sda
NAME FSTYPE FSVER LABEL UUID FSAVAIL FSUSE% MOUNTPOINTS
sda
```
