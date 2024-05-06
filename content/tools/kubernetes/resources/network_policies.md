---
title: Network Policies
tags:
  - kubernetes
  - network
  - security
  - policy
---

This page contain information about the use of Kubernetes [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/).
<!--more-->
[NetworkPolicies](https://kubernetes.io/docs/concepts/services-networking/network-policies/) are an application-centric
construct which allow you to specify how a pod is allowed to communicate with various network "entities" over the network.

## To and From selectors

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