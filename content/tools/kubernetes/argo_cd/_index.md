---
title: "Argo CD"
date: 2023-12-29T06:43:55+11:00
tags:
- kubernetes
- development
- ci_cd
- argo_cd
---

[Argo CD](https://argo-cd.readthedocs.io/en/stable/) is a declarative, GitOps continuous delivery tool for Kubernetes.
<!--more-->

## Child Pages

{{% children sort="title" description="true" %}}

## Install

```shell
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/<version>/manifests/install.yaml
```

## Declarative setup

Argo CD applications, projects and settings can be defined [declaratively](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/) 
using Kubernetes manifests.

The table below shows a commonly used subset of the [ArgoCD configuration resources](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/#quick-reference). 

| Resource Name                                                                         | Kind      | Description                    |
|---------------------------------------------------------------------------------------|-----------|--------------------------------|
| [argocd-cm](https://argo-cd.readthedocs.io/en/stable/operator-manual/argocd-cm-yaml/) | ConfigMap | 	General Argo CD configuration |

### Kustomizing Helm charts

It's possible to render [Helm charts with Kustomize in ArgoCD](https://argo-cd.readthedocs.io/en/stable/user-guide/kustomize/#kustomizing-helm-charts).
Doing so requires that you pass the `--enable-helm` flag to the kustomize build command.

In ArgoCD this is done by setting a build option in the `argocd-cm` ConfigMap:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
data:
  kustomize.buildOptions: --enable-helm
```

## Managing Argo CD with Argo CD

You can use [Kustomize](./kustomize) to apply any 'last mile' changes to the Argo CD manifests which will allow you to 
[Manage Argo CD Using Argo CD](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/#manage-argo-cd-using-argo-cd).

Below is an example `kustomization.yaml` file:

```yaml
#
# Description: This is a Kustomise file for applying site specific changes to the default Argo CD manifests.
#
# To view the results use:
#   kubectl kustomize ./
#
# To apply run:
#   kubectl apply -k ./
#
---
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: argocd

resources:
  - https://raw.githubusercontent.com/argoproj/argo-cd/v2.10.1/manifests/install.yaml
  # Additional resources like ingress rules, cluster and repository secrets.
  - argocd-ingress.yaml
  - argocd-ingressroute.yaml

# Changes to config maps
patches:
  - path: overlays/argocd-cmd-params-cm.yaml
```

