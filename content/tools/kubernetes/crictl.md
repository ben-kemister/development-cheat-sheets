---
title: crictl
tags:
 - container
 - kubernetes
---

[crictl](https://kubernetes.io/docs/tasks/debug/debug-cluster/crictl/) is a command-line interface for CRI-compatible container runtimes. 
<!--more-->
You can use it to inspect and debug container runtimes and applications on a Kubernetes node. 

## List images on node

You can use the `crictl image ls` command to list the images which are present on a k3s node, for example:

```shell
$ sudo crictl image ls
IMAGE                                                            TAG                               IMAGE ID            SIZE
docker.io/container-tools/inotify-rsync                          latest                            c6bcd87db9379       4.23MB
...
```

## Inspect an image

Use `crictl inspecti <IMAGE>:<TAG>`

```shell
sudo crictl inspecti jlesage/handbrake:v24.06.1
```

> The response is a JSON document so you can use tools like jq to help filter/navigate the results.
> For example: `` sudo crictl inspecti jlesage/handbrake:v24.06.1 | jq .info.imageSpec.config``

## Links & References

* [List of Commands example for working with crictl](https://www.devopsschool.com/blog/list-of-commands-example-for-working-with-crictl/)
