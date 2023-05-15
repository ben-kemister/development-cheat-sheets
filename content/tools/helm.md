---
title: Helm
tags:
 - container
 - linux
 - development
 - kubernetes
 - helm
 - yaml
---

[Helm](https://helm.sh/) helps you manage Kubernetes applications — Helm Charts help you define, install, and upgrade even the most complex Kubernetes application.
<!--more-->

## Handy Commands

| Command | Description |
| --- | --- |
| `helm version` | Print the client version information |
| `helm ls` | List the deployed helm charts |
| `helm create <chart_name>` | Create a helm chart |
| `helm uninstall <deployment_name>` | Remove/uninstall the *deployment_name* from the cluster |
| `helm upgrade -i my <deployment_name> .\<folder>\` | Upgrade, or install, the helm chart from *folder* into the cluster with the name *deployment_name* |
 

## Helm Charts 

### Flow control

See: https://helm.sh/docs/chart_template_guide/control_structures/

#### If/Else

Example:
```yaml
      volumes:
        - name: influxdb-data
          {{- if not .Values.volumes.influxdb.claimName }}
          emptyDir: {}
          {{- else }}
          persistentVolumeClaim:
            claimName: {{ .Values.volumes.influxdb.claimName }}
          {{- end }}
```