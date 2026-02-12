---
title: "DNS"
tags:
- linux
- network
- dns
---

Linux commands relating to the systems' DNS configuration.
<!--more-->

## DNS configuration

On **most** linux systems you can get information about the systems' DNS configuration in the `/etc/resolv.conf` file.
The `nameserver` lines will inform you which DNS servers are being used.

For example:
```shell
cat /etc/resolv.conf
```
```text
search <REDACTED>
nameserver 10.43.0.10
options ndots:5
```

## Add/Editing DNS Server configuration

First up this is complex!    

There are a number of systems that can update the DNS configuration, so depending on what version of Linux you are running 
and what packages/systems are installed this can be complex thing to figure out.

### Ubuntu 22.04.5 LTS (Jammy Jellyfish)

It is likely this Operating System is using `netplan` to (at least in part) configure the DNS.

You can find the configuration files for this the `/etc/netplan/` directory. 
Typically, the `/etc/netplan/00-installer-config.yaml` file.

#### Manually setting DNS servers

Edit the configuration file `sudo nano /etc/netplan/00-installer-config.yaml` and add/update the set the `nameservers.addresses`
array with the values you are after.

The example below shows that the device `enp0sXXX` will use the DNS servers `8.8.8.8` and `8.8.4.4`
```yaml
network:
  ethernets:
    enp0sXXX:
      dhcp4: true
      nameservers:
        addresses: [8.8.8.8,8.8.4.4]
  version: 2
```

To apply the changes use the command `sudo netplan apply`.

You should then see the changes reflected in `cat /etc/resolv.conf`:
```test
...
nameserver 8.8.8.8
nameserver 8.8.4.4
...
```

### Ubuntu 24.04.4 LTS (Noble Numbat)

It is likely this Operating System is using `cloud-init` to configure `netplan` to (at least in part) configure the DNS.

You can find the configuration files for this the `/etc/netplan/` directory. 
Typically, the `/etc/netplan/50-cloud-init.yaml` file.

#### Manually setting DNS servers

Firstly, to make any changes persist across an instance reboot you may need to create the file 
`/etc/cloud/cloud.cfg.d/99-disable-network-config.cfg` with the following contents:

```text
network: {config: disabled}
```

This will disable cloud-init's network configuration capabilities.

Then, edit the configuration file `sudo nano /etc/netplan/50-cloud-init.yaml` and add/update the set the `nameservers.addresses`
array with the values you are after.

The example below shows that the device `enp2sX` will use the DNS servers `8.8.8.8` and `8.8.4.4`
```yaml
network:
  ethernets:
    enp2sX:
      dhcp4: true
      nameservers:
        addresses: [8.8.8.8,8.8.4.4]
  version: 2
```

To apply the changes use the command `sudo netplan apply`.

You should then see the changes reflected for the `enp2sX` device in `resolvectl status`:
```test
...
Link 2 (enp2sX)
    Current Scopes: DNS
        Protocols: +DefaultRoute -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
        DNS Servers: 8.8.8.8 8.8.4.4
        DNS Domain: lan
...
```
