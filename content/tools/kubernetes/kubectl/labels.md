---
title: Labels
tags:
 - kubernetes
 - kubectl
 - labels
---

This page covers the use of labels with kubectl.
<!--more-->

## Viewing labels

You can use the `--show-labels` to return the labels that are present on a Kubernetes objects. 
For example:

```shell
# Pods
kubectl get pods --show-labels
# Nodes
kubectl get nodes --show-labels
```

## Adding a Label

```shell
kubectl label nodes my-node-1 nas.volume/mediawiki=true
```

## Removing a label

You can remove a label by adding a `-` character after the name of the label you want to remove.

For example:

```shell
kubectl label node my-node-1 nas.volume/mediawiki-
```

