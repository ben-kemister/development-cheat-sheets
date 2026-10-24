---
title: "Patches"
tags:
- kustomize
- patches
---

This page provides a guide on using Kustomize **patches** to modify Kubernetes resource definitions.
<!--more-->

## Replace/update

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

## Add a single annotation to existing block

You can add a single annotation to an existing block using:

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

## Add annotations block

You can add annotations block (if it does not exist) by using the following:

```yaml
...
patches:
  - target:
      kind: Deployment
      name: argocd-redis
    patch: |-
      # Disable version-checker on the redis image
      - op: add
        path: /spec/template/metadata/annotations
        value: 
          enable.version-checker.io/redis: "false"
```

## Append to a list/array

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

## Modify an entry in a list/array

```yaml
patches:
  - target:
      kind: Ingress
    patch: |-
      # Update the hostname in the Ingress
      - op: replace
        path: /spec/rules/0/host
        value: my-app.example.com
```

## Remove a resource from the Kustomize build

If you are inheriting a base (such as a public git repository or a shared base folder) and want to drop or delete a 
specific resource from being included in the final output:

1. Create a patch file (e.g. `delete-resource.yaml`) targeting the resource:
    ```yaml
    apiVersion: v1
    kind: ConfigMap # Replace with the resource kind you want to delete
    metadata:
      name: unwanted-configmap
      namespace: default
    $patch: delete
    ```
2. Reference the patch under patches in your `kustomization.yaml`:
    ```yaml
    apiVersion: kustomize.config.k8s.io/v1beta1
    kind: Kustomization
    resources:
     - ../base
    patches:
       - path: delete-resource.yaml
    ```
