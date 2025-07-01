---
title: "Sync Options"
tags:
- argo_cd
- annotations
- sync
---

[Argo CD's](../) [sync options](https://argo-cd.readthedocs.io/en/stable/user-guide/sync-options/) changes
the way that Argo CD deals with the manifests.
<!--more-->

## Create Namespace

You can get Argo CD to automatically create your namespace by adding the `CreateNamespace=true` to the `syncOptions` array
of the [application](application.md) resource.

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

## Sync Options (`sync-options`)

### Replace instead of Apply

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

### No Resource Deletion

For certain resources you might want to retain them even after your application is deleted, for e.g. PVCs, Longhorn volumes.
In such situations you can stop those resources from being cleaned up during app deletion by using the following annotation:

```yaml
metadata:
  annotations:
    argocd.argoproj.io/sync-options: Delete=false
```

For more information see: [Sync Options](https://argo-cd.readthedocs.io/en/stable/user-guide/sync-options/#no-resource-deletion)

### Using multiple sync-options

Multiple Sync Options can be configured with the `argocd.argoproj.io/sync-options` annotation can be concatenated with a 
`,` in the annotation value; white-space will be trimmed.

For example:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: some-data
  annotations:
    # Prevent the PVC from being deleted by ArgoCD, and use 'Replace' instead of Apply
    argocd.argoproj.io/sync-options: Delete=true,Replace=true
spec:
...
```
