---
title: K3s
tags:
 - container
 - linux
 - development
 - kubernetes
 - k3s
---

[K3s](https://docs.k3s.io/) - Lightweight Kubernetes. Easy to install, half the memory, all in a binary of less than 100 MB.
<!--more-->

## Cluster Access

Accessing the Cluster from Outside with kubectl
Copy `/etc/rancher/k3s/k3s.yaml` on your machine located outside the cluster as `~/.kube/config`. 
Then replace the value of the server field with the IP or name of your K3s server. kubectl can now manage your K3s cluster.

### Certificates

By default, certificates in K3s expire in 12 months.
If the certificates are expired or have fewer than 90 days remaining before they expire, the certificates are rotated when K3s is restarted.

    Note: If the certificates have expired you will not be able to access the cluster with `kubeclt`

For more see [Certificate Rotation](https://docs.k3s.io/advanced#certificate-rotation).

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

## Changing the k3s service

When installed as a service, the service file is located in: `/etc/systemd/system/k3s.service`

You can alter the `ExecStart=` entry if you need to change the startup arguments.

Just remember to reload the systemctl daemon and restart the service so that the settings are applied:

```shell
sudo systemctl daemon-reload
sudo systemctl restart k3s
# Check the status
sudo systemctl status k3s
```

## Changing IP address of the server

On the worker nodes edit the `sudo nano /etc/systemd/system/k3s-agent.service.env` file to change the IP address of the server.
