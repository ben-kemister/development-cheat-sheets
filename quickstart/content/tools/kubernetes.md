---
title: Kubernetes
tags:
 - container
 - linux
 - development
 - kubernetes
 - kubectl
--- 

TBC
<!--more-->

# Kubernetes overview and major components

## Control Pane components

The control pane components are started with the container runtime (i.e. Docker, CRI-O )

## Creating/Updating API Resources - `Apply` vs `Create`

`apply` is preferred as this uses a declarative approach, this means that if you run it twice you will NOT get an error.

## Namespaces

default
kube-system       Used for the Kubernetes systems
kube-public       Available publicly, for use for things like container repositories

## Volumes

`nfs` is the only storage type that supports accessModes of `ReadWriteMany`.

## Services (and access)

`ClusterIP` only exposes the service(s) to the container.

`NodePort` is accessible from both within and outside the Kuberentes cluster.

## Config maps

Best practice is to inject your config map as a volume.

## Resource Limits

You can apply resource limits to prevent containers from using all available resources on a node.

```yaml
...
      containers:
        - name: gogs
          image: gogs/gogs:0.12.6
          imagePullPolicy: IfNotPresent
          ...
          resources:
            limits:
              # Limit the memory use as it can get a little wild if left unbound
              memory: 512Mi
              cpu: 500m
```