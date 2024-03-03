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

To view the Resources generated from a directory containing a `kustomization.yaml` file, run the `kubectl kustomize <kustomization_directory>`
command, for example:

```shell
kubectl kustomize ./k3s-monitoring/
```
Alternatively you can use `kubectl` with the `--dry-run` flag like:

```shell
kubectl apply --dry-run=client -k ./k3s-monitoring/
```

## Apply

To apply those Resources, run kubectl apply with `--kustomize` or `-k` flag:

```shell
kubectl apply -k <kustomization_directory>
```

## Basic kustomization.yaml file

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

## Use resources from another git repo

```yaml
---
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# Use the default namespace specified in the resources

# Resources like ingress rules, cluster and repository secrets.
resources:
  #  The kustomization.yaml file lives in the root of the kube-prometheus git repo
  - github.com/prometheus-operator/kube-prometheus?ref=v0.13.0
```

## Patches

### Replace/update

```yaml
...
patches:
  # Reduce the Alertmanager replicas to save on system resources
  - target:
      kind: Alertmanager
      name: main
    patch: |-
      - op: replace
        path: /spec/replicas
        value: 1
```

### Add annotation

```yaml
...
patches:
  - target:
      kind: Namespace
    patch: |-
      - op: add
        path: /metadata/annotations/argocd.argoproj.io~1sync-wave
        value: -5
```

The result will be:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  annotations:
    argocd.argoproj.io/sync-wave: "-5"
  name: monitoring
```

### Append to a list/array

Note the use of the `-` characters at the path, this will append to the `/spec/ingress` list/array.

```yaml
...
patches:
  # Patch the grafana network policy so that we can get to the UI
  - target:
      kind: NetworkPolicy
      name: grafana
    patch: |-
      - op: add
        path: /spec/ingress/-
        value:
          from:
            - namespaceSelector:
                matchLabels:
                  # The Traefik ingress controller resides in another namespace
                  kubernetes.io/metadata.name: kube-system
          ports:
            - port: 3000
              protocol: TCP
```