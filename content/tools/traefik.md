---
title: Traefik
tags:
 - kubernetes
 - docker
 - certificate
---

[Traefik](https://doc.traefik.io/traefik/) is a modern HTTP reverse proxy and load balancer that makes deploying 
microservices easy.
<!--more-->
Traefik integrates with your existing infrastructure components (Docker, Swarm mode, Kubernetes, Marathon, Consul, Etcd, 
Rancher, Amazon ECS, ...) and configures itself automatically and dynamically.

## [Configuration](https://doc.traefik.io/traefik/v2.0/getting-started/configuration-overview/)

There are three different, mutually exclusive (e.g. you can use only one at a time), ways to define static configuration 
options in Traefik:

1. In a configuration file
2. In the command-line arguments
3. As environment variables
These ways are evaluated in the order listed above.

