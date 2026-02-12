---
title: "Network"
tags:
- linux
- hardware
- network
---

Linux commands relating to the systems' network connection and configuration.
<!--more-->

## Command/Topic Specific Pages

{{% children sort="title" description="true" %}}

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


## How to Verify the Speed of the NIC

We can check the speed of our NIC module by using the `/sys` pseudo-filesystem:

```shell
cat /sys/class/net/<interface>/speed
```

The advantage of this method is that we don’t need to install any external software tools.

Before we run the command, we need to know the network interface name. Two easy ways to find it are by using the `ifconfig` command or the `ip` command. 
We can also just list the interfaces within the `/sys/class/net/` directory:

```shell
$ ls /sys/class/net
cni0  enp0s31f6  flannel.1  lo  veth15e576e3  veth409b84fd  vethc1665644  vethf896545a  wlp2s0
```

Then we can run the `cat` command on its speed file. For example, if the interface name is `enp0s31f6` execute:

```shell
$ cat /sys/class/net/enp0s31f6/speed
1000
```

