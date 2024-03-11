---
title: dig
tags:
 - unix
 - linux
 - dns
---

The [dig](https://linux.die.net/man/1/dig) command in Linux is used to gather DNS information. 
<!--more-->
It stands for Domain Information Groper (DIG), and it collects data about Domain Name Servers (DNS). 
The dig command is helpful for troubleshooting DNS problems, but is also used to display DNS information.

Dig replaces older tools such as `nslookup` and [host](./host).

## Installation

Verify that it’s installed by checking the software version. To do so, open a command line and enter the following:

```shell
dig -v
```

If the system can’t find the command specified, install dig by entering the following:

### Debian / Ubuntu:

```shell
sudo apt-get install dnsutils
```

### CentOS / RedHat:

```shell
sudo yum install bind-utils
```

## Usage

The `dig` command is used as follows: `dig [server] [name] [type]`

By default, `dig` will query the name servers listed in `/etc/resolv.conf` to perform a DNS lookup. 

### Target a name sever

We can target a specific name server by using `@` symbol followed by a hostname or IP address of the name server. 
For example:

```shell
dig @10.42.1.215 google.com
```

### Use a specific port

If you need to target a specific port you can use the `-p <PORT>` argument. For example:

```shell
dig @192.168.0.13 -p 32357 google.com
```





