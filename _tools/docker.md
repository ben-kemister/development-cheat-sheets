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

## Errors

### `standard_init_linux.go:228: exec user process caused: exec format error`

That error usually means you're trying to run a **amd64** image on a **non-amd64** host (such as 32-bit or ARM).

### buildx build --push to private registry fails with 'tcp: lookup <registry_hostname> on <container_ip_address>: no such host' error

This happens when the build container is not able to resolve the <registry_hostname> to an IP address. When this happen you recieve an error something like:

``` sh
------
 > exporting to image:
------
ERROR: failed to solve: failed to do request: Head "https://<registry_hostname>/v2/<image_name>/blobs/sha256:699020637dddc10710f773812992bbccd136879ea4680f4a3a819e95c6b65055": dial tcp: lookup <registry_hostname> on 192.168.65.5:53: no such host
```

Even using the builder created with `docker buildx create --driver docker-container --driver-opt network=host --use` fails to resolve the IP address of the registry.

The workaround is to add the details of the private registry to the running build container's /etc/hosts file:

# Find the id if the running build container: docker ps
# Get a terminal in the container: docker exec -it <build_container_id> /bin/sh
# Edit the /etc/hosts file: vi /etc/hosts
# Add a line for the private registry: 192.168.0.X   <private_registry_hostname>

This problem looks to be related to: [Docker Buildx issue 1461](https://github.com/docker/buildx/issues/1461#issuecomment-1358979427).
