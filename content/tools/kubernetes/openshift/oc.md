---
title: oc (command line tool)
tags:
- openshift
---

The [OpenShift command-line interface (CLI)](https://docs.redhat.com/en/documentation/openshift_container_platform/latest/html/cli_tools/openshift-cli-oc#cli-about-cli_cli-developer-commands), 
is a way to create applications and manage OpenShift Container Platform projects from a terminal.
<!--more-->
The oc binary offers the same capabilities as the kubectl binary, 
but it is further extended to natively support OpenShift Container Platform features, 
such as: Full support for OpenShift resources.

## oc debug

The `oc debug` command starts a special pod on the cluster and drops you into a console allowing you to 
debug/investigate issues with the cluster.

More information can be found on: [How the oc debug command works in OpenShift](https://www.redhat.com/en/blog/how-oc-debug-works).

### Debugging Cluster nodes

You can use ``oc debug node/node_name`` to drop you into a pod which has access to the file system on that node.
The node's file system will be mapped to the `/host` directory in the pod.


## Troubleshooting

### oc login returns `net/http: TLS handshake timeout`

This can be caused by the `oc` client/machine not trusting the TLS certificate/s presented by the server.
You can get this messages if you yet to set up cert-manager on the cluster with your trusted certificates.

You can bypass this by adding `--insecure-skip-tls-verify=true` to your login command, for example:

```shell
oc login -u <USER_ID> --insecure-skip-tls-verify=true
```