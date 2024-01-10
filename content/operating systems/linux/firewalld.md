---
title: "Firewalld"
date: 2024-01-10T12:06:49+11:00
tags:
- linux
- firewall
---

[firewalld](https://firewalld.org/) is a firewall management tool for Linux operating systems.
<!--more-->
`firewalld` superceded iptables and frequently used as the default zone-based firewall system in Linux.

## Running vs permanent

firewalld has two configurations the current **running** configuration and a **permanent** configuration.
Changes made to the running configuration are **temporary** and are lost when the system reboots, and the permanent configuration 
is used as the new running configuration.

In practice this means that we can make temporary change to the running configuration, but if we want these to persist 
we need to make these change to the permanent configuration (by using the `--permanent` flag).

## Service definitions

`firewalld` comes with a bunch of service definitions (`.xml` files) for popular services when it is installed. 
You can view these using `sudo firewall-cmd --get-services` the source files for these are located in:
`/usr/lib/firewalld/services/service.xml`.

You can view the details of a service with `sudo firewall-cmd --info-service=<SERVICE_NAME>`, for example:

```shell
sudo firewall-cmd --info-service=http
```

### Listing services

You can list the services in the configuration using:

```shell
sudo firewall-cmd --list-services
```

### Adding a Service

You can add a service to the _running_ configuration with the command:

```shell
sudo firewall-cmd --add-service=http
```

To make the change to the permanent configuration add the `--permanent` flag.

## Reloading configuration

You can reload the permanent configuration into `firewalld`'s running configuration using the command:

```shell
sudo firewall-cmd --complete-reload
```

## Custom Service definitions

You can also create your own service definitions which can be added with `firewall-cmd` and used. 

To add a custom service definition use the command `sudo firewall-cmd --new-service-from-file=<SERVICE_NAME>.xml --permanent`.
These custom service definitions will be store in `/etc/firewalld/services/<SERVICE_NAME>.xml`.

### Example

Below is an example of a custom service definition (`k3s.xml`) which opens up the ports required for running the 
[k3s](../../tools/kubernetes/k3s) distribution of Kubernetes:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<service>
    <short>k3s</short>
    <description>k3s is a lightweight Kubernetes distribution</description>
    <!-- Kubernetes API (kubectl endpoint) tcp/6443 -->
    <port protocol="tcp" port="6443"/>
    <!-- Use for HA with embedded etcd -->
    <port protocol="tcp" port="2379-2380"/>
    <!-- Flannel VXLAN -->
    <port protocol="udp" port="8472"/>
    <!-- kubelet metrics -->
    <port protocol="tcp" port="10250"/>
    <!-- http ingress -->
    <port protocol="tcp" port="80"/>
    <!-- https ingress tcp/443 -->
    <port protocol="tcp" port="443"/>
</service>
```

This could then be added to `firewalld` using:

```shell
sudo firewall-cmd --new-service-from-file=k3s.xml --permanent
```
