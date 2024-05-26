---
title: "Grafana"
tags:
- grafana
- graphs
---

[Grafana](https://grafana.com/) is a multi-platform open source analytics and interactive visualization web application. 
<!--more-->
It can produce charts, graphs, and alerts for the web when connected to supported data sources.

## Configuring

Grafana can be [configured](https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/#configure-grafana) using environmental variables or via a `grafana.ini` configuration file.


### Configuration file

On linux the configuration file is located at `/etc/grafana/grafana.ini`

### Overriding configuration with Environmental variables

[Grafana's configuration documentation](https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/#override-configuration-with-environment-variables) 
recommends using environmental variables to override existing options, not to _add_ new configuration settings.

You can use the `GF_<SectionName>_<KeyName>` form to override a particular value in the `grafana.ini` configuration file.

#### Handy Variables

| Variable Name                | Description              |
|------------------------------|--------------------------| 
| `GF_SECURITY_ADMIN_PASSWORD` | (Default) Admin password |
| `GF_SECURITY_ADMIN_USER`     | (Default) Admin username |

