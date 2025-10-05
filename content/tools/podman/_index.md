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

Many think of Podman to be a replacement for [Docker](../docker). But, this is not the case, as Podman is another option that provides better security and developer features. 
Podman is a cloud-native, daemonless tool that helps developers manage their Linux containers.
Podman uses a microservices approach, creating a network with many other cloud-native products, such as Buildah and Skopeo, to build and push containers. 
This makes Podman a lighter and faster application than Docker, allowing for customization and changes.

## Windows Installation

These instructions are based on the [Podman for Windows doco](https://github.com/containers/podman/blob/main/docs/tutorials/podman-for-windows.md).

1. Download the Windows installer (`.msi`/`.exe` file) from the [GitHub release page](https://github.com/containers/podman/releases).
2. Open a new Terminal and run `podman machine init`
3. Start the podman machine with `podman machine start`
4. (Optional) Verify the installation by running a simple container with ``podman run ubi8-micro date``


## Topic Specific Pages

{{% children sort="title" description="true" %}}


## Handy Commands

### podman run

```shell
podman run --rm -it `
     -p 6052:6052 `
     --network=host `
     -v ${PWD}/my-configs:/config `
     <IMAGE_REGISTRY>/<IMAGE_NAME>:<IMAGE_TAG> [COMMANDS]
```

Options:
* `--rm` - remove/delete the container when completed
* `-it` - keep in terminal foreground and display STDOUT
* `-p <HOST_PORT>:<CONTAINER_PORT>` - expose/map a port in the container to the host 
* `-v <HOST_PATH>:<CONTAINER_PATH>` - map a directory (or file) to the container
* `--network=host` - (Optional) Use the network of the host


### podman ps

`podman ps` lists the running containers on the system. Use the `--all` flag to view all the containers information.

```shell
podman ps               
```
```text
CONTAINER ID  IMAGE                             COMMAND               CREATED         STATUS         PORTS                   NAMES
562c109a4631  ghcr.io/esphome/esphome:2025.6.3  run everything-pr...  23 minutes ago  Up 23 minutes  0.0.0.0:6052->6052/tcp  elated_bhabha
```

### podman top <CONTAINER>

`podman top <CONTAINER>` display the running processes of a container.

```shell
podman top elated_bhabha
```
```text
USER        PID         PPID        %CPU        ELAPSED           TTY         TIME        COMMAND
root        1           0           4.826       23m49.723240324s  pts/0       1m9s        /usr/local/bin/python /usr/local/bin/esphome run some-config-file-123.yaml
```