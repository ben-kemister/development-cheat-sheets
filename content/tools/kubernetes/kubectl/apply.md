---
title: Apply
tags:
 - kubernetes
 - kubectl
 - apply
 - resources
---

[Apply](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_apply/) a configuration to a Kubernetes resource by file name or stdin.
<!--more-->

## Apply a directory of manifests in order

You can use `kubectl apply -f dir` to apply all the files within the given directory.

You can control the order that the files are applied by using a number prefix on the filename as per [this comment](https://github.com/kubernetes/kubernetes/issues/16448#issuecomment-454218437).
For example:

```text
0_namespace.yaml
1_secret.yaml
2_service.yaml
3_deployment.yaml
4_ingress.yaml
```

## Links & References

* [kubectl apply - Kubernetes Documentation](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_apply/)