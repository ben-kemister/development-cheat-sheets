---
title: Network
tags:
- docker
- docker_compose
- network
---


[Docker/container networking](https://docs.docker.com/engine/network/) refers to the ability for containers to connect 
to and communicate with each other, or to non-Docker workloads.
<!--more-->
Containers have networking enabled by default, and they can make outgoing connections. 
A container has no information about what kind of network it's attached to, or whether their peers are also Docker workloads or not. 

A container only sees a network interface with an IP address, a gateway, a routing table, DNS services, and other networking details. 
That is, unless the container uses the `none` network driver.

## Host network

If you use the [`host` network mode](https://docs.docker.com/engine/network/drivers/host/) for a container, 
that container's network stack isn't isolated from the Docker host (the container shares the host's networking namespace), 
and the container doesn't get its own IP-address allocated. 

For instance, if you run a container which binds to port 80 and you use `host` networking, 
the container's application is available on port 80 on the host's IP address.

> Note that Host networking is also supported on Docker Desktop version 4.29 and later for Mac, Windows, and Linux as a **beta** feature. 
> To enable this feature, navigate to the Features in development tab in Settings, and then select Enable host networking.

### Using Host network

To run a container using the `host` network you need to add the `--network=host` argument, for example:

```powershell
docker run --rm --network=host `
-e TZ=Australia/Canberra `
-e PORT=20211 `
jokobsk/netalertx:latest
```



