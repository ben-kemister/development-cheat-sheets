---
title: Config
tags:
- git
- config
---

The [Git Configuration](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration) allow you to customise how 
git works.
<!--more-->

## Configuration Hierarchy

Git prioritizes configuration settings from the following sources:

1. System-wide configuration: `/etc/gitconfig` (applies to all users and repositories)
2. User configuration: `~/.gitconfig` (applies to all repositories for a user)
3. Repository-specific configuration: `.git/config` (applies only to the current repository)

You can specify which level to work with using options like `--system`, `--global`, or `--local` with the git `config` command.

## Viewing configuration

Displaying all Git configuration settings (system, global, and local):

```shell
git config --list
```

Use the ``--show-origin`` argument to display the configuration settings with their origin files:
```shell
git config --list --show-origin
```

Displaying global Git configuration settings:
```shell
git config --global --list
```

Displaying a specific Git configuration setting:
```shell
git config <key>
```
For example:
```shell
git config --global user.email
```

## Add a git configuration

To add/alter a git configuration setting use the following command:

```sh
# Syntax: git config [options] <configuration.item> <value>
git config http.sslverify false
```

You can make the configuration change global by using the `--global` option.
For example:
```sh
git config --global http.sslverify true
```

