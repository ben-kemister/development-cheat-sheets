---
title: "Project: K3s on Raspberry Pi"
tags:
    - raspberry_pi
    - kubernetes
    - linux
    - k3s
    - project
    - docker
status: incomplete
---

# Aim

To setup a small scale *multi* worker Kubernetes instance on my home network.
<!--more-->

# Why

There are two main drivers for this project:

1. Enable me to host resilient services/containers (using the hardware I have lying around)
2. Learn more about Kubernetes

## Why not Docker (or Docker Swarm)

It appears that some of more *advanced* features (such as self healing containers, security, network storage) is a little beyond the capabilities of Docker (swarm).

Kubernetes also seems to be more of an industry standard and (similar technologies) are used by most cloud providers.

Lastly for some of the more advance features to work in Docker I needed to run it as *root* which is a security concern.

## Containers Services to host

1. Artifactory (or SonarNexus)
2. Personal Projects:
   1. DS Photo service/site
   2. Others....?

# Steps

## Install OS

I am using a 8GB Raspberry Pi 4 as the host for the k3s server.

First I installed the latest version of the Raspberry Pi 64-bit OS lite using the Raspberry Pi Imager.
Using the advanced options in the imager I was able to set the hostname, password, and enable SSH as the Pi is running headless in my closet.

The Raspberry OS I installed was based on [Debian version 11](https://en.wikipedia.org/wiki/Raspberry_Pi_OS#Versions), you can check which OS version you have with the command:

``` sh
cat /etc/os-release
```

## Install K3s

Even though I wasn't using *buster* I needed to enable `cgroups` as K3S needs cgroups to start the systemd service.

cgroupscan be enabled by appending `cgroup_memory=1 cgroup_enable=memory` to `/boot/cmdline.txt`.

For more info on this see [Enabling cgroups for Raspbian Buster](https://rancher.com/docs/k3s/latest/en/advanced/#enabling-legacy-iptables-on-raspbian-buster).

The actual installation of K3s was very easy, I just ran:

``` sh
curl -sfL https://get.k3s.io | sh -
```

For more info on this see the [k3s install doco](https://rancher.com/docs/k3s/latest/en/installation/install-options/#options-for-installation-with-script).

### Test with a 'Hello World' container/service

## Enable the use of persistent volumes/data on my NAS
