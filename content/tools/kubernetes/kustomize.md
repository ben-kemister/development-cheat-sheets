---
title: "Kustomize"
tags:
- kubernetes
- development
- kubectl
---

[Kustomize](https://kustomize.io/) is a standalone tool to customize Kubernetes objects through a kustomization file.
<!--more-->
Since 1.14, Kubectl also supports the management of Kubernetes objects using a kustomization file. 

## View

To view the Resources generated from a directory containing a `kustomization.yaml` file, run the `kubectl kustomize <kustomization_directory>`
command, for example:

```shell
kubectl kustomize ./k3s-monitoring/
```
Alternatively you can use `kubectl` with the `--dry-run` flag like:

```shell
kubectl apply --dry-run=client -k ./k3s-monitoring/
```

## Apply

To apply those Resources, run kubectl apply with `--kustomize` or `-k` flag:

```shell
kubectl apply -k <kustomization_directory>
```

## Basic kustomization.yaml file

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: default

resources:
  - https://raw.githubusercontent.com/argoproj/argo-cd/v2.10.1/manifests/install.yaml
  # Additional resources like ingress rules, cluster and repository secrets.
  - argocd-ingress.yaml
  - argocd-ingressroute.yaml

# Patches to config maps
patches:
  - path: overlays/argocd-cmd-params-cm.yaml
```

## Use resources from another git repo

```yaml
---
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# Use the default namespace specified in the resources

# Resources like ingress rules, cluster and repository secrets.
resources:
  #  The kustomization.yaml file lives in the root of the kube-prometheus git repo
  - github.com/prometheus-operator/kube-prometheus?ref=v0.13.0
```

## Images

You can change the images, registries, or tags of the images by using an `images` section, for example:

```yaml
#
# Description: This is a Kustomise file for applying changes to the default Argo CD manifests.
#
# To view the results use:
#   kubectl kustomize ./argocd/ > ./argocd/test.yaml
#
# To apply run:
#   kubectl apply -k ./argocd/
#
---
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: argocd

resources:
  - https://raw.githubusercontent.com/argoproj/argo-cd/v2.10.6/manifests/install.yaml

images:
  # Use an image (without changing the tag) from a private image registry
  - name: quay.io/argoproj/argocd
    newName: my-private-registry.example.org/argoproj/argocd
  # Use a different tag/version of an image
  - name: redis
    newTag: 7.0.11-alpine # originally 7.0.14-alpine
```

## Kustomizing Helm charts

Add the helm chart details to the `kustomization.yaml` file:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: eclipse-che

helmCharts:
  - name: eclipse-che
    repo: https://eclipse-che.github.io/che-operator/charts
    releaseName: eclipse-che
    namespace: eclipse-che
    version: 7.89.0
...
```

To use kustomizes' builtin HelmChartInflationGenerator you need to add the `--enable-helm` to any commands. 
For example:

You can preview the results with: `kubectl kustomize --enable-helm directory/with/kustomization.yaml/`

And you can apply it to a cluster with: `kubectl kustomize --enable-helm directory/ | kubectl apply -f -`

### Helm chart fields/options

There are a number of useful fields/options within the Helm chart structure/object to be aware of:

| Field/Option          | Description                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------| 
| name                  | The name of the chart, e.g. 'minecraft'.                                                                                                                                                                                                                                                                                                                                                                    |
| version               | The version of the chart, e.g. '3.1.3'                                                                                                                                                                                                                                                                                                                                                                      |
| repo                  | Repo is a URL locating the chart on the internet. <br>This is the argument to helm's  `--repo` flag, e.g. <br> `https://itzg.github.io/minecraft-server-charts`.                                                                                                                                                                                                                                            |
| releaseName           | Replaces RELEASE-NAME in chart template output, making a particular inflation of a chart unique with respect to other inflations of the same chart in a cluster.<br>It's the first argument to the helm `install` and `template` commands, i.e. `helm install {RELEASE-NAME} {chartName} helm template {RELEASE-NAME} {chartName}` <br>If omitted, the flag `--generate-name` is passed to 'helm template'. |
| namespace             | Set the target namespace for a release. It is `.Release.Namespace` in the helm template                                                                                                                                                                                                                                                                                                                     |
| additionalValuesFiles | Local file paths to values files to be used in addition to either the default values file or the values specified in `valuesFile`.                                                                                                                                                                                                                                                                          |
| valuesFile            | A local file path to a values file to use _instead of_ the default values that accompanied the chart. <br>The default values are in `{ChartHome}/{Name}/values.yaml`.                                                                                                                                                                                                                                       |
| valuesInline          | Holds value mappings specified directly rather than in a separate file.                                                                                                                                                                                                                                                                                                                                     |
| valuesMerge           | Specifies how to treat ValuesInline with respect to Values. <br>Legal values: `merge`, `override`, `replace`. Defaults to `override`.                                                                                                                                                                                                                                                                       |
| includeCRDs           | Specifies if Helm should also generate CustomResourceDefinitions. <br>Defaults to `false`                                                                                                                                                                                                                                                                                                                   |
| skipHooks             | Sets the `--no-hooks` flag when calling helm template. <br>This prevents helm from erroneously rendering test templates.                                                                                                                                                                                                                                                                                    |
| apiVersions           | The kubernetes apiversions used for Capabilities.APIVersions                                                                                                                                                                                                                                                                                                                                                |
| kubeVersion           | The kubernetes version used by Helm for Capabilities.KubeVersion                                                                                                                                                                                                                                                                                                                                            |
| nameTemplate          | For specifying the name template used to name the release                                                                                                                                                                                                                                                                                                                                                   |
| skipTests             | Skips tests from templated output                                                                                                                                                                                                                                                                                                                                                                           |
| debug                 | Enables debug output from the Helm chart inflator generator                                                                                                                                                                                                                                                                                                                                                 |
| devel                 | Allow for devel release to be used                                                                                                                                                                                                                                                                                                                                                                          |

I found the full list of fields in the [Kustomize source code on GitHub](https://github.com/kubernetes-sigs/kustomize/blob/master/api/types/helmchartargs.go).


## Patches

### Replace/update

```yaml
...
patches:
  # Reduce the Alertmanager replicas to save on system resources
  - target:
      kind: Alertmanager
      name: main
    patch: |-
      - op: replace
        path: /spec/replicas
        value: 1
```

### Add a single annotation to existing block

You can add a single annotation to an existing block using:

```yaml
...
patches:
  - target:
      kind: Namespace
    patch: |-
      - op: add
        path: /metadata/annotations/argocd.argoproj.io~1sync-wave
        value: -5
```

The result will be:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  annotations:
    argocd.argoproj.io/sync-wave: "-5"
  name: monitoring
```

### Add annotations block

You can add annotations block (if it does not exist) by using the following:

```yaml
...
patches:
  - target:
      kind: Deployment
      name: argocd-redis
    patch: |-
      # Disable version-checker on the redis image
      - op: add
        path: /spec/template/metadata/annotations
        value: 
          enable.version-checker.io/redis: "false"
```

### Append to a list/array

Note the use of the `-` characters at the path, this will append to the `/spec/ingress` list/array.

```yaml
...
patches:
  # Patch the grafana network policy so that we can get to the UI
  - target:
      kind: NetworkPolicy
      name: grafana
    patch: |-
      - op: add
        path: /spec/ingress/-
        value:
          from:
            - namespaceSelector:
                matchLabels:
                  # The Traefik ingress controller resides in another namespace
                  kubernetes.io/metadata.name: kube-system
          ports:
            - port: 3000
              protocol: TCP
```

### Modify an entry in a list/array

```yaml
patches:
  - target:
      kind: Ingress
    patch: |-
      # Update the hostname in the Ingress
      - op: replace
        path: /spec/rules/0/host
        value: my-app.example.com
```

## ConfigMapGenerator

Kustomize provides two ways of adding ConfigMap in one kustomization, either by declaring ConfigMap as a resource or 
declaring ConfigMap from a ConfigMapGenerator. The formats inside `kustomization.yaml` are:

```yaml
# declare ConfigMap as a resource
resources:
- configmap.yaml

# declare ConfigMap from a ConfigMapGenerator
configMapGenerator:
- name: a-configmap
  files:
  # configfile is used as key
    - configs/configfile
  # configkey is used as key
    - configkey=configs/another_configfile
```

The ConfigMaps declared as resource are treated the same way as other resources. 
Kustomize doesn't append any hash to the ConfigMap name. 

The ConfigMap declared from a ConfigMapGenerator is treated differently. 
A hash is appended to the name and any change in the ConfigMap will trigger a rolling update. 
This can be disabled if needed using:

```yaml
configMapGenerator:
- name: a-configmap
  options:
    disableNameSuffixHash: true
```


