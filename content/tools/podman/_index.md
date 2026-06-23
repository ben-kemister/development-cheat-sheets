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

## Configuration files

The table below shows some of the common podman configuration files.

| File                                            | Description                                            |
|-------------------------------------------------|--------------------------------------------------------|
| `/home/user/.config/containers/containers.conf` | Configures podman (i.e. default network type etc)      |
| `/home/user/.config/containers/registries.conf` | User (rootless) container image registry configuration |
| `/etc/containers/registries.conf`               | System wide container image registry configuration     |


## Topic Specific Pages

{{% children sort="title" description="true" %}}


## Other Handy Commands

### ps

`podman ps` lists the running containers on the system. Use the `--all` flag to view all the containers information.

```shell
podman ps               
```
```text
CONTAINER ID  IMAGE                             COMMAND               CREATED         STATUS         PORTS                   NAMES
562c109a4631  ghcr.io/esphome/esphome:2025.6.3  run everything-pr...  23 minutes ago  Up 23 minutes  0.0.0.0:6052->6052/tcp  elated_bhabha
```

### top <CONTAINER>

`podman top <CONTAINER>` display the running processes of a container.

```shell
podman top elated_bhabha
```
```text
USER        PID         PPID        %CPU        ELAPSED           TTY         TIME        COMMAND
root        1           0           4.826       23m49.723240324s  pts/0       1m9s        /usr/local/bin/python /usr/local/bin/esphome run some-config-file-123.yaml
```

### machine ssh

When running podman on Windows you can use the `podman machine ssh` command to interact with the underlying podman WSL 
distribution (the default name of the wsl distribution is `podman-machine-default`).

You can:
* Get an interactive shell with: `podman machine ssh`
* Run a command in the wsl distribution with: `podman machine ssh <COMMAND_TO_RUN>`, for example:
    ```powershell
    podman machine ssh mkdir -p /home/user/.config/containers
    ```