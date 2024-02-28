---
title: Ubuntu
tags:
- operating_system
- linux
- debian
- raspberry_pi
- ubuntu
---

[Ubuntu](https://ubuntu.com/) is a Linux distribution based on [Debian](../debian) and composed mostly of free and open-source software. 
<!--more-->
Ubuntu is officially released in three editions: Desktop, Server, and Core for Internet of things devices and robots. 
All the editions can run on the computer alone, or in a virtual machine.

## Ubuntu on Raspberry Pi

[Install Ubuntu on a Raspberry Pi](https://ubuntu.com/download/raspberry-pi)

### USB HDD Support

Version 20.10 supports booting from SSD “out-of-the-box”.

## Extending the default LVM partition

1. Check free space on the filesystem using `df -h`
2. Check free space on the Volume Group (VG) using `vgdisplay`
3. Check free space on your Logical Volume (LV) using `lvdisplay`
4. Extend the LV using `lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv`
5. Extend the filesystem using `resize2fs /dev/mapper/ubuntu–vg-ubuntu–lv`
6. Check free disk space with `df -h`

Based on the information [here](https://packetpushers.net/blog/ubuntu-extend-your-default-lvm-space/).

## Troubleshooting

### DNS resolution fails

This appeared as a failure to download (docker) images to the k3s node running on my Ubuntu machine with an error message:
`dial tcp: lookup <FQDN_HOSTNAME>: Try again...`

On inspection the system would return a `SERVFAIL` error when running `host <FQDN_HOSTNAME>`, for example:

```shell
$ host my-host.subdomain.duckdns.org
my-host.subdomain.duckdns.org is an alias for subdomain.duckdns.org.
subdomain.duckdns.org is an alias for my-machine_name.
my-machine_name has address 192.168.0.XX
Host my-machine_name not found: 2(SERVFAIL)
```
The issue did not appear when using a specific DNS server.

It appears that ``/etc/resolv.conf`` was linked to `/run/systemd/resolve/stub-resolv.conf`.

```shell
# Show link
ls -la /etc/resolv.conf
lrwxrwxrwx 1 root root 39 Aug  9  2022 /etc/resolv.conf -> ../run/systemd/resolve/stub-resolv.conf

# Contents of /etc/resolv.conf
$ cat /etc/resolv.conf
#
# Comments removed...
#
nameserver 127.0.0.53
...
```

As per this [Ubuntu question](https://askubuntu.com/questions/1068131/ubuntu-18-04-local-domain-dns-lookup-not-working) 
`/etc/resolv.conf` should be linked to `/run/systemd/resolve/resolv.conf`. 
To fix this run:

```shell
sudo rm -f /etc/resolv.conf
sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
```