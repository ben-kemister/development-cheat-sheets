---
title: "Podman"
date: 2024-01-01T08:03:39+11:00
tags:
- container
- linux
- development
- docker
- podman
---

[Podman](https://github.com/containers/podman) (the POD MANager) is a tool for managing containers and images, volumes mounted into those containers, and pods made from groups of containers. 
<!--more-->
Podman runs containers on Linux, but can also be used on Mac and Windows systems using a Podman-managed virtual machine.

Many think of Podman to be a replacement for [Docker](./docker). But, this is not the case, as Podman is another option that provides better security and developer features. 
Podman is a cloud-native, daemonless tool that helps developers manage their Linux containers.
Podman uses a microservices approach, creating a network with many other cloud-native products, such as Buildah and Skopeo, to build and push containers. 
This makes Podman a lighter and faster application than Docker, allowing for customization and changes.

## Windows Installation

These instructions are based on the [Podman for Windows doco](https://github.com/containers/podman/blob/main/docs/tutorials/podman-for-windows.md).

1. Download the Windows installer (`.msi`/`.exe` file) from the [GitHub release page](https://github.com/containers/podman/releases).
2. Open a new Terminal and run `podman machine init`
3. Start the podman machine with `podman machine start`
4. (Optional) Verify the installation by running a simple container with ``podman run ubi8-micro date``
