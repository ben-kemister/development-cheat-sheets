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

### History

`history` will display the command line history.

## Find 

`find` search for files in a directory hierarchy

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

```sh
find -type f -name *.xml -newermt 2022-03-08
```

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

## Create a new user (and add them to a group)

```shell
# Create user
$ sudo useradd temp_user

# Set password
$ sudo passwd temp_user

# Add the user to a group
$ sudo usermod -a -G groupname temp_user
```

## Delete a user
```shell
sudo userdel temp_user
```

## Change a users UID

```shell
# View the current UID
$ id some_user

uid=1034(some_user) gid=100(users) ...

# Change the UID
sudo usermod -u 1024 some_user
```

## Group ID

To find out the GID (Group ID) for a group name use:
```sh
getent group 124
# mysql:x:124:

getent group mysql
# mysql:x:124:
```

## nohup

The nohup command executes another program specified as its argument and ignores all SIGHUP (hangup) signals. 
SIGHUP is a signal that is sent to a process when its controlling terminal is closed.

To run a command in the background using the nohup command, type:
```shell
$ nohup <command> &

nohup: ignoring input and appending output to 'nohup.out'
```
If you log out or close the terminal, the process is not terminated.

## **curl**

### POST Request
The general form of the curl command for making a POST request is as follows:
`curl -X POST [options] [URL]`

    The -X option specifies which HTTP request method will be used when communicating with the remote server.

## Exit Code

In linux a `0` exit status means the command was successful without any errors. 
A non-zero (1-255 values) exit status means command was a failure.

To return the last exit code use the command:

```shell
echo $?
```

## Flow Control

### Loops

You can create a simple single line using `while true; do foo; sleep 2; done`

### Ignoring the exit code of a command

To ignore errors, you can use the `command || true` construct. 
This construct allows you to execute a command, and if it returns a non-zero exit status, 
the command following the `||` operator (in this case, true) will be executed instead.

```shell
# Create a group, ignoring any errors if a group with the same ID already exists
addgroup -g $PGID -S backup-group || true
```