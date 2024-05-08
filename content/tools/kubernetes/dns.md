---
title: DNS
tags:
  - kubernetes
  - network
  - service
  - dns
---

This page contain information about DNS operations within Kubernetes.
<!--more-->

## Pod DNS

The Kubernetes Kubelet configures each Pod's `/etc/resolv.conf` file.

The contents of this file typically look like:

```text
nameserver 10.32.0.10
search <namespace>.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

To learn more about how this file works with DNS queries, see the [resolv.conf manual page](https://www.man7.org/linux/man-pages/man5/resolv.conf.5.html).

### Service Resolution

* A Pod in the **same** namespace as a Service can simply use the Service's name
* A Pod in a different namespace to a Service can use `<service-name>.<namespace>` or `<service-name>.<namespace>.svc.cluster.local`

For more information see:
* [Services](./resources/services)
* [DNS for Services and Pods](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)


