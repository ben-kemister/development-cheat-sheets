---
title: "Private Registry"
date: 2024-02-04T05:38:31+11:00
tags:
  - k3s
  - image
  - registry
---

## Using a private image registry

If you want to pull the images from a private image registry (rather than their defaults) you can create a configuration
file `/etc/rancher/k3s/registries.yaml` on each of the k3s nodes.
You can then configure which image registries are redirected to your private (mirrored) registry. For example:

```yaml
mirrors:
  docker.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
  quay.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
  gcr.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
  # Replaces the deprecated k8s.gcr.io registry
  registry.k8s.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
```

This will redirect any images that use `docker.io`, `quay.io`, `gcr.io` or `registry.k8s.io` to a private image registry.

> Note: In order for the registry changes to take effect, you need to restart the K3s service on the node
> `sudo systemctl restart k3s-agent`

Configuring the node to use mirrors means that you do not need to worry about changing the registry named in each manifest
or Helm chart.

For more information see the [k3s documentation](https://docs.k3s.io/installation/private-registry).

### With a fallback registry

As `containerd` will try each of the endpoint URLs one by one (using the first working one) you can add a second URL as
a fallback which will be used in the event that the first endpoint is unavailable.

For example:

```yaml
mirrors:
  docker.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
      - "https://registry-1.docker.io"
  quay.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
      - "https://quay.io"
  gcr.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
      - "https://gcr.io"
  # Replaces the deprecated k8s.gcr.io registry
  registry.k8s.io:
    endpoint:
      - "https://<PRIVATE_REGISTRY_HOST>:<PORT>"
      - "https://registry.k8s.io"
```
