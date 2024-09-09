---
title: "mount"
tags:
- sh
- bash
- linux
- mount
- filesystem
---

Information about how to mount a local or network (shared) filesystem in linux operating systems
<!--more-->

## Syntax

The general syntax is `sudo mount <-t [TYPE]> <-o [OPTIONS]> <filesystem path> <Mount point>`

Also see:
* [A list of common mount options](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/6/html/storage_administration_guide/sect-using_the_mount_command-mounting-options#sect-Using_the_mount_Command-Mounting-Options)

## Mount a local filesystem

Create the directory on the local machine where the filesystem will be mounted, for example:
`sudo mkdir -p /var/opt/longhorn`

The use `mount` to mount the filesystem to the directory, for example to temporarily mount the Logical Volume available 
at `/dev/ubuntu-vg-2/ubuntu-lv-2` use:
```sh
sudo mount /dev/ubuntu-vg-2/ubuntu-lv-2 /var/opt/longhorn
```

(Optional) You can see the filesystem mount using `df`:
```shell
$ df -h /var/opt/longhorn
Filesystem                               Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg--2-ubuntu--lv--2  916G   28K  870G   1% /var/opt/longhorn
```

### Mount at startup

First find the UUID of the filesystem using `blkid`
```shell
$ blkid
/dev/sda1: UUID="JSkn68-aaQE-9ZTL-GR5D-xfxy-XPSR-stuEuG" TYPE="LVM2_member" PARTUUID="2b98721b-3c00-e24c-b504-530e1f6626c8"
```

To mount the filesystem at startup you need to add a line to the `/etc/fstab` file
``` sh
sudo nano /etc/fstab
```
Add the following line to the file, replacing `UUID="xxxxxx-xxxx"` with the UUID of the local filesystem:
``` txt
# <file system>    <dir>       <type>   <options>   <dump> <pass>

UUID="xxxxxx-xxxx" /var/opt/longhorn  ext4      defaults    0       0
```
Mount the filesystem by running the following command: `sudo mount -a`


/dev/mapper/ubuntu--vg--2-ubuntu--lv--2 on /var/opt/longhorn type ext4 (rw,relatime)

sda    8:0    0 931.5G  0 disk
└─sda1
8:1    0 931.5G  0 part
└─ubuntu--vg--2-ubuntu--lv--2
253:0    0 931.5G  0 lvm  /var/opt/longhorn


## Mount an NFS share

The information below came from [here](https://linuxize.com/post/how-to-mount-and-unmount-file-systems-in-linux/#mounting-nfs).

Firstly make sure that you have installed `nfs-utils` for your OS, for debian:
`sudo apt install nfs-common`

Create the directory on the local machine where the remote file system will be mounted
`sudo mkdir /media/nfs`

For a temporary mount you can just run:
```sh
sudo mount <hostname_or_ip_address>:/volume1/Virtual_Machines/docker/volumes /mnt/docker-volumes
```

To remove the mount run: `sudo umount /mnt/docker-volumes`.

### Mount at startup

To mount the remote file system at startup you need to add a line to the `/etc/fstab` file
``` sh
sudo nano /etc/fstab
```
Add the following line to the file, replacing remote.server:/dir with the NFS server IP address or hostname and the exported directory:
``` txt
# <file system>    <dir>       <type>   <options>   <dump> <pass>

remote.server:/dir /media/nfs  nfs      defaults    0       0
```
Mount the NFS share by running the following command: `sudo mount /media/nfs`


## Mount a CIFS share

* Install cifs-utils: `sudo apt-get install cifs-utils`

The syntax is as follows: `sudo mount [-o <OPTIONS>] -t cifs //<REMOTE_HOST>/shared/directory /local/directory`

For example:
```shell
sudo mount -o username=USER_ID,rw,uid=1000,gid=500,vers=2.0 -t cifs //<REMOTE_HOST>/shared/directory /local/directory
```

### Temporary Mount

* Create a directory: `mkdir /mnt/cifs`
* Run mount command with using either:
  * `sudo mount -o username=USER_ID,rw,uid=1000,gid=500,vers=2.0 -t cifs //<REMOTE_HOST>/shared/directory /mnt/cifs`
  * `sudo mount.cifs //<REMOTE_HOST>/shared/folder /mnt/cifs -o username=<user_id>,file_mode=0777,dir_mode=0777`

> You will be asked to enter the password for the user

### Mount at startup

* Create the directory: `mkdir /mnt/cifs`
* Create a file to contain the credentials: `nano ~/.smbcredentials`
    * Insert the details of the credentials:
``` sh
username=<your_username>
password=<your_password>
```
* Now edit the fstab (so that it mounts on boot): `sudo nano /etc/fstab`
    * Add the details of your mount: `//<hostname>/shared/folder /mnt/cifs cifs credentials=/home/<user>/.smbcredentials,rw,file_mode=0777,dir_mode=0777,addr=<HOST_IP_ADDRESS>,nounix,noserverino,vers=2.0 0 0`
* Mount the share: `mount -a`

Note the version `vers` may be different depending on the capabilities of the cifs server.

These instructions were based on the information [here](https://marzorati.co/how-to-mount-cifs-share-permanently-on-ubuntu/).

## Mount an ISO file

1. Create the directory to mount to: `mkdir /mnt/iso-image`
2. Mount the iso file with: `sudo mount -o loop IsoFile.iso /mnt/iso-image/`
