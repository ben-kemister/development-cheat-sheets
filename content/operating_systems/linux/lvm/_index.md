---
title: "Logical Volume Manager"
tags:
- linux
- volume
- lvm
- mount
- physical_volume
---

In Linux, Logical Volume Manager (LVM) is a device mapper framework that provides logical volume management for the Linux kernel.
<!--more-->
Most modern Linux distributions are LVM-aware to the point of being able to have their root file systems on a logical volume.

## Overview

Traditional storage capacity is based on individual disk capacity. LVM uses a different concept. Storage space is managed 
by combining or pooling the capacity of the available drives.

Below is one of the better diagrams I have found which shows how Logical Volumes get exposed from the hard drive up to the 
file system.

![Logical Volume Manager Diagram](lvm.png)
[Image source](https://blog.victormendonca.com/2020/11/18/linux-logical-volume-manager/)

## Physical Volumes (PV)

Physical Volumes (PV) are disks or partitions that are available to LVM as potential storage capacity. 
They have identifiers and metadata that describes each PV.

### Create a PV

```shell
# Create a PV using the total capacity of the storage disc `c`
pvcreate /dev/sdc
```

```shell
# Creates a PV using partition 1 on the storage disc 'b'
pvcreate /dev/sdb1
```

### View PV information

You can use the `pvdisplay` or the `pvs` to view information about a PV

```shell
$ sudo pvs
  PV         VG          Fmt  Attr PSize    PFree
  /dev/sda1  ubuntu-vg-2 lvm2 a--  <931.51g    0
  /dev/sdb3  ubuntu-vg   lvm2 a--  <116.19g    0
```

```shell
$ sudo pvdisplay
  --- Physical volume ---
  PV Name               /dev/sdb3
  VG Name               ubuntu-vg
  PV Size               116.19 GiB / not usable 3.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              29744
  Free PE               0
  Allocated PE          29744
  PV UUID               vgrQ8Z-O6Od-b9VI-Eycu-IqGw-KSkN-aEuvCs

  --- Physical volume ---
  PV Name               /dev/sda1
  VG Name               ubuntu-vg-2
  PV Size               931.51 GiB / not usable 4.69 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              238466
  Free PE               0
  Allocated PE          238466
  PV UUID               318Q1K-DiKm-AfAS-THPC-AMf2-U7C9-FwIugu
```

### Remove a PV

If a device is no longer required for use by LVM, you can remove the LVM label with the `pvremove` command.
Executing the `pvremove` command zeroes the LVM metadata on an empty physical volume.
```shell
$ sudo pvremove /dev/sda1
  Labels on physical volume "/dev/sda1" successfully wiped.
```

If the physical volume you want to remove is currently part of a volume group, you must remove it from the volume group with the `vgreduce` command.

The following command removes the physical volume `/dev/hda1` from the volume group `my_volume_group`.
```shell
vgreduce my_volume_group /dev/hda1
```

## Volume Group (VG)

### View VG

```shell
$ sudo vgs
  VG          #PV #LV #SN Attr   VSize    VFree
  ubuntu-vg     1   1   0 wz--n- <116.19g       0
  ubuntu-vg-2   1   0   0 wz--n- <931.51g <931.51g
```

### Create a VG

Use the `vgcreate` command to create a new Volume Group. The VG must have at least one member. The command syntax is:
`vgcreate name-of-new-VG PV-members`
Use the following command to create a Volume Group named `vg00` with `/dev/sdb1` and `/dev/sdc` as members:
```shell
vgcreate vg00 /dev/sdb1 /dev/sdc
```

#### pvcreate: Can't use /dev/sda: device is partitioned

I encountered this issue once and found the solution [here](https://unix.stackexchange.com/questions/680801/pvcreate-cant-use-dev-sda-device-is-partitioned). 

Use `wipefs` to get rid of all data on the disc:
```shell
$ sudo wipefs /dev/sda
DEVICE OFFSET       TYPE UUID LABEL
sda    0x200        gpt
sda    0xe8e0db5e00 gpt
sda    0x1fe        PMBR
```
After that I could use `vgcreate` without any issues.

### Change a VG

[vgchange](https://linux.die.net/man/8/vgchange) allows you to change the attributes of one or more volume groups. 
Its main purpose is to activate and deactivate VolumeGroupName, or all volume groups if none is specified.

To activate a volume group
```shell
$ sudo vgchange -ay ubuntu-vg
1 logical volume(s) in volume group "ubuntu-vg" now active
```

### Delete/Remove VG

To remove a Volume Group (VG) use `sudo vgremove vg_name`

```shell
$ sudo vgremove ubuntu-vg-2
  Volume group "ubuntu-vg-2" successfully removed
```

## Logical Volume

### View LV

```shell
$ sudo lvs
  LV          VG          Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  ubuntu-lv   ubuntu-vg   -wi-ao---- <116.19g
  ubuntu-lv-2 ubuntu-vg-2 -wi-a----- <931.51g
```

### Create a LV

The `lvcreate` command carves storage capacity from a VG.

The syntax for the lvcreate command is as follows `lvcreate -L size -n lvname vgname`.

Here is the command to create a 10 GB Logical Volume named `sales-lv` carved from the `vg00` Volume Group:
```shell
lvcreate -L 10G -n sales-lv vg00
```

To create a LV using **all** the available space you can use `-l 100%FREE`, for example
```shell
sudo lvcreate -n ubuntu-lv-2 -l 100%FREE ubuntu-vg-2
```

> Remember that you will need to [apply a filesystem](../mkfs) and set a [mount point](../mount) to effectively use the 
> new Logical Volume.

### Delete/Remove LV

To remove an inactive logical volume, use the `lvremove` command. 
If the logical volume is currently mounted, `unmount` the volume before removing it. 

The following command removes the logical volume `/dev/ubuntu-vg-2/ubuntu-lv-2` from the volume group `ubuntu-vg-2`. Note that in this case the logical volume has not been deactivated.
```shell
$ sudo lvremove /dev/ubuntu-vg-2/ubuntu-lv-2
Do you really want to remove and DISCARD active logical volume ubuntu-vg-2/ubuntu-lv-2? [y/n]: y
Logical volume "ubuntu-lv-2" successfully removed
```



