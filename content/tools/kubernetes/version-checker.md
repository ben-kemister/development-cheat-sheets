---
title: "Version Checker"
tags:
- kubernetes
---

[version-checker](https://github.com/jetstack/version-checker?tab=readme-ov-file) is a Kubernetes utility for observing 
the current versions of images running in the cluster, as well as the latest available upstream. 
<!--more-->
These checks get exposed as Prometheus metrics to be viewed on a [grafana dashboard](https://grafana.com/grafana/dashboards/12833-version-checker/), 
or soft alert cluster operators.

## Pod Annotations

The following annotations can be used on Pods to control version-checker's behaviour.

| (YAML/Helm chart) Annotation                                                                        | Description                                                                                 |
|-----------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------| 
| `enable.version-checker.io/sidecar: "false"`                                                        | Exclude the `sidecar` container from being checked.                                         |
| `override-url.version-checker.io/cert-manager-controller: quay.io/jetstack/cert-manager-controller` | Change the URL for where to lookup where the latest image version is located                |
| `match-regex.version-checker.io/cert-manager-controller: v(\d+)\.(\d+)\.(\d+)`                      | Used for only comparing against image tags which match the regex set `v(\d+)\.(\d+)\.(\d+)` |
