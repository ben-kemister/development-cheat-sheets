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

## Changing IP address of the server

On teh worker nodes edit the `sudo nano /etc/systemd/system/k3s-agent.service.env` file to change the IP address of the server.