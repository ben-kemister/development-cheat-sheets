---
title: submodules
tags:
- git
- submodules
---

[Git Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) allow you to keep a Git repository as a subdirectory
of another Git repository. This lets you clone another repository into your project and keep your commits separate.
<!--more-->

## Adding a submodule

```shell
git submodule add https://github.com/chaconinc/DbConnector
```

### Add a submodule to a specific directory

```shell
git submodule add [--depth <depth>] <repository> <path>
```

For example:
```shell
git submodule add --depth 1 https://github.com/EverythingSmartHome/everything-presence-lite.git presence-sensors/everything-presence-lite
```


## Cloning a Project with Submodules

When you clone a project which has submodules, by default you get the directories that contain submodules, 
but none of the files within them yet.

You can override this behaviour and use `--recurse-submodules` when cloning the project. This will clone the main project
and all the submodules. For example:

```shell
git clone --recurse-submodules https://github.com/chaconinc/MainProject
```

### Populate (pull) contents of submodule

If you have cloned a git repo without using the `--recurse-submodules` you will need to run some addition commands to
pull in the contents of the submodules.

```shell
git submodule init
git submodule update
```

## Remove a submodule

Use `git rm <path-to-submodule>` and commit (if the adding of the submodule has been committed previously).

```shell
git rm k3s-ansible
```