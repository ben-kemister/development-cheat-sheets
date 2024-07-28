---
title: Configuration (Linux) 
tags:
- linux
- configuration
- bash
- sh
---

This page provides information about how to configure linux.
<!--more-->

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

## Change/Set IP address

For Debian based distros:

```shell
# find the interface name to change with
$ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.167  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::e280:ee50:6b3b:efd  prefixlen 64  scopeid 0x20<link>
        ether b8:27:eb:4c:65:1f  txqueuelen 1000  (Ethernet)
        RX packets 4534  bytes 831537 (812.0 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1553  bytes 196406 (191.8 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

# change the IP address
sudo ifconfig eth0 192.168.0.5 netmask 255.255.255.0
```
https://www.howtogeek.com/118337/stupid-geek-tricks-change-your-ip-address-from-the-command-line-in-linux/

If the IP address revert, check the `/etc/dhcpcd.conf` file