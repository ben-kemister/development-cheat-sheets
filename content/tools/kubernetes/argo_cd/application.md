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

## The Argo CD Application manifest

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
  source:
    repoURL: 'https://<GIT_HOST>/monitoring.git'
    targetRevision: main
    path: <SUB_FOLDER_IN_REPO> # Optional if the manifests reside in a sub-folder of the repo
    directory:
      # Include contains a glob pattern to match paths against that should be explicitly included during manifest
      # generation. If this field is set, only matching manifests will be included.
      # To match multiple patterns, wrap the patterns in {} and separate them with commas. For example: '{*.yml,*.yaml}'
      include: '{grafana-ingress.yaml,prometheus-ingress.yaml}'
```

## Sync Options

For more information see the [Argo CD Sync Options doco](https://argo-cd.readthedocs.io/en/stable/user-guide/sync-options/).

### Create Namespace

You can get Argo CD to automatically create your namespace by adding the `CreateNamespace=true` to your `syncOptions` array.

For example:

```yaml
...
    syncPolicy:
      syncOptions:
        - CreateNamespace=true
```

## Sync Waves

You can use [sync waves](https://argo-cd.readthedocs.io/en/stable/user-guide/sync-waves/#how-do-i-configure-waves),
to ensure certain resources are healthy before subsequent resources are synced.

This is handy if you need to apply/create CustomResourceDefinition (CRDs) prior to applying the resources that use them.

You specify the wave using the following annotation:
```yaml
metadata:
    annotations:
        argocd.argoproj.io/sync-wave: "5"
```

Hooks and resources are assigned to wave zero by default. The wave can be negative, so you can create a wave that runs
before all other resources.

## Replace instead of Apply

By default, Argo CD executes `kubectl apply` operation to apply the configuration stored in Git.
In some cases `kubectl apply` is not suitable. For example, resource spec (such as a large CustomResourceDefinition) might be
too big and won't fit into `kubectl.kubernetes.io/last-applied-configuration` annotation that is added by `kubectl apply`.

In such cases you might use `Replace=true` sync option.

You can do this for the whole application:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
spec:
  syncPolicy:
    syncOptions:
    - Replace=true
```

Or to a specific resource using an annotation:

```yaml
metadata:
  annotations:
    argocd.argoproj.io/sync-options: Replace=true
```

For more information on this see [Replace Resource Instead Of Applying Changes](https://argo-cd.readthedocs.io/en/stable/user-guide/sync-options/#replace-resource-instead-of-applying-changes).

## No Resource Deletion

For certain resources you might want to retain them even after your application is deleted, for e.g. PVCs, Longhorn volumes.
In such situations you can stop those resources from being cleaned up during app deletion by using the following annotation:

```yaml
metadata:
  annotations:
    argocd.argoproj.io/sync-options: Delete=false
```

For more information see: [Sync Options](https://argo-cd.readthedocs.io/en/stable/user-guide/sync-options/#no-resource-deletion)


