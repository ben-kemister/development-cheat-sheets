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

### Tracing DNS resolution

Use the `+trace` argument to view the steps taken in the [DNS resolution process](https://www.ibm.com/blog/using-dig-trace/),
for example:

```shell
dig +trace google.com.au
```

Resolution starts from the Top-Level Domain (TLD) end of the hostname, 
so first it looks for the root (which is represented in `dig` by the leading ``.``

```text
; <<>> DiG 9.18.25 <<>> +trace google.com.au
;; global options: +cmd
.                       4       IN      NS      a.root-servers.net.
.                       4       IN      NS      b.root-servers.net.
.                       4       IN      NS      c.root-servers.net.
.                       4       IN      NS      d.root-servers.net.
<...>
;; Received 729 bytes from 10.43.0.10#53(10.43.0.10) in 0 ms
```

Next it looks for the `au.` component:
```text
au.                     172800  IN      NS      s.au.
au.                     172800  IN      NS      r.au.
au.                     172800  IN      NS      q.au.
au.                     172800  IN      NS      t.au.
<...>
;; Received 707 bytes from 192.33.4.12#53(c.root-servers.net) in 254 ms
```

Then, in this case `google.com.au` from googles name servers:
```text
google.com.au.          3600    IN      NS      ns4.google.com.
google.com.au.          3600    IN      NS      ns3.google.com.
google.com.au.          3600    IN      NS      ns2.google.com.
<...>
;; Received 623 bytes from 65.22.198.1#53(s.au) in 8 ms
```

Finally, it receives the DNS A record (i.e. hostname --> IP address mapping) from `172.217.167.67`:
```text
google.com.au.          300     IN      A       172.217.167.67
;; Received 58 bytes from 216.239.36.10#53(ns3.google.com) in 140 ms
```

For more information on what the DNS record types are, see the [DNS page](../../../concepts/dns).

