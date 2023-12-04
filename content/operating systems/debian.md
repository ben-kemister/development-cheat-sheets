---
title: Debian
tags:
- operating_system
- linux
- debian
---

The [Linux]({{< ref "/operating systems/linux">}}) Debian operating system is used as the base for many other linux 
operating systems such as Ubuntu and the Raspberry Pi OS (formerly Raspbian).
<!--more-->

## apt

Advanced Package Tool, or APT, is a free-software user interface that works with core libraries to handle the installation 
and removal of software on Debian, and Debian-based Linux distributions.
APT simplifies the process of managing software on Unix-like computer systems by automating the retrieval, 
configuration and installation of software packages, either from precompiled files or by compiling source code.

### Remove a repo 

If you are getting an error when trying to run an update `sudo apt update` you can remove the repo as a source with the following:

1. Check your /etc/apt/sources.list and remove unwanted sources.
    `sudo nano /etc/apt/sources.list`
2. If step 1 does not solve your problem, see what other sources are used.
`sudo ls -al /etc/apt/sources.list.d/`
3. If you have found a source-list causing the problem, remove it (in the example below, NodeJS source list is removed):
`sudo rm /etc/apt/sources.list.d/nodesource.list`