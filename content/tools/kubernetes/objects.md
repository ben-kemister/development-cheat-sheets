---
title: Kubernetes Objects
tags:
 - container
 - kubernetes
 - kubectl
 - yaml
 - manifest
---

This page contains information about Kubernetes objects.
<!--more-->

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

## Network Policies

[NetworkPolicies](https://kubernetes.io/docs/concepts/services-networking/network-policies/) are an application-centric 
construct which allow you to specify how a pod is allowed to communicate with various network "entities" over the network.

### To and From selectors

**podSelector**: Selects particular **Pods in the same namespace** as the NetworkPolicy which should be allowed as ingress sources or egress destinations.

**namespaceSelector**: Selects **particular namespaces** for which all Pods should be allowed as ingress sources or egress destinations.

Example:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  labels:
    app.kubernetes.io/component: grafana
    app.kubernetes.io/name: grafana
    app.kubernetes.io/part-of: kube-prometheus
    app.kubernetes.io/version: 10.2.2
  name: grafana
  namespace: monitoring
spec:
  egress:
    # No egress restrictions
    - {}
  ingress:
    - from:
        # the podSelector only allows for pods within the same namespace (i.e. pods in the 'monitoring' namespace).
        - podSelector:
            matchLabels:
              app.kubernetes.io/name: prometheus
        # Added to allow ingress from pods within the `kube-system` namespace
        - namespaceSelector:
            matchLabels:
              kubernetes.io/metadata.name: kube-system
      ports:
        - port: 3000
          protocol: TCP
  podSelector:
    matchLabels:
      app.kubernetes.io/component: grafana
      app.kubernetes.io/name: grafana
      app.kubernetes.io/part-of: kube-prometheus
  policyTypes:
    - Egress
    - Ingress
```
