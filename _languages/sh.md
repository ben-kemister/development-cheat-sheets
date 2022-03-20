---
name: "sh - Shell Command Language"
language: sh
tags:
  - sh
  - bash
---

**sh** (or the Shell Command Language) is a programming language described by the POSIX standard. 
It has many implementations (ksh88, dash, ...). bash can also be considered an implementation of sh.
<!--more-->

Because sh is a specification, not an implementation, `/bin/sh` is a symlink (or a hard link) to an actual implementation on most POSIX systems.

# What is *bash*
bash started as an sh-compatible implementation (although it predates the POSIX standard by a few years), but as time passed it has acquired many extensions. 
Many of these extensions may change the behavior of valid POSIX shell scripts, so by itself bash is not a valid POSIX shell. 
Rather, it is a dialect of the POSIX shell language.

# Handy Commands

## `find`

Search for files in a directory hierarchy

### `-newerXY <reference>`
          Compares the timestamp of the current file with reference.   The
          reference  argument  is  normally the name of a file (and one of
          its timestamps is used for the comparison) but it may also be  a
          string  describing  an  absolute time.  X and Y are placeholders
          for other letters, and these letters select which time belonging
          to how reference is used for the comparison.

          a   The access time of the file reference
          B   The birth time of the file reference
          c   The inode status change time of reference
          m   The modification time of the file reference
          t   reference is interpreted directly as a time

#### Example

Find all of the xml files in that have a modification time newer than 2022-03-08

``` sh
find -type f -name *.xml -newermt 2022-03-08
```

## `mount` an NFS share

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
