---
title: "Kustomizing Helm charts"
tags:
- kustomize
- helm charts
---

This page covers the use of Helm charts within kustomize.
<!--more-->

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
