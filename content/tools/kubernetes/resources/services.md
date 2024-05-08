---
title: Services
tags:
  - kubernetes
  - network
  - service
---

This page contain information about the use of the Kubernetes [Service](https://kubernetes.io/docs/concepts/services-networking/service/) resource.
<!--more-->

## Types

* `ClusterIP` - only exposes the service within the cluster.
* `NodePort` - is accessible from both within and outside the Kubernetes cluster. It exposes the listed port on each Node.

## DNS name

Within the same namespace you can simply use the name of the service (i.e. `my-service`),
for a service in another namespace you can use:
* `my-service.my-namespace`, or
* `my-service.my-namespace.svc.cluster.local`

For more information see: 
* [Kubernetes DNS](../dns)
* [DNS for Services and Pods - Kubernetes](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)