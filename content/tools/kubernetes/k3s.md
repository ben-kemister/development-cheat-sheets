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

## Using a private image registry

If you want to pull the images from a private image registry (rather than their defaults) you can create a configuration 
file `/etc/rancher/k3s/registries.yaml` on each of the k3s nodes.
You can then configure which image registries are redirected to your private (mirrored) registry. For example:

```yaml
mirrors:
  docker.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
  quay.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
  gcr.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
  # Replaces the deprecated k8s.gcr.io registry
  registry.k8s.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
```

This will redirect any images that use `docker.io`, `quay.io`, `gcr.io` or `registry.k8s.io` to a private image registry.

> Note: In order for the registry changes to take effect, you need to restart the K3s service on the node 
> `sudo systemctl restart k3s-agent`
 
Configuring the node to use mirrors means that you do not need to worry about changing the registry named in each manifest
or Helm chart.

For more information see the [k3s documentation](https://docs.k3s.io/installation/private-registry).

### With a fallback registry

As `containerd` will try each of the endpoint URLs one by one (using the first working one) you can add a second URL as 
a fallback which will be used in the event that the first endpoint is unavailable.

For example:

```yaml
mirrors:
  docker.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
      - "https://registry-1.docker.io"
  quay.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
      - "https://quay.io"
  gcr.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
      - "https://gcr.io"
  # Replaces the deprecated k8s.gcr.io registry
  registry.k8s.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
      - "https://registry.k8s.io"
```

## List images on node

You can use the `crictl image ls` command to list the images which are present on a k3s node, for example:

```shell
$ sudo crictl image ls
IMAGE                                                            TAG                               IMAGE ID            SIZE
docker.io/container-tools/inotify-rsync                          latest                            c6bcd87db9379       4.23MB
...
```


