---
title: Repositories
tags:
 - kubernetes
 - helm
 - yaml
---

This page contains information about using (Helm chart) repositories.
<!--more-->

## Add a Helm repository

Add a helm repository with the command: `helm repo add [NAME] [URL] [flags]`, [more info](https://1helm.sh/docs/helm/helm_repo_add/).

Fetch the latest charts from the repository/ies with: `helm repo update`

## Searching a repository

To see the charts available within a repository use:

```powershell
PS> helm search repo wireguard
NAME                    CHART VERSION   APP VERSION     DESCRIPTION                                       
wireguard/wireguard     0.23.0          0.0.0           A Helm chart for managing a wireguard vpn in ku...
```

To view all the versions available add the `--versions` flag:

```powershell
PS> helm search repo wireguard --versions
NAME                    CHART VERSION   APP VERSION     DESCRIPTION
wireguard/wireguard     0.23.0          0.0.0           A Helm chart for managing a wireguard vpn in ku...
wireguard/wireguard     0.22.0          0.0.0           A Helm chart for managing a wireguard vpn in ku...
wireguard/wireguard     0.21.0          0.0.0           A Helm chart for managing a wireguard vpn in ku...
...
```

