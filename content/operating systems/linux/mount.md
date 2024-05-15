---
title: "mount"
tags:
- sh
- bash
- linux
- mount
---

Information about how to mount a local or network (shared) drive in linux operating systems
<!--more-->

## Mount an NFS share

The information below came from [here](https://linuxize.com/post/how-to-mount-and-unmount-file-systems-in-linux/#mounting-nfs).

Firstly make sure that you have installed `nfs-utils` for your OS, for debian:
`sudo apt install nfs-common`

Create the directory on the local machine where the remote file system will be mounted
`sudo mkdir /media/nfs`

For a temporary mount you can just run the mount command:
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

### Temporary Mount

* Install cifs-utils: `sudo apt-get install cifs-utils`
* Create the directory: `mkdir /mnt/cifs`
* Run mount command: `sudo mount.cifs //<hostname>/shared/folder /mnt/cifs -o username=<user_id>,file_mode=0777,dir_mode=0777`
  * You will be asked to enter the password for the `user_id`

### Mount at startup

* Install cifs-utils: `sudo apt-get install cifs-utils`
* Create the directory: `mkdir /mnt/cifs`
* Create a file to contain the credenials: `nano ~/.smbcredentials`
    * Insert the details of the credientials:
``` sh
username=<your_username>
password=<your_password>
```
* Now edit the fstab (so that it mounts on boot): `sudo nano /etc/fstab`
    * Add the details of your mount: `//<hostname>/shared/folder /mnt/cifs cifs credentials=/home/<user>/.smbcredentials,rw,file_mode=0777,dir_mode=0777,addr=<HOST_IP_ADDRESS>,nounix,noserverino,vers=2.0 0 0`
* Mount the share: `mount -a`

Note the version `vers` may be different depending on the capabilities of the cifs server.

These instructions were based on the information [here](https://marzorati.co/how-to-mount-cifs-share-permanently-on-ubuntu/).