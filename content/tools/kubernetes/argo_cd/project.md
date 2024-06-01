---
title: "Project"
tags:
- ci_cd
- argo_cd
- project
- application
---

[Argo CD](https://argo-cd.readthedocs.io/en/stable/) [Projects](https://argo-cd.readthedocs.io/en/stable/user-guide/projects/) 
provide a logical grouping of applications, which is useful when Argo CD is used by multiple teams. 
<!--more-->
Projects provide the following features:
* restrict what may be deployed (trusted Git source repositories)
* restrict where apps may be deployed to (destination clusters and namespaces)
* restrict what kinds of objects may or may not be deployed (e.g. RBAC, CRDs, DaemonSets, NetworkPolicy etc...)
* defining project roles to provide application RBAC (bound to OIDC groups and/or JWT tokens)

All the fields available in the Project CRD can be found in the [Project Specification](https://argo-cd.readthedocs.io/en/stable/operator-manual/project-specification/).

## Example Project

```yaml
#
# kubectl apply -f ./web-content-appproject.yaml
#
---
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: web-content
  namespace: argocd
  # Finalizer that ensures that project is not deleted until it is not referenced by any application
  finalizers:
    - resources-finalizer.argocd.argoproj.io
  annotations:
    # Create this project after the CRDs have been applied
    argocd.argoproj.io/sync-wave: "1"
spec:
  # Project description
  description: Web content project

  # Allow manifests to deploy from any Git repos
  sourceRepos:
    - '*'

  # Only permit applications to deploy to the 'web-content' namespace in the same cluster
  # Destination clusters can be identified by 'server', 'name', or both.
  destinations:
    - namespace: 'web-content'
      server: https://kubernetes.default.svc
      name: in-cluster

  # Deny all cluster-scoped resources from being created, except for Namespace
  clusterResourceWhitelist:
    - group: ''
      kind: Namespace
```