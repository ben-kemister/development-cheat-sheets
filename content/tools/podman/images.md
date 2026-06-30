---
title: image
tags:
- podman
- image
---

This page contains examples about the use of Podman's `image` commands.
<!--more-->

## Pull an image, without TLS verification

If you need to pull from an image registry that has a self-signed TLS certificate (or similar) add the `--tls-verify=false`
flag to your pull command, for example:
```shell
podman pull --tls-verify=false dev-registry.local:5000/myapp:latest
```


## Prune unused images

To safely delete only dangling and unreferenced images without deleting active one use:
```shell
podman image prune -a
```


## Force remove all images 

This will remove **all** images, including those currently attached to running or stopped containers.
```power
podman rmi --all --force
```


