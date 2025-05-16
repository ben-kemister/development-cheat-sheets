---
title: iptables
tags:
- linux
- network
---

`iptables` is a command-line utility used to manage the Linux kernel firewall, Netfilter. 
<!--more-->
It allows system administrators to define rules that control network traffic based on various criteria like IP addresses, ports, and protocols.

## List the rules

To list all the rules

```shell
[sudo] iptables -L
```
```text
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

### List rules in a specific table

> Note: `filter` is the most commonly used table of the 3 (`filter`, `nat`, and `mangle`).

```shell
[sudo] iptables -t filter -L
```

## Insert a rule

Syntax: `iptables -I chain [rulenum] rule-specification [options]`

For example:

```shell
sudo iptables -I FR_FORWARD 1 -i br1 -o wg0 -j ACCEPT
```

Options:
* `--in-interface` or `-i` input name[+] - network interface name ([+] for wildcard)
* `--out-interface` or `-o` output name[+] - network interface name ([+] for wildcard)
* `--jump` or `-j` target - target for rule (may load target extension)