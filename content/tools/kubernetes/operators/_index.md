---
title: Operator Lifecycle Manager (OLM)
tags:
- kubernetes
- operators
- olm
---

The [Operator Lifecycle Manager (OLM)](https://olm.operatorframework.io/) extends 
Kubernetes to provide a declarative way to install, manage, and upgrade Operators on a cluster.
<!--more-->

## Topic Specific Pages

{{% children sort="title" description="true" %}}

## OLM Resources

| Name                       | Description                                                                                                                                              |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `OperatorGroup`            | The unit of multitenancy for OLM managed operators. It constrains the installation of operator in its namespace to a specified set of target namespaces. |
| `Subscription`             | Keeps Operators up to date by tracking changes to `Catalogs`                                                                                             |





