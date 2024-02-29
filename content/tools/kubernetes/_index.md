---
title: Kubernetes
tags:
 - container
 - kubernetes
---

This page and it's children contain information about Kubernetes that I have discovered, and documented.
<!--more-->

## Kubernetes Sub-Pages

{{% children sort="title" description="true" %}}

# Kubernetes overview and major components

## Control Pane components

The control pane components are started with the container runtime (i.e. Docker, CRI-O )

## Creating/Updating API Resources - `Apply` vs `Create`

`apply` is preferred as this uses a declarative approach, this means that if you run it twice you will NOT get an error.

## Namespaces

default
kube-system       Used for the Kubernetes systems
kube-public       Available publicly, for use for things like container repositories

## Services (and access)

`ClusterIP` only exposes the service(s) to the container.

`NodePort` is accessible from both within and outside the Kuberentes cluster.

## Config maps

Best practice is to inject your config map as a volume.

