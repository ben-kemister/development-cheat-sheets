---
title: Creating Charts
tags:
 - kubernetes
 - helm
 - yaml
---

This page contains information about creating (Helm) charts and using templates.
<!--more-->

## Flow control

See: https://helm.sh/docs/chart_template_guide/control_structures/

### If/Else

To add entries based on the existence of a value/object.

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

To control the template based on a value match you can use:

```yaml
{{ if eq .Values.gogsConfig.database.type "sqlite3" }}
; For "sqlite3" only, make sure to use absolute path.
PATH = {{ .Values.gogsConfig.database.path }}
{{ end }}
```

### Range (Loops)

```yaml
# 
# Create a new configmap for every object in .Values.dashyPages
#
{{- range .Values.dashyPages }}
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ include "dashy.fullname" $ }}-page-{{- .path }}
  labels:
    {{- include "dashy.labels" $ | nindent 4 }}
data:
  {{ .path | quote }}: |-
    {{- toYaml .contents | nindent 4 }}
  {{- end }}
```

## Including (YAML) blocks

You can embed/include yaml blocks in the generated manifests using the syntax `{{ toYaml .Values.blockName | indent 4 }}`

> Note you can also combine this with _whitespace controls_ to get rid of any extra whitespaces at the start/end of your
> block.

This informational nugget came from [this stackoverflow post](https://stackoverflow.com/questions/51815600/how-to-include-nested-value-in-helm-template).

### Using include inside Range

**TL;DR;** just replace `.` with `$` to use the global scope instead of the local one you created.

Example:
```yaml
{{- include "my-chart.labels" $ | nindent 4 }}
```

When inside a range `.` refers to the current scope. `$` is mapped to the root scope when template execution begins and 
it does not change during template execution.

## Controlling Whitespace

The curly brace syntax of template declarations can be modified with special characters to tell the template engine to chomp whitespace.
`{{-` (with the dash and space added) indicates that whitespace should be chomped left, while `-}}` means whitespace to the right should be consumed.
_Be careful! Newlines are whitespace!_

For more information see the [Helm whitespace docs](https://helm.sh/docs/chart_template_guide/control_structures/#controlling-whitespace).

## Replacing characters

If you're just trying to replace a fixed string, use [replace](https://docs.helm.sh/docs/chart_template_guide/function_list/#replace). 

```yaml
{{- "https://endpoint.index.up" | replace "." "\\." -}}
```

```yaml
    {{- range .Values.dashyPages }}
    - mountPath: /app/user-data/{{- .path }}
      name: {{ .path | replace "." "-" }}
      subPath: {{ .path }}
    {{- end }}
```

## Multiline Strings

An easy way use multiline strings in helm templates is to use:

```yaml
coffee: |
  Latte
  Cappuccino
  Espresso 
```

## Key Value pairs

```yaml
# in value.yaml
vrIds:
  51: 169.254.1.1
  52: 169.254.1.2
```

```yaml
# In template
{{- range $key, $value := .Values.vrIds }}
  vrrp.{{ $key }}.vip: {{ $value }}
{{- end }}
```

## Automatically Roll Deployments

You can use values in the annotations of your Deployment as a trigger to restart the pods within a deployment.

### Automatically Roll Deployment if the config changes

This technique comes from the [Helm Tips and Tricks page](https://helm.sh/docs/howto/charts_tips_and_tricks/#automatically-roll-deployments).

```yaml
kind: Deployment
spec:
  template:
    metadata:
      annotations:
        # Automatically Roll Deployment if the config changes
        checksum/config: {{ include (print $.Template.BasePath "/configmap.yaml") . | sha256sum }}
```

### Always Roll Deployment

```yaml
kind: Deployment
spec:
  template:
    metadata:
      annotations:
        # Add a timestamp annotation to always trigger a re-deployment when performing an upgrade
        # Note this will cause the Argo CD app to appear 'Out Of Sync' when the timestamp changes
        timestamp: {{ now | quote }}
```