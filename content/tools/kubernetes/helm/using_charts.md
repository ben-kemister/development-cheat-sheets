---
title: Using Charts
tags:
 - kubernetes
 - helm
 - yaml
---

This page contains information about using Helm charts.
<!--more-->

## Deploy to a namespace

Add the `-n <namespace>` option to your command, for example

```shell
helm upgrade -i gogs -n dev-ops -f .\my-values.yaml ./gogs-helm-chart/gogs/
```

### Create a namespace

Add the `--create-namespace` option to your command to create the release namespace if not present.
This can be used with the `-n <namespace>` option so that the namespace is created when deploying.

## Using multiple values.yaml files

To use multiple `values.yaml` files, simply use the `--values` flag (or `-f`) with each file. 
The priority will be given to the last (right-most) file specified.

Example:

```shell
helm upgrade -i prod-dashboard -f .\prod-values.yaml -f .\base-values.yaml .\dashy\
```

