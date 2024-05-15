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