---
title: get
tags:
 - kubernetes
 - kubectl
 - get
---

This page covers the use of `get` with `kubectl` to display one or many resources
<!--more-->

## Synopsis

Display one or many resources.

By specifying the output as ‘template’ and providing a Go template as the value of the `–template` flag, you can filter the attributes of the fetched resource(s).

```shell
kubectl get [(-o|--output=)json|yaml|wide|go-template=...|go-template-file=...|jsonpath=...|jsonpath-file=...] (TYPE [NAME | -l label] | TYPE/NAME ...) [flags]
```

## Basic examples

```shell
# List all pods in ps output format.
kubectl get pods

# List all pods in ps output format with more information (such as node name).
kubectl get pods -o wide

# List a single replication controller with specified NAME in ps output format.
kubectl get deployment web
```

## Filter on partial name

You can pipe the output of the kubectl get command through tools like grep or Select-String to filter on pod names,
for example:

In Linux Bash:

```shell
kubectl get pods | grep ^j
```
In Windows PowerShell:

```powershell
kubectl get pods | Select-String '^j'
```

## Filter by label

You can use `get <resource> -l '<label>'` to retrieve resources with matching labels.
```shell
kubectl get po -n kube-system -l 'k8s-app=kube-dns'
```

You can also combine multiple label filters like
```shell
kubectl get pods -l 'part-of=my-app, tier!=frontend'
```

## Custom columns

Using the flag `-o custom-columns=<COLUMN_NAME>:<field>` will let you customize the output.

Example with resource name, under header `NAME`
`kubectl get pods -o custom-columns=NAME:metadata.name`
output
```shell
NAME
myapp-5b77df6c48-dbvnh
myapp-64d5985fdb-mcgcn
httpd-9497b648f-9vtbl
```

You can output multiple columns using a comma `,` as a delimiter.

```shell
kubectl get nodes -o custom-columns=NAME:metadata.name,LABELS:metadata.labels
```

## Omit headers
The proper solution to omit the header line is by using the flag `--no-headers`

`kubectl get pods -o custom-columns=NAME:metadata.name --no-headers`
Example output
```shell
myapp-5b77df6c48-dbvnh
myapp-64d5985fdb-mcgcn
httpd-9497b648f-9vtbl
```

## Get <Resource> Examples

| Command                                                                                  | Description                                                                       |
|------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `kubectl get pods --show-labels`                                                         | Show labels for all pods (or any other Kubernetes object that supports labelling) |
| `kubectl get po -w -l app.kubernetes.io/instance=test-monitoring`                        | Watch a specific resource(s) based on a label                                     |
| `kubectl get po -l app.kubernetes.io/name=openhab --field-selector status.phase=Running` | Get a running pod based on a label                                                |
| `kubectl get -o custom-columns=NAME:metadata.name \| Select-String '^podname'\| foreach { kubectl delete pod $_ }` | Delete all of the pods which start with `podname` |




