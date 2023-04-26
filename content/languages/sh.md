---
title: "sh - Shell Command Language"
tags:
  - sh
  - bash
  - linux
  - terminal
---

**sh** (or the Shell Command Language) is a programming language described by the POSIX standard. 
It has many implementations (ksh88, dash, ...). bash can also be considered an implementation of sh.
<!--more-->

Because sh is a specification, not an implementation, `/bin/sh` is a symlink (or a hard link) to an actual implementation on most POSIX systems.

## What is *bash*
bash started as an sh-compatible implementation (although it predates the POSIX standard by a few years), but as time passed it has acquired many extensions.
Many of these extensions may change the behavior of valid POSIX shell scripts, so by itself bash is not a valid POSIX shell. 
Rather, it is a dialect of the POSIX shell language.

## Command line Shortcuts (and helpers)

### Jump to a word

`Ctrl + E` - go to the end of the line  
`Ctrl + A` - go to the start of the line  
`Alt + left` - go back one word  
`Alt + right` - go right one word  
`Ctrl + W` - delete the last word  

### Search command history (reverse-i-search)

You can search the command line history by using `Ctrl + R` then entering the search term.

From this prompt `Ctrl + R` will cycle **backwards** and `Ctrl + S` (if supported) will cycle **forwards**.

## Handy Commands

### History

`history` will display the command line history.

### Find 

`find` search for files in a directory hierarchy

#### `-newerXY <reference>`
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

##### Example

Find all of the xml files in that have a modification time newer than 2022-03-08

```sh
find -type f -name *.xml -newermt 2022-03-08
```

### File permissions

To change the permissions for a file/folder use `chmod [permission] [file_name]`, for example:
```shell
chmod 0777 public_file
```

Every file and folder contains 8-bit data that controls the permissions. In its basic binary form, 000 means that no permissions of any form are granted.

When you set a “Read” permission, it adds 4-bit to the data, making it “100” (in binary format) or a “4” in the usual decimal format. Setting a “Write” permission will add 2-bit to the data, making it “010” and “2” in decimal form. Lastly, setting an “Execute” permission adds 1-bit to the data, which will result in “001,” or “1” in decimal form. In short:

* Read is equivalent to “4.”
* Write is equivalent to “2.”
* Execute is equivalent to “1.”*

In a nutshell, setting permissions is basic math. For example, to set “Read and Write” permissions, we combine 4 and 2 to get 6. Of course, there are other permutations:

* 0: No permission
* 1: Execute
* 2: Write
* 3: Write and Execute
* 4: Read
* 5: Read and Execute
* 6: Read and Write
* 7: Read, Write, and Execute
* A complete set of file permissions assigns the first digit to the Owner, the second digit to the Group, and the third to Others. Here are some of the commonly used permissions:

  - `755` This set of permissions is commonly used by web servers. The owner has all the permissions to read, write and execute. Everyone else can read and execute but cannot make changes to the file.
  - `644` Only the owner can read and write. Everyone else can only read. No one can execute this file.
  - `655` Only the owner can read and write and cannot execute the file. Everyone else can read and execute and cannot modify the file.
  As for 777, this means every user can Read, Write, and Execute. Because it grants full permissions, it should be used with care. However, in some cases, you’ll need to set the 777 permissions before you can upload any file to the server.

For more [information on 0777](https://www.maketecheasier.com/file-permissions-what-does-chmod-777-means/)

### Mount an NFS share

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

#### Mount at startup

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


### Mount a CIFS share

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

These instructions were baes on the information [here](https://marzorati.co/how-to-mount-cifs-share-permanently-on-ubuntu/).

### **curl**

#### POST Request
The general form of the curl command for making a POST request is as follows:
`curl -X POST [options] [URL]`

    The -X option specifies which HTTP request method will be used when communicating with the remote server.