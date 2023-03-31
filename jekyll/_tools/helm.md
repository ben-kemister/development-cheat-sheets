---
tool: helm
name: Helm
tags:
 - container
 - linux
 - development
 - kubernetes
---

TBC
<!--more-->

## Handy Commands

| Command | Description |
| --- | --- |
| `helm version` | Print the client version information |
| `helm ls` | List the deployed helm charts |
| `helm uninstall <deployment_name>` | Remove/uninstall the *deployment_name* from the cluster |
| `helm upgrade -i my <deployment_name> .\<folder>\` | Upgrade, or install, the helm chart from *folder* into the cluster with the name *deployment_name* |
