---
title: build
tags:
- podman
- image
- container
- development
---

This page contains examples about the use of the podman `build` command used to create container images.
<!--more-->

## Building with Containerfile (or Dockerfile)

You can specify the `Dockerfile` to use when build with the `-f <path_to_Dockerfile>` argument, for example:

```powershell
podman build -f .\Dockerfile -t [PRIVATE_IMAGE_REGISTRY[:PORT]/]<IMAGE_NAME>[:<VERSION_TAG>] .
```

## Build arguments

You can pass build argument to the Dockerfile with the `--build-arg=<KEY>=<VALUE>` argument.   
You will need a `--build-arg=<KEY>=<VALUE>` argument for each key/value pair you are passing in.

For example:

```shell
podman build --build-arg=<KEY>=<VALUE> -f .\Dockerfile -t <IMAGE_NAME>[:<VERSION_TAG>] .
```