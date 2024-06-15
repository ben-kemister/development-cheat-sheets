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

## Create a namespaces (from the command line)

```shell
kubectl create ns dev-ops
```

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

## Pod Security Admission labels for namespaces

After setting the **default** Pod Security Standards in the Built-in [Admission Controller](./admission_controller) you can 
use labels on a namespace to set the `enforce`, `audit`, and `warn` policies for a [specific namespace](https://kubernetes.io/docs/concepts/security/pod-security-admission/#pod-security-admission-labels-for-namespaces).

For each mode (`enforce`, `audit`, and `warn`) there are two labels that determine the policy used:
```yaml
# The per-mode level label indicates which policy level to apply for the mode.
#
# MODE must be one of `enforce`, `audit`, or `warn`.
# LEVEL must be one of `privileged`, `baseline`, or `restricted`.
pod-security.kubernetes.io/<MODE>: <LEVEL>

# Optional: per-mode version label that can be used to pin the policy to the
# version that shipped with a given Kubernetes minor version (for example v1.30).
#
# MODE must be one of `enforce`, `audit`, or `warn`.
# VERSION must be a valid Kubernetes minor version, or `latest`.
pod-security.kubernetes.io/<MODE>-version: <VERSION>
```

Below is an example of setting the `enforce` mode to use the `privileged` policy:
```yaml
apiVersion: v1
kind: Namespace
metadata:
  labels:
    # Set the `enforce` mode to use the `privileged` policy for this namespace
    pod-security.kubernetes.io/enforce: privileged
    pod-security.kubernetes.io/enforce-version: latest
  name: privileged-ns
```

### Applying to an existing namespace

You can set these labels on an existing namespace using the `label` command, for example:
```shell
kubectl label --overwrite ns my-existing-namespace \
  pod-security.kubernetes.io/enforce=restricted \
  pod-security.kubernetes.io/enforce-version=v1.30
```

