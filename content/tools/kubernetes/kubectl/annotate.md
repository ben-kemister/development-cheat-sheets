---
title: annotate
tags:
 - kubernetes
 - kubectl
 - annotate
---

This page covers the use of `annotate` with `kubectl` to update the annotations on a resource.
<!--more-->

## Synopsis

Update the annotations on one or more resources.

An annotation is a key/value pair that can hold larger (compared to a label), and possibly not human-readable, data. 
It is intended to store non-identifying auxiliary data, especially data manipulated by tools and system extensions. 
If `–overwrite` is true, then existing annotations can be overwritten, otherwise attempting to overwrite an annotation will result in an error. 
If `–resource-version` is specified, then updates will use this resource version, otherwise the existing resource-version will be used.

```shell
kubectl annotate [--overwrite] (-f FILENAME | TYPE NAME) KEY_1=VAL_1 ... KEY_N=VAL_N [--resource-version=version]
```

## View a resources' annotations

You can view all the annotations on a resource with `kubectl get <resource-type> <resource-name> -o=jsonpath='{.metadata.annotations}'`
For example:
```shell
# View the annotations for pod 'my=pod'
kubectl get pod my-pod -o=jsonpath='{.metadata.annotations}'
```

## Update an existing annotation

```shell
# Update pod 'foo' with the annotation 'description' and the value 'my frontend running nginx', overwriting any existing value.
kubectl annotate --overwrite pods foo description='my frontend running nginx'
```

