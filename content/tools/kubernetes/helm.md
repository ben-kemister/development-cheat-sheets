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

### Including (YAML) blocks

You can embed/include yaml blocks in the generated manifests using the syntax `{{ toYaml .Values.blockName | indent 4 }}`

> Note you can also combine this with _whitespace controls_ to get rid of any extra whitespaces at the start/end of your
> block.

This informational nugget came from [this stackoverflow post](https://stackoverflow.com/questions/51815600/how-to-include-nested-value-in-helm-template).

### Controlling Whitespace

The curly brace syntax of template declarations can be modified with special characters to tell the template engine to chomp whitespace. 
`{{-` (with the dash and space added) indicates that whitespace should be chomped left, while `-}}` means whitespace to the right should be consumed. 
_Be careful! Newlines are whitespace!_

For more information see the [Helm whitespace docs](https://helm.sh/docs/chart_template_guide/control_structures/#controlling-whitespace).

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
 
