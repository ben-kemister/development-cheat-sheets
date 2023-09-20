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

## Liveness, Readiness and Startup Probes

The kubelet uses **liveness** probes to know when to restart a container.

> **Caution**: Liveness probes do not wait for readiness probes to succeed. 
> If you want to wait before executing a liveness probe you should use initialDelaySeconds or a startupProbe.

The kubelet uses **readiness** probes to know when a container is ready to start accepting traffic. 
A Pod is considered ready when all of its containers are ready.

The kubelet uses **startup** probes to know when a container application has started. 
If such a probe is configured, it disables liveness and readiness checks until it succeeds, 
making sure those probes don't interfere with the application startup. 
This can be used to adopt liveness checks on slow starting containers, 
avoiding them getting killed by the kubelet before they are up and running.

## Forcefully delete Resources

> **DANGER** - Forcefully deleting objects can leave resources in the cluster dangling. 
> Use with extreme caution!

Sometimes when you want to delete a particular resource in Kubernetes, it gets stuck in a Terminating phase.
This is typically because the object's finalizer is no longer in the cluster. 
This invalid state can come as a result of using tools like Helm which creates custom resources during installation and does not edit or remove them during an uninstall.

To force delete, you take the following steps:

1. Edit the Object e.g. `kubectl edit pod pod-name` or `kubectl edit customresource/name`
2. Remove delete the custom finalizers. If the finalizer has only a "kubernetes" finalizer then you can ignore it as it will be recreated if you remove it
3. Delete the object `kubectl delete customresource/name`

For more information see:
* https://github.com/odytrice/kubernetes/blob/master/force-delete.md
