---
title: WSL
tags:
- windows
- linux
- wsl
- wsl2
---

This page contains information about the use of the Windows Subsystem for Linux (WSL).
<!--more-->

## Basic Commands

| Command                                 | Description                                                                                                                         | 
|-----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| `wsl --version`                         | [Check WSL version](https://learn.microsoft.com/en-us/windows/wsl/basic-commands#check-wsl-version)                                 |
| `wsl -l`                                | [List the installed distributions](https://learn.microsoft.com/en-us/windows/wsl/basic-commands#list-available-linux-distributions) |
| `wsl --set-default <Distribution Name>` | [Set default Linux distribution](https://learn.microsoft.com/en-us/windows/wsl/basic-commands#set-default-linux-distribution)       |

## Accessing host files

The host (Windows) files can be access from the `/mnt/c/` mount.

For Windows 11, the users' directory can be found at `/mnt/c/Users/<USERID>`

### Symlinks can help with tools!

You can use symlinks to share configuration between tools that are used in both Windows and (wsl) Linux.

For example the configuration for the [kubectl](../../tools/kubernetes/kubectl) tool typically resides within the users' 
`.kube` directory.

To share the configuration so that it works the same on Windows and Linux you can create a symlink on the linux system 
which points to the folder on the Windows (host).

```shell
ln -s /mnt/c/Users/<USERID>/.kube ~/.kube
```

## Mount Disc

The instructions below walk you through how to [mount a disc in wsl](https://learn.microsoft.com/en-us/windows/wsl/wsl2-mount-disk).

1. Open a PowerShell console as _Administrator_
2. To identify the `DeviceID` run:
    ```powershell
    GET-CimInstance -query "SELECT * from Win32_DiskDrive"
    ```
    ```text
    DeviceID           Caption                                   Partitions Size          Model
    --------           -------                                   ---------- ----          -----
    \\.\PHYSICALDRIVE0 Samsung SSD 980 PRO 2TB                   1          2000396321280 Samsung SSD 980 PRO 2TB
    \\.\PHYSICALDRIVE1 SKHynix_HFS512GEJ9X115N                   3          512105932800  SKHynix_HFS512GEJ9X115N
    \\.\PHYSICALDRIVE2 SanDisk X400 M.2 2280 12 SCSI Disk Device 3          128034708480  SanDisk X400 M.2 2280 12 SCSI ...
    ```
3. You can then use the DeviceID in the command:
    ```powershell
    # wsl --mount <DeviceID>
    wsl --mount \\.\PHYSICALDRIVE2
    ```
   
### Mount a LVM

There are a few differences (and additional steps)  that need to be performed in this case.

1. Open a PowerShell console as _Administrator_
2. To identify the `DeviceID` run:
    ```powershell
    GET-CimInstance -query "SELECT * from Win32_DiskDrive"
    ```
    ```text
    DeviceID           Caption                                   Partitions Size          Model
    --------           -------                                   ---------- ----          -----
    \\.\PHYSICALDRIVE2 SanDisk X400 M.2 2280 12 SCSI Disk Device 3          128034708480  SanDisk X400 M.2 2280 12 SCSI ...
    ```
3. You can then use the DeviceID in the command:
    ```powershell
    # wsl --mount <DeviceID> --bare
    wsl --mount \\.\PHYSICALDRIVE2 --bare
    ```
4. Open a wsl2 terminal (Ubuntu in my case)
5. Install lvm2
   ```shell
   sudo apt install lvm2
   ```
6. Check that you can see the Logical Volume (lv) 
   ```shell
   $ sudo lvs
    LV        VG        Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
    ubuntu-lv ubuntu-vg -wi------- <116.19g
   ```
7. Activate the Volume Group (vg) using [vgchange](https://linux.die.net/man/8/vgchange)
   ```shell
   $ sudo vgchange -ay ubuntu-vg
    1 logical volume(s) in volume group "ubuntu-vg" now active
   ```
8. Use [dmsetup](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/logical_volume_manager_administration/dmsetup) to make the lv available at `/dev/mapper/<vg-name>-<lv-name>`
   ```shell
   sudo dmsetup mknodes
   ```
   You can now see the mapping
   ```shell
   $ ls -la /dev/mapper/ubuntu--vg-ubuntu--lv
   brw-rw---- 1 root disk 252, 0 May 18 06:19 /dev/mapper/ubuntu--vg-ubuntu--lv
   ```
   and in lsblk
   ```shell
   $ lsblk /dev/sdc
   NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
   sdc                         8:32   0 119.2G  0 disk
   ├─sdc1                      8:33   0     1G  0 part
   ├─sdc2                      8:34   0     2G  0 part
   └─sdc3                      8:35   0 116.2G  0 part
     └─ubuntu--vg-ubuntu--lv 252:0    0 116.2G  0 lvm
   ```
9. Make a folder to mount the filesystem to
   ```shell
   mkdir /tmp/lvm-mount
   ```
10. Lastly, mount the lvm filesystem
   ```shell
   sudo mount /dev/mapper/ubuntu--vg-ubuntu--lv /tmp/lvm-mount
   ```
11. View and Celebrate!
   ```shell
   $ ls -la /tmp/lvm-mount/
   total 4194400
   drwxr-xr-x  20 root root       4096 Mar  6 07:51 .
   drwxrwxrwt   6 root root       4096 May 18 06:45 ..
   lrwxrwxrwx   1 root root          7 Aug 10  2023 bin -> usr/bin
   ```

## Unmount Disc

1. In an _Administrator_ PowerShell console run:
    ```powershell
    # wsl --unmount <DeviceID>
    PS > wsl --unmount \\.\PHYSICALDRIVE2
    The operation completed successfully.
    ```