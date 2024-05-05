---
title: K3s
tags:
 - container
 - linux
 - kubernetes
 - k3s
---

[K3s](https://docs.k3s.io/) - Lightweight Kubernetes. Easy to install, half the memory, all in a binary of less than 100 MB.
<!--more-->

## Child Pages

{{% children sort="title" description="true" %}}

## Default directories and paths

## Directories and paths

| Name               | Path                           | Notes                                                       |
|--------------------|--------------------------------|-------------------------------------------------------------|
| Binary location    | `/usr/local/bin/`              | Contains the `k3s`, `ctr`, `crictl`, and `kubectl` binaries |
| Node configuration | `/etc/rancher/k3s/config.yaml` |                                                             |

## Cluster Access

Accessing the Cluster from Outside with kubectl
Copy `/etc/rancher/k3s/k3s.yaml` on your machine located outside the cluster as `~/.kube/config`. 
Then replace the value of the server field with the IP or name of your K3s [server](./server). kubectl can now manage your K3s cluster.

## Node Configuration

You can configure the k3s nodes using yaml file(s) located in: `/etc/rancher/k3s/config.yaml`

For more information see the [k3s configuration doco](https://docs.k3s.io/installation/configuration#configuration-file).

## k3s logs

[Where are the k3s logs](https://docs.k3s.io/faq#where-are-the-k3s-logs):
* When running under `systemd`, logs will be sent to Journald and can be viewed using `journalctl -u k3s`.
* Pod logs can be found at `/var/log/pods`.
* `containerd` logs can be found at `/var/lib/rancher/k3s/agent/containerd/containerd.log`

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

## Enable the use of NFS PV

If you want to use NFS backed Persistent Volumes (PV) you will need to install `nfs-common` on the k3s agent/node.

```sh
sudo apt-get update && sudo apt-get install -y nfs-common
```

## List images on node

You can use the `crictl image ls` command to list the images which are present on a k3s node, for example:

```shell
$ sudo crictl image ls
IMAGE                                                            TAG                               IMAGE ID            SIZE
docker.io/container-tools/inotify-rsync                          latest                            c6bcd87db9379       4.23MB
...
```



