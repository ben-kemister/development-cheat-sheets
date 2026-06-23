---
title: Registries
tags:
- podman
- registries
---

This page contains examples about the configuration of image registries in Podman.
<!--more-->

## Configuration files

The configuration of image registries in podman are done in the `registries.conf` file.
This is either located in `/home/user/.config/containers/registries.conf` when running in rootless mode or
`/etc/containers/registries.conf` for the system-wide configuration.

## Configuring mirrors

You can 

```toml
#
# Redirect 'docker.io' images to a private registry.
#
[[registry]]
prefix = "docker.io"
# Final fallback locaiton, tried if all the mirror(s) are unavailable
location = "private-image-registry-2.example.com/docker_mirror"

[[registry.mirror]]
# Primary mirror
location = "private-image-registry-1.example.com/docker_proxy"
```

> Note if you are using image shortnames (i.e. `podman pull hello-world` ) you may need to delete the `/etc/containers/registries.conf.d/000-shortnames.conf` 
> file otherwise podman will use the image registries configured by Red Hat.    
> On Windows this file can be deleted with the command:
> ```powershell
> wsl --distributions podman-machine-default -u root bash -c "rm -rf /etc/containers/registries.conf.d/000-shortnames.conf"
> ```
> 

