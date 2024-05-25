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
construct which allow you to specify how a Pod is allowed to communicate with various network "entities" over the network.

## Overview

A `NetworkPolicy` is just another object in the Kubernetes API. You can create many policies for a cluster.
NetworkPolicies operate at layer 3 or 4 of OSI model (IP and port level). They are used to control the traffic in(ingress) and out(egress) of pods.
Network policies do not conflict; they are additive. The order of evaluation does not affect the policy result.

A NetworkPolicy has several main parts:
* **Target pods**: Which pods should have their ingress (incoming) network connections enforced by the policy? These pods are selected by their label.
* **Ingress rules**: Which pods can connect to the target pods? These pods are selected by their labels, or by their namespace.
* **Egress rules**: Which pods can the target pods connect to? These pods are selected by their labels, or by their namespace.

### Network Policy evaluation

1. If there is no NetworkPolicy defined for a pod, all pods in all namespaces can connect to that pod. This means that by default with no Network Policy defined for a specific pod there is an implicit **“allow all”**.
2. If a NetworkPolicy selects a pod, traffic destined to that pod will be **restricted**.
3. If traffic to Pod A is restricted and Pod B needs to connect to Pod A, there should be at least a NetworkPolicy selecting Pod A, that has an ingress rule selecting Pod B.

## NetworkPolicy Crash Course

NetworkPolicies operate at layer 3 or 4 of OSI model (IP and port level). They are used to control the traffic in(ingress) and out(egress) of pods.

* If no NetworkPolicies targets a pod, all traffic to and from the pod is allowed. In other words all traffic are allowed until a policy is applied.
* There are no deny rules in NetworkPolicies. NetworkPolicies are deny by default allow explicitly. It's the same as saying "If you're not on the list you can't get in."
* An empty selector will match everything. For example `spec.podSelector: {}` will apply the policy to **all** pods in the current namespace.
* Selectors can only select Pods that are in the **same namespace** as the NetworkPolicies. Eg. `spec.podSelector` of an ingress rule can only select pods in the same namespace the NetworkPolicy is deployed to.
* If a NetworkPolicies matches a pod but has a null rule, all traffic is blocked. Example of this is a "Deny all traffic policy".
* NetworkPolicy are **additive**. If multiple NetworkPolicies are selecting a pod, their union is evaluated and applied to that pod.

## Simple Ingress Examples

### Deny all traffic to a Pod

Below is a simple example showing how the Ingress to a pod (with a label of `app: web`) is restricted:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: web-deny-all
  # This policy only applies to Pods within the 'default' Namespace
  namespace: default
spec:
  podSelector:
    matchLabels:
      # Targets any pods (in the 'default' Namespace) which have this label
      app: web
  # There are no ingress rules defined, so this NetworkPolicy does not allow any traffic into the pods (i.e. the pods are isolated)
  ingress: []
```

For more details on this use case see [DENY all traffic to an application](https://github.com/ahmetb/kubernetes-network-policy-recipes/blob/master/01-deny-all-traffic-to-an-application.md).

### Allow ingress from a namespace

```yaml
# Allow ingress from kube-system namespace, deny all others
---
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: allow-kube-system-ingress
  # This policy only applies to Pods within the 'default' Namespace
  namespace: default
spec:
  # Targets ALL pods in the 'default' namespace
  podSelector: {}
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              # Use the immutable label 'kubernetes.io/metadata.name' set on the namespace by the control plane
              kubernetes.io/metadata.name: kube-system
```

### Allow ingres from select pods in another namespace

```yaml
#
# Description: Allow prometheus to scrape metrics from the longhorn-managers
#
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-prometheus-longhorn-manager-ingress
  namespace: longhorn-system
spec:
  ingress:
    - from:
      - namespaceSelector:
          matchLabels:
            # Allow from pods within the 'monitoring' namespace
            kubernetes.io/metadata.name: monitoring
        podSelector:
          matchLabels:
            # Only pods which have the label 'app.kubernetes.io/name=prometheus'
            app.kubernetes.io/name: prometheus
      ports:
        # Only allow traffic on TCP port 9500 
        - port: 9500
          protocol: TCP
  podSelector:
    matchLabels:
      # Allow ingress to the longhorn-manager pod
      app: longhorn-manager
  policyTypes:
    - Ingress
```

For information checkout the [example in Network Policy recipes](https://github.com/ahmetb/kubernetes-network-policy-recipes/blob/master/07-allow-traffic-from-some-pods-in-another-namespace.md).


## To and From selectors

* **podSelector**: Selects particular **Pods in the same namespace** as the NetworkPolicy which should be allowed as ingress sources or egress destinations.
* **namespaceSelector**: Selects **particular namespaces** for which all Pods should be allowed as ingress sources or egress destinations.

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
    # No egress restrictions <-- Check this???
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

## Links & References

* [Securing Kubernetes Cluster Networking - The Unoffical Guide to Kubernetes Network Policies](https://ahmet.im/blog/kubernetes-network-policy/)
* [Kubernetes Network Policy Recipes - GitHub](https://github.com/ahmetb/kubernetes-network-policy-recipes/tree/master?tab=readme-ov-file)
* [Network Policies - Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
