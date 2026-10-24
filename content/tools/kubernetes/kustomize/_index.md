---
title: "Kustomize"
tags:
- kubernetes
- kubectl
- kustomize
---

[Kustomize](https://kustomize.io/) is a standalone tool to customize Kubernetes objects through a kustomization file.
<!--more-->
Since 1.14, Kubectl also supports the management of Kubernetes objects using a kustomization file. 

## Topic Specific Child Pages

{{% children sort="title" description="true" %}}

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

## Images

You can change the images, registries, or tags of the images by using an `images` section, for example:

```yaml
#
# Description: This is a Kustomise file for applying changes to the default Argo CD manifests.
#
# To view the results use:
#   kubectl kustomize ./argocd/ > ./argocd/test.yaml
#
# To apply run:
#   kubectl apply -k ./argocd/
#
---
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: argocd

resources:
  - https://raw.githubusercontent.com/argoproj/argo-cd/v2.10.6/manifests/install.yaml

images:
  # Use an image (without changing the tag) from a private image registry
  - name: quay.io/argoproj/argocd
    newName: my-private-registry.example.org/argoproj/argocd
  # Use a different tag/version of an image
  - name: redis
    newTag: 7.0.11-alpine # originally 7.0.14-alpine
```

## Name Prefix

If you want every resource in your `kustomization.yaml` (including those inherited from a base directory) to share the 
same prefix, use the `namePrefix` field. 
Kustomize will automatically update the resource names and any references to them (like a Deployment pointing to a Service or ConfigMap).

Add `namePrefix` directly to your `kustomization.yaml` file:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# This adds "dev-" to the beginning of all resource names
namePrefix: dev-

resources:
- deployment.yaml
- service.yaml
```

## Labels

Kustomize can add labels (and selectors) to the resources by using the `labels` block in you `kustomization.yaml` file.

For example:
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namePrefix: add-to-watch-

# Labels to add to all objects but not selectors
labels:
    # Pairs contains the key-value pairs for labels to add
  - pairs:
      app.kubernetes.io/name: add-to-watch-queue
    # IncludeSelectors indicates should transformer include the fieldSpecs for selectors
    includeSelectors: true

resources:
  - deployment.yaml
```

Will result in the following: 
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app.kubernetes.io/name: add-to-watch-queue # Added by Kustomize
  name: add-to-watch-queue-processor
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: add-to-watch-queue # Added by Kustomize
  ...
```

For more information see [Setting cross-cutting fields](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/#setting-cross-cutting-fields)


## Overriding Namespace behavior

When you set a value the `namespace` block of the `kustimization.yaml` file it will update the namespace values for all 
the resources it references.

Since kustomize 4.5.6 you can override this default behavior (so that some resources keep the namespace value they have 
set) by configuring the `unsetOnly` field of the NamespaceTransformer.

The example below should set the namespace to `dev` for all resources that **DO NOT** have a namespace set:
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
    - local-path-storage.yaml

transformers:
    - |-
        apiVersion: builtin
        kind: NamespaceTransformer
        metadata:
          name: notImportantHere
          # Set the desired default namespace for resources without one
          namespace: dev
        # This is the key: only apply the namespace if it is not already set
        unsetOnly: true
```

For more information see the link to the [Kustomize namespaceTransformer Spec](https://kubectl.docs.kubernetes.io/references/kustomize/builtins/#_namespacetransformer_)
