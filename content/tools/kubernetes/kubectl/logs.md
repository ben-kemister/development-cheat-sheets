---
title: logs
tags:
 - kubernetes
 - kubectl
 - logs
---

This page covers the use of the `kubectl logs` command.
<!--more-->

## Logs for a Pod

To view the logs for a pod use the commands below:

```sh
kubectl logs <pod_name>

# For example
kubectl logs traefik-ingress-7f4f6cd549-jkdjl -n kube-system
```

### Logs for a specific container in a Pod

To access logs from a specific container (such as Init Containers)
Pass the Init Container name along with the Pod name to access its logs.

```sh
kubectl logs <pod-name> -c <init-container-2>

# For example
kubectl logs <pod-name> -c <init-container-2>

kubectl logs $(kubectl get po | grep magicmirror | awk '{ print $1}') -c install-modules
```

## Logs by label

You can use `kubectl logs -l '<label>'` to retrieve the logs from the Pods with matching labels.
```shell
kubectl get logs -n kube-system -l 'k8s-app=kube-dns'
```

You can also combine multiple label filters like
```shell
kubectl logs pods -l 'part-of=my-app, tier!=frontend'
```

## Logs <Resource> Examples

| Command                                                        | Description                                 |
|----------------------------------------------------------------|---------------------------------------------|
| `kubectl -n namespace logs pod_name`                           | Show logs for a specific pod in a namespace |
| `kubectl logs -l 'app.kubernetes.io/instance=test-monitoring'` | Show the logs based on a label              |
| `kubectl logs pod_name --tail=100`                             | Get the last 100 log lines                  |




