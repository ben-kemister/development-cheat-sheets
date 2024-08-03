---
title: OpenTelemetry
tags: 
- apm
- opentelemetry
- otel
---

[OpenTelemetry](https://opentelemetry.io/) is a collection of APIs, SDKs, and tools. Use it to instrument, generate, 
collect, and export telemetry data (metrics, logs, and traces) to help you analyze your software’s performance and behavior.
<!--more-->

## Topic Specific pages

{{% children sort="title" description="true" %}}

## Attributes

When configuring an agent for an application there are a number of ways to set the high level attributes 
(such as `service.name`, `deployment.environment.name` etc).

You can set these attributes via environment variables, (Java) system properties, or using a configuration file.

### Handy Attributes

For a full list of attributes available (and their status/stability) see [Attribute Registry](https://opentelemetry.io/docs/specs/semconv/attributes-registry/).

| Attribute | Description | Link |
| --- |-------------|------| 
| `service.name` | Logical name of the service | [Service](https://opentelemetry.io/docs/specs/semconv/attributes-registry/service/) |
| `deployment.environment.name` | Name of the deployment environment (aka deployment tier) | [Deployment](https://opentelemetry.io/docs/specs/semconv/attributes-registry/deployment/) |


