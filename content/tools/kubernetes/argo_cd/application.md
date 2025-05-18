---
title: "Application"
tags:
- ci_cd
- argo_cd
- application
---

An [Argo CD](https://argo-cd.readthedocs.io/en/stable/) Application is a Custom Resource Definition (CRD) which describes
how and where an application should be deployed to Kubernetes.
<!--more-->
All the fields available in this CRD can be found in the [Application Specification](https://argo-cd.readthedocs.io/en/stable/user-guide/application-specification/).

## The 'Application' manifest

Below is a **basic** Argo CD application manifest for the deployment of the _guestbook_ app.

```yaml
#
# Description: A basic Argo CD application manifest to assist with testing.
#
---
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: test
  namespace: argocd
spec:
  destination:
    # The namespace to deploy the app to
    namespace: default
    server: 'https://kubernetes.default.svc'
  source:
    repoURL: 'https://github.com/argoproj/argocd-example-apps.git'
    # A sub path within the repo
    path: guestbook
    targetRevision: main
  project: default
```

For more details on all the various options see the [Argo CD Application Specification page](https://argo-cd.readthedocs.io/en/stable/user-guide/application-specification/).

### Excluding/Including specific files

When using the `directory` configuration you can use the `include` and `exclude` blocks to define glob patterns of files
to include/exclude.

For example:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: test
  namespace: argocd
spec:
  source:
    repoURL: 'https://<GIT_HOST>/monitoring.git'
    targetRevision: main
    path: <SUB_FOLDER_IN_REPO> # Optional if the manifests reside in a sub-folder of the repo
    directory:
      # Include contains a glob pattern to match paths against that should be explicitly included during manifest
      # generation. If this field is set, only matching manifests will be included.
      # To match multiple patterns, wrap the patterns in {} and separate them with commas. For example: '{*.yml,*.yaml}'
      include: '{grafana-ingress.yaml,prometheus-ingress.yaml}'
...
```

## Ignore differences

You can specify particular parts of a manifest to ignore when it comes to calculating the out-of-sync resources.
This is handy if a resource is altered (by another operation) after being applied by ArgoCD.

For example:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: test
  namespace: argocd
spec:
  ignoreDifferences:
    # Do NOT include the version in the 'group'
    - group: "apiextensions.k8s.io"
      kind: "CustomResourceDefinition"
      name: "bgppeers.metallb.io"
      # Ignore the differences in the CA bundle
      jsonPointers:
        - /spec/conversion/webhook/clientConfig/caBundle
...
```


