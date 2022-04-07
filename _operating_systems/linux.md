---
title: Linux
layout: page_with_tags
tags:
    - operating_system
    - linux
---

The Linux Debian operating system is used as the base for many other linux operating systems such as Ubuntu and the Raspberry Pi OS (formerly Raspbian).
<!--more-->

## What version of Linux am I running?

To find out what OS version you are running use the following command:

``` sh
cat /etc/os-release
```

## Change the hostname

For more information on changing the hostname see [this webpage](https://phoenixnap.com/kb/linux-hostname-command).

### Temporary change

In the terminal, type the following, replacing *new-hostname* with the name you choose:

```sh
sudo hostname new-hostname
```

### Permanent change

Use `set-hostname` to Change the Hostname, type the following command:

```sh
hostnamectl set-hostname new-hostname
```
Use your own hostname choice instead of *new-hostname*.

You can confirm the change using `hostnamectl` and checking the output.

## cgroups

[cgroups](https://www.redhat.com/sysadmin/cgroups-part-one) is a Linux kernel feature that limits, accounts for, and isolates the resource usage of a collection of processes. Engineers at Google started the work on this feature in 2006 under the name "process containers"
