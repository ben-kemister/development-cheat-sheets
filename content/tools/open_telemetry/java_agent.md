---
title: OpenTelemetry (Java)
tags: 
- apm
- opentelemetry
- otel
- java
---
 
Java specific implementation of [OpenTelemetry](../open_telemetry).
<!--more-->

## Java agent configuration

The java agent can consume [configuration from a variety sources](https://opentelemetry.io/docs/zero-code/java/agent/configuration/) including:

* Java System properties
* Environment variables
* Configuration file

## Auto-instrument Java applications

[OpenTelemetry Java auto-instrumentation](https://github.com/open-telemetry/opentelemetry-java-instrumentation) supports
collecting telemetry data from a huge number of libraries and frameworks. You can check out the full list [here](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/main/docs/supported-libraries.md).

### Application setup

1. Download the [latest OpenTelemetry Java JAR agent](https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/latest/download/opentelemetry-javaagent.jar)
2. Enable the instrumentation agent and run your application


* If you run your Java application as a JAR file, run your application using the following command:
```shell
OTEL_EXPORTER_OTLP_ENDPOINT="http://<IP of SigNoz Backend>:4317" OTEL_RESOURCE_ATTRIBUTES=service.name=<app_name> java -javaagent:/path/to/opentelemetry-javaagent.jar -jar  <myapp>.jar
```

Things to note about the command:
* <app_name> - the name you want to set for your application.
* `OTEL_EXPORTER_OTLP_ENDPOINT` - the endpoint of the machine where your SigNoz/APM backend is installed.
* `path/to` - the path of your downloaded OpenTelemetry Java JAR agent.

You can also specify the OpenTelemetry variables as [Java system properties](../../languages/java) in the following way:

```shell
java -javaagent:/path/opentelemetry-javaagent.jar \
-Dotel.exporter.otlp.endpoint=http://<IP of SigNoz Backend>:4317 \
-Dotel.resource.attributes=service.name=<app_name> \
-jar <myapp>.jar
```
