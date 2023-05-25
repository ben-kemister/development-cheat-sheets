---
title: Docker build
tags:
- container
- linux
- development
- cli
- image
---

This page contains examples about the use of Docker commands when _building_ containers.
<!--more-->

## Building with `Dockerfile`

```powershell
docker build -f .\Dockerfile -t [PRIVATE_IMAGE_REGISTRY[:PORT]/]<IMAGE_NAME>[:<VERSION_TAG>] .
```

## Labels

The [OCI Containers Specification](https://github.com/opencontainers/image-spec/blob/main/annotations.md#pre-defined-annotation-keys) 
defines several conventional labels that encapsulate common use cases for container images. These exist within the org.opencontainers.image namespace.

org.opencontainers.image.created – Image creation time.
org.opencontainers.image.url – URL to get information about the image.
org.opencontainers.image.version – Version of the main software inside the container (not the image’s version).
org.opencontainers.image.licenses – Container licensing information.
org.opencontainers.image.title – A human-readable name for the container.

To add these to your built image use the following syntax:
```dockerfile
LABEL org.opencontainers.image.authors="awesome.developer@email.com"
```

## Errors

### buildx build --push to private registry fails with 'tcp: lookup <registry_hostname> on <container_ip_address>: no such host' error

This happens when the build container is not able to resolve the <registry_hostname> to an IP address. When this happens
you receive an error something like:
```text
------
 > exporting to image:
------
ERROR: failed to solve: failed to do request: Head "https://<registry_hostname>/v2/<image_name>/blobs/sha256:699020637dddc10710f773812992bbccd136879ea4680f4a3a819e95c6b65055": dial tcp: lookup <registry_hostname> on 192.168.65.5:53: no such host
```

Even using the builder created with `docker buildx create --driver docker-container --driver-opt network=host --use` fails to resolve the IP address of the registry. For more details on the creation of a buildx builder see [this page](https://docs.docker.com/engine/reference/commandline/buildx_create/#driver-opt).

The workaround is to add the details of the private registry to the running build container's /etc/hosts file:

* Find the id if the running build container: `docker ps`
* Get a terminal in the container: `docker exec -it <build_container_id> /bin/sh`
* Add your private regisrty to the hosts file `echo "192.168.0.xxx    <your_private_registry_hostname>" >> /etc/hosts`
* Done!

This problem looks to be related to: [Docker Buildx issue 1461](https://github.com/docker/buildx/issues/1461#issuecomment-1358979427).
