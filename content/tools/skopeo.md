---
title: "Skopeo"
tags:
- container
- image
---

[Skopeo](https://github.com/containers/skopeo) is an open-source, daemonless command-line utility for managing container 
images and repositories, designed to inspect, copy, sign, and delete images without requiring a local container engine. 
<!--more-->
It works directly with remote registries (Docker/OCI) and local storage.

## inspect image

You can use skopeo to inspect a container image in an image registry without needing to pull it to the local file system.
The output includes the sizes of the image layers.

To inspect an image with skopeo use `skoep inspect docker://<IMAGE_URL>`, for example:

```shell
% bin/skopeo inspect docker://docker.io/library/nginx 
{
    "Name": "docker.io/library/nginx",
    "Digest": "sha256:b95a99feebf7797479e0c5eb5ec0bdfa5d9f504bc94da550c2f58e839ea6914f",
    "RepoTags": [],
    "Created": "2022-08-23T03:59:02.789512663Z",
    "DockerVersion": "20.10.12",
    "Labels": {
        "maintainer": "NGINX Docker Maintainers \u003cdocker-maint@nginx.com\u003e"
    },
    "Architecture": "amd64",
    "Os": "linux",
    "Layers": [
    ...
```

> Note if you need to trust the image registries certificate you can add the CA certificate to the command using:
> `skopeo inspect --cert-dir=/certificates/ docker://docker.io/library/nginx`