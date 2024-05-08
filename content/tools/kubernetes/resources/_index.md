---
title: Resources
tags:
 - container
 - kubernetes
 - kubectl
 - yaml
 - manifest
---

This page contains information about Kubernetes resource (a.k.a. objects).
<!--more-->

## Kubernetes resource specific sub-pages

{{% children sort="title" description="true" %}}

## Namespaces

default
kube-system       Used for the Kubernetes systems
kube-public       Available publicly, for use for things like container repositories

## Volumes

`nfs` is the only storage type that supports accessModes of `ReadWriteMany`.

## Config maps

Best practice is to inject your config map as a volume.

## Workload Resources (Pods, Deployments, StatefulSets)

### Resource Limits

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

### Liveness, Readiness and Startup Probes

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

