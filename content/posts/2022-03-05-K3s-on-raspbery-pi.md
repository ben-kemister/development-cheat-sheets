---
title: "K3s on Raspberry Pi"
draft: false
tags:
    - raspberry_pi
    - kubernetes
    - linux
    - k3s
    - project
    - docker
status: complete
---

A project to set up a small scale *multi* worker Kubernetes instance at home (sometimes called a 'home lab').
<!--more-->

## Why

There are two main drivers for this project:

1. Enable me to host resilient services/containers (using the hardware I have lying around)
2. Learn more about Kubernetes

## Why not Docker (or Docker Swarm)

It appears that some of more *advanced* features (such as self healing containers, security, network storage) is a little beyond the capabilities of Docker (swarm).

Kubernetes also seems to be more of an industry standard and (similar technologies) are used by most cloud providers.

Lastly for some of the more advance features to work in Docker I needed to run it as *root* which is a security concern.

## Containers Services to host

1. Artifactory (or SonarNexus)
2. CI/CD Pipeline (Jenkins)
3Personal Projects:
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

## Install/Setup an Agent for an existing cluster

The value to use for **K3S_TOKEN** is stored at `/var/lib/rancher/k3s/server/node-token` on your server node.

```sh
curl -sfL https://get.k3s.io | K3S_URL=https://<IP_ADDRESS_OF_SERVER>:6443 K3S_TOKEN=$K3S_TOKEN sh -

# For example
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.0.180:6443 K3S_TOKEN=K102c547b44<truncated>e06ce9b06442a sh -
```

## Labeling Nodes

```sh
sudo k3s kubectl label node pi-server arch=arm64
```

To view the current labels for a node use the following command:
```sh
sudo k3s kubectl label --list node <NODE_NAME>

# For example
sudo k3s kubectl label --list node ht-pc
```

## IDE/Tool access to the Cluster

Copy `/etc/rancher/k3s/k3s.yaml` on your server machine located outside the cluster as `~/.kube/config`. Then replace “localhost” with the IP or name of your K3s server. kubectl can now manage your K3s cluster.

[For more information](https://rancher.com/docs/k3s/latest/en/cluster-access/).

### Test with a 'Hello World' container/service

The steps below walk you through the (manual) creation of a simple 'hello world' pod

```sh
sudo k3s kubectl run my-httpd --image=httpd:2.4

# You can find the ip address of this running pod with:
sudo k3s kubectl get pods my-httpd -o wide
# NAME       READY   STATUS    RESTARTS   AGE     IP          NODE    NOMINATED NODE   READINESS GATES
# my-httpd   1/1     Running   0          8m27s   10.42.1.5   ht-pc   <none>           <none>

# You can see the pod in action by using curl
curl 10.42.1.5
#<html><body><h1>It works!</h1></body></html>

# And remove the pod with
sudo k3s kubectl delete pod my-httpd
```

## Enable the use of NFS persistent volumes (i.e. NAS shares)

Install ``nfs-common`` on all the k3s nodes.

```sh
sudo apt-get update && sudo apt-get install -y nfs-common
```

## K3s bash aliases

My experience is the there is a lot of use of the command line so setting up some bash aliases will make your life a lot easier and save on keystrokes for common commands.

```sh
ssh <user>@<server>

# Create edit the .bash_aliases file
nano ~/.bash_aliases

# add the following line 
alias k='sudo k3s kubectl'
# Write out the file (Ctrl + O), and exit (Ctrl + X)

# Load the aliases file, so that you don't need to log out of the ssh session.
. ~/.bash_aliases
```
