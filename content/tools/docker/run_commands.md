---
title: Docker (run) commands
tags:
- container
- linux
- development
- cli
---

This page contains examples about the use of Docker commands when _running_ containers.
<!--more-->

## Copy files from container to host

`docker cp <container_id>:/path/filename.txt ~/Desktop/filename.txt`

## Networking

### Mode
The docker *host* network topologies works on linux, and tends to be flaky in Windows. In host mode the port bindings are ignored as the container's network is shared with the host.

For more information see the [Docker Network doco](https://docs.docker.com/network/).

### Exposing UDP ports

To expose UDP ports when running a container add the `/udp` suffix to the port mapping.

`docker run -p 80:80/tcp -p 80:80/udp my_app`

The same goes for use within a `Dockerfile`.

``` sh
# Expose the UI, and the UDP port (used for device discovery)
EXPOSE 3000 50000/udp
```

For more information see the [Docker expose doco](https://docs.docker.com/engine/reference/builder/#expose).


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

## Errors

### `standard_init_linux.go:228: exec user process caused: exec format error`

That error usually means you're trying to run a **amd64** image on a **non-amd64** host (such as 32-bit or ARM).
