---
title: Docker (run) commands
tags:
- container
- linux
- development
- cli
- run
---

This page contains examples about the use of Docker commands when _running_ containers.
<!--more-->

## Remove container on exit

Add the `--rm` flag to automatically remove the container when it exits.
```shell
docker run --rm hello-world
```

## Setting environmental variables

You can set environmental variables in your docker container by using the syntax: `-e "VARIABLE_NAME=value"`.

Just add this argument to your `docker run` command before you specify the image. 

### Exposing UDP ports

To expose UDP ports when running a container add the `/udp` suffix to the port mapping.

`docker run -p 80:80/tcp -p 80:80/udp my_app`

The same goes for use within a `Dockerfile`.

``` sh
# Expose the UI, and the UDP port (used for device discovery)
EXPOSE 3000 50000/udp
```

For more information see the [Docker expose doco](https://docs.docker.com/engine/reference/builder/#expose).

## Copy files from container to host

`docker cp <container_id>:/path/filename.txt ~/Desktop/filename.txt`

## Networking

### Accessing the host from the container

Within the container you can use the hostname `host.docker.internal` to refer to the machine running the container.
Using `host.docker.internal` effectively equates to `localhost` on the host machine.

For more information see: [How to connect to the Docker host from inside a Docker container?](https://medium.com/@TimvanBaarsen/how-to-connect-to-the-docker-host-from-inside-a-docker-container-112b4c71bc66)

### Mode
The docker *host* network topologies works on linux, and tends to be flaky in Windows. 
In `host` mode the port bindings are ignored as the container's network is shared with the host.

For more information see the [Docker Network doco](https://docs.docker.com/network/).

# Other handy Commands

Format the response returned from Docker:
``` sh
sudo docker container ls  --format "\{\{.ID\}\} \{\{.Image\}\} \{\{.Names\}\}"

sudo docker container inspect -f '{{ .NetworkSettings.IPAddress }}' mysql-basic
```

## diff

Use the docker `diff` command to examine the differences in the container between the image and the new layer created by the container.
``` sh
sudo docker container diff official-nginx
```

## Errors

### `standard_init_linux.go:228: exec user process caused: exec format error`

That error usually means you're trying to run a **amd64** image on a **non-amd64** host (such as 32-bit or ARM).

