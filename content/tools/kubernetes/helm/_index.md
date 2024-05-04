---
title: Helm
tags:
 - container
 - kubernetes
 - helm
 - yaml
---

[Helm](https://helm.sh/) helps you manage Kubernetes applications — _Helm Charts_ help you define, install, and upgrade even the most complex Kubernetes application.
<!--more-->

## Helm sub-pages

{{% children sort="title" description="true" %}}

## Handy Commands

| Command                                                                         | Description                                                                                        |
|---------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| `helm version`                                                                  | Print the client version information                                                               |
| `helm ls`                                                                       | List the deployed helm charts                                                                      |
| `helm create <chart_name>`                                                      | Create a helm chart                                                                                |
| `helm install <deployment_name> --dry-run -f .\my-values.yaml .\<helm_folder>\` | Using `--dry-run` tests the generation/output created by an install/upgrade command                |
| `helm upgrade -i <deployment_name> .\<folder>\`                                 | Upgrade, or install, the helm chart from *folder* into the cluster with the name *deployment_name* |
| `helm get values <RELEASE_NAME> -a`                                             | Download all od the computed values for a given release                                            |
| `helm uninstall <deployment_name>`                                              | Remove/uninstall the *deployment_name* from the cluster                                            |
 
