---
tool: docker
name: Docker
tags:
 - container
 - linux
 - development
--- 

Docker is a set of platform as a service products that use OS-level virtualization to deliver software in packages called containers. The service has both free and premium tiers. The software that hosts the containers is called Docker Engine.
<!--more-->

Note that Docker is one of many container runtimes that are available, other runtimes include [rkt](https://coreos.com/rkt), [containerd](https://containerd.io) and [podman](https://podman.io).

# Overview

## Networking
The docker *host* network topologies only works on linux. In host mode the port bindings are ignored as the container's network is shared with the host.

# Acronyms and Terms

| Term | Explanation |
| ---- | ----------- |
| OCI | Open Container Initiative |

# Handy Commands

Format the response returned from Docker:
``` sh
sudo docker container ls  --format "\{\{.ID\}\} \{\{.Image\}\} \{\{.Names\}\}"

sudo docker container inspect -f '{{ .NetworkSettings.IPAddress }}' mysql-basic
```

## Get IP address of container

## diff

Use the docker `diff` command to examine the differences in the container between the image and the new layer created by the container.
``` sh
sudo docker container diff official-nginx
```