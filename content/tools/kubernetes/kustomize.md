---
title: "Kustomize"
tags:
- kubernetes
- development
- kubectl
---

[Kustomize](https://kustomize.io/) is a standalone tool to customize Kubernetes objects through a kustomization file.
<!--more-->
Since 1.14, Kubectl also supports the management of Kubernetes objects using a kustomization file. 

## View

To view Resources found in a directory containing a `kustomization.yaml` file, run the following command:

```shell
kubectl kustomize <kustomization_directory>
```

## Apply

To apply those Resources, run kubectl apply with `--kustomize` or `-k` flag:

```shell
kubectl apply -k <kustomization_directory>
```

## Basic kustomization file

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: default

resources:
  - https://raw.githubusercontent.com/argoproj/argo-cd/v2.10.1/manifests/install.yaml
  # Additional resources like ingress rules, cluster and repository secrets.
  - argocd-ingress.yaml
  - argocd-ingressroute.yaml

# Patches to config maps
patches:
  - path: overlays/argocd-cmd-params-cm.yaml
```