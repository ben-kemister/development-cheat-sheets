---
title: compose
tags:
- container
- development
- docker
- docker_compose
---

[Docker Compose](https://docs.docker.com/compose/) is a tool for defining and running multi-container applications.
<!--more-->
When using Docker extensively, the management of several different containers quickly becomes cumbersome.
Docker Compose is a tool that helps us overcome this problem and easily handle multiple containers at once.
In short, Docker Compose works by applying many rules declared within a single `docker-compose.yml` configuration file.

## Syntax

Docker compose uses the `docker compose [-f <arg>...] [options] [COMMAND] [ARGS...]` [command line syntax](https://docs.docker.com/engine/reference/commandline/compose/).

## Common Commands

| Command              | Description                                                            |
|----------------------|------------------------------------------------------------------------|
| `docker compose up`  | Builds, (re)creates, starts, and attaches to containers for a service. |
| `docker compose run` | Run a one-off command on a service.                                    | 

## docker compose run

Syntax ``docker compose run [OPTIONS] SERVICE [COMMAND] [ARGS...]``

> Note: the command passed by run overrides the command defined in the service configuration.

For the given `docker-compose.yaml` file:

```yaml
version: '3.3'
services:
  app:
    image: alpine:latest
    entrypoint:
          - /bin/sh
          - -c
          - |
            echo In EntryPoint
            echo Args: "$@"
            exec "$@"
    command: ["sh", "date"]
```

Running `docker-compose run --rm app` will return:

```text
[+] Building 0.0s (0/0)                                                       docker-container:hardcore_dhawan
[+] Building 0.0s (0/0)                                                       docker-container:hardcore_dhawan 
In EntryPoint
Args: date
Sun Jan 28 21:59:21 UTC 2024
```

However, running `docker-compose run --rm app sh echo Hello World ` will return:

```text
[+] Building 0.0s (0/0)                                                       docker-container:hardcore_dhawan
[+] Building 0.0s (0/0)                                                       docker-container:hardcore_dhawan 
In EntryPoint
Args: echo Hello World
Hello World
```

## Links & References

* [Introduction to Docker Compose - Baeldung](https://www.baeldung.com/ops/docker-compose)