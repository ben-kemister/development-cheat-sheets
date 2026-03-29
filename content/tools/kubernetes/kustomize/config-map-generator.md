---
title: "ConfigMapGenerator"
tags:
- kustomize
- ConfigMap
---

This page covers the use of kustomizes' ConfigMapGenerator.
<!--more-->

Kustomize provides two ways of adding ConfigMap in one kustomization, either by declaring ConfigMap as a resource or
declaring ConfigMap from a ConfigMapGenerator. The formats inside `kustomization.yaml` are:

```yaml
# declare ConfigMap as a resource
resources:
- configmap.yaml

# declare ConfigMap from a ConfigMapGenerator
configMapGenerator:
- name: a-configmap
  files:
  # configfile is used as key
    - configs/configfile
  # configkey is used as key
    - configkey=configs/another_configfile
```

The ConfigMaps declared as resource are treated the same way as other resources.
Kustomize doesn't append any hash to the ConfigMap name.

The ConfigMap declared from a ConfigMapGenerator is treated differently.
A hash is appended to the name and any change in the ConfigMap will trigger a rolling update.
This can be disabled if needed using:

```yaml
configMapGenerator:
- name: a-configmap
  options:
    disableNameSuffixHash: true
```