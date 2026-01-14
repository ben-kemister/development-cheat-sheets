---
title: OpenShift
tags:
- kubernetes
- red_hat
- openshift
---

OpenShift is a family of containerization software products developed by Red Hat. 
<!--more-->
Its flagship product is the [OpenShift Container Platform](https://www.redhat.com/en/technologies/cloud-computing/openshift/container-platform) 
— a hybrid cloud platform as a service built around Linux 
containers orchestrated and managed by Kubernetes on a foundation of Red Hat Enterprise Linux.

## Topic Specific Pages

{{% children sort="title" description="true" %}}

## OpenShift Resources

| Name                       | Description                                                                                                                                              |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ImageContentSourcePolicy` | Holds cluster-wide information about how to handle registry mirror rules.                                                                                |


## Project wide node selectors

You can use an annotations on a Namespace to be able to set the default node selector for any Pods (including those 
created by Deployments) which are created in that Namespace.

For example:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  annotations: 
    # Schedule Pods onto the Nodes that are assigned the 'app' node-role.
    openshift.io/node-selector: "node-role.kubernetes.io/app="
  name: demo-namespace
```

The namespace annotation will **add** to any `nodeSelector` specified in the Pod's manifest, which can cause Pods to enter 
a _Pending_ status if a node with matching labels cannot be found.

For more information on this feature see [Creating project-wide node selectors](https://docs.openshift.com/container-platform/4.17/nodes/scheduling/nodes-scheduler-node-selectors.html#nodes-scheduler-node-selectors-project_nodes-scheduler-node-selectors)



