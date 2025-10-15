---
title: Configuration Properties
tags:
- java
- springboot
- configuration
---

Spring Boot's `@ConfigurationProperties` are a mechanism for binding external configuration properties (from files like 
`application.properties` or `application.yaml`) to Java objects. 
<!--more-->
This provides a structured, type-safe, and maintainable way to manage application configuration.

## Overview

SpringBoot configuration trees allow you to externalize some of the applications configurations as files in a directory structure.

The directory structure (and file names) will be used as the property **name** and the value comes from the file contents.

For example, given the file structure:
```text
/opt
    /my
        /app
            my-app.jar
            credentials/
                username
                password
```

And the following `application.yaml` file:
```yaml
spring:
  config:
    # Note this path is relative to the .jar file, but you can also use absolute paths as well.
    import: "configtree:credentials/"
```

You can then access or inject `credentials.username` and `credentials.password` properties from the Environment in the usual way.

This can be particularly useful when dealing with Secrets in Kubernetes.

## Kubernetes Secrets

SpringBoot configuration trees are particularly useful when running in Kubernetes. In this situation you can map Secret
values to volumes, and expose them as files in a particular directory structure.

For example, for the Secret created with:
```shell
kubectl -n <NAMESPACE> create secret generic my-app-secret \
  --from-literal=username=<USERNAME_VALUE> \
  --from-literal=password=<PASSWORD_VALUE> 
```

The values could be exposed to the Pod as follows:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-springboot-app
spec:
  volumes:
    - name: credentials
      secret:
        # Name of the Secret
        secretName: my-app-secret
  containers:
    - name: my-springboot-app
      volumeMounts:
        # The directory (configuration tree) structure to use to expose the value
        - mountPath: /opt/my/app/credentials/username
          name: credentials
          # The key within the Secret
          subPath: username
        - mountPath: /opt/my/app/credentials/password
          name: credentials
          subPath: password
```




