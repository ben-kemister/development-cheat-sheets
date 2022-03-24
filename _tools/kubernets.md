---
tool: kubernetes
name: Kubernetes
tags:
 - container
 - linux
 - development
--- 

TBC
<!--more-->

# Control Pane components

The control pane components are started with the container runtime (i.e. Docker, CRI-O )

## Namespaces

Kuber

default           
kube-system       Used for the Kubernetes systems
kube-public       Available publicly, for use for things like container repositories

## `Apply` vs `Create`

`apply` is preferred as this uses a declarative approach, this means that if you run it twice you will NOT get an error.

# Volumes

`nfs` is the only storage type that supports accessModes of `ReadWriteMany`.

# Services (and access)

`ClusterIP` only exposes the service(s) to the container.

`NodePort` is accessible from both within and outside the Kuberentes cluster.

# Config maps

Best practice is to inject your config map as a **volume**.