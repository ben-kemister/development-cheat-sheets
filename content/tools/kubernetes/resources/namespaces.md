---
title: Namespaces
tags:
  - kubernetes
---

This page contain information about the use of Kubernetes [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).
<!--more-->
In Kubernetes, `namespaces` provide a mechanism for isolating groups of resources within a single cluster.


## Initial namespaces

* default - Provided as default namespace with a new cluster.
* kube-system - The namespace for objects created by the Kubernetes system
* kube-public - This namespace is readable by all clients (including those not authenticated). This namespace is mostly reserved for cluster usage, in case that some resources should be visible and readable publicly throughout the whole cluster.
* kube-node-lease - This namespace holds Lease objects associated with each node. Node leases allow the kubelet to send heartbeats so that the control plane can detect node failure.

## Namespace Label(s)

The Kubernetes control plane sets an immutable label `kubernetes.io/metadata.name` on all namespaces, the value of the label is the namespace name.

Below is an example of the `default` namespace resource:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: default
  labels:
    # Immutable label set by the Kubernetes control plane.
    kubernetes.io/metadata.name: default
...
```

A namespaces' immutable `kubernetes.io/metadata.name` label is useful when targeting pods with a [NetworkPolicy](./network_policies). 

## Create a namespaces

```shell
kubectl create ns dev-ops
```

