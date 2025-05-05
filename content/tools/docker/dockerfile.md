---
title: Dockerfile
tags:
- container
- linux
- development
- cli
- image
- dockerfile
---

This page contains examples about the use of a `Dockerfile` used when creating container image(s).
<!--more-->

## Labels

The [OCI Containers Specification](https://github.com/opencontainers/image-spec/blob/main/annotations.md#pre-defined-annotation-keys) 
defines several conventional labels that encapsulate common use cases for container images. These exist within the org.opencontainers.image namespace.

org.opencontainers.image.created – Image creation time.
org.opencontainers.image.url – URL to get information about the image.
org.opencontainers.image.version – Version of the main software inside the container (not the image’s version).
org.opencontainers.image.licenses – Container licensing information.
org.opencontainers.image.title – A human-readable name for the container.

To add these to your built image use the following syntax:
```dockerfile
LABEL org.opencontainers.image.authors="awesome.developer@email.com"
```

## ARG

You can define build arguments using `ARG` and also set default values for them when they are not supplied with the 
`--build-arg=<KEY>=<VALUE>` argument.

For example:
```dockerfile
ARG MY_BUILD_ARG=some_default_value

# Using the build argument
COPY ${MY_BUILD_ARG} /dest
```


## ADD vs COPY

`ADD` and `COPY` are both instructions used to transfer files from the host machine to the Docker image. 
While they serve a similar purpose, they differ in functionality and use cases.

`COPY`: Copies files or directories from the source to the destination in the Docker image. It only handles basic file copying without extra features.

`ADD`: Offers more functionalities, including:
* Extracting local tar archives (e.g., .tar, .tar.gz).
* Fetching files from remote URLs via HTTP/HTTPS.

### Usage Recommendations

Use `COPY` for most file transfer needs due to its simplicity and security.
Use `ADD` only when you need its specific functionalities, such as:
* Automatically extracting archives.
* Downloading files from URLs (with caution and from trusted sources).

### Changing file permissions or ownership

Both `COPY` and `ADD` support changing file permissions (with `--chmod=<OCTAL_NOTATION>`) or ownership (with `--chown=<user:group>`)

For example:

```dockerfile
COPY --chmod=0755 /source /dest
```

## Environmental Variables (ENV)

You can create, set and provide default values for environmental variables in a `Dockerfile` using `ENV`.

For example:

```dockerfile
ENV MY_VARIABLE="defaultValue"

# You can also define multiple environmental variables on the same line like:
ENV VAR_1="Value_1" VAR_2="Value-2"
```

## Multiline

If you have long commands or want to use multiple lines you can use a space and ` \ `, for example:

```dockerfile
ENTRYPOINT [ "java", \
            "-javaagent:./otel/opentelemetry-javaagent.jar", \
            "-Dotel.javaagent.configuration-file=./config/opentelemetry.properties", \
            "-jar", "./my-springboot-app.jar" \
            ]
```