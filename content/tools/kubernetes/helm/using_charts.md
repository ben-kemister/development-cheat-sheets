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

## Show default values.yaml

Use the syntax `helm show values <CHART_NAME>`, for example:

```shell
helm show values prometheus-community/kube-prometheus-stack > .\kube-prometheus\values.yaml
```

## Using multiple values.yaml files

To use multiple `values.yaml` files, simply use the `--values` flag (or `-f`) with each file. 
The priority will be given to the last (right-most) file specified.

Example:

```shell
helm upgrade -i prod-dashboard -f .\prod-values.yaml -f .\base-values.yaml .\dashy\
```

## Locally rendering output

You can use the `helm template --debug` command with your values to locally render the output and help check/verify the results.
For example:

```shell
helm template --debug -f .\longhorn\my-values.yaml longhorn/longhorn > longhorn\test.yaml
```

## Debugging Templates

[Debugging templates](https://helm.sh/docs/chart_template_guide/debugging/) can be tricky because the rendered templates 
are sent to the Kubernetes API server, which may reject the YAML files for reasons other than formatting.

There are a few commands that can help you debug.

* `helm lint` is your go-to tool for verifying that your chart follows best practices
* `helm template --debug` will test rendering chart templates locally.
* `helm install --dry-run --debug` will also render your chart locally without installing it, but will also check if conflicting resources are already running on the cluster. Setting `--dry-run=server` will additionally execute any lookup in your chart towards the server.
* `helm get manifest`: This is a good way to see what templates are installed on the server.


