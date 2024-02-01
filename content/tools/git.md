---
title: git
tags:
- git
- version_control
---

Git  is a distributed version-control system for tracking changes in source code during software development. 
It is designed for coordinating work among programmers, but it can be used to track changes in any set of files. 
Its goals include speed, data integrity, and support for distributed, non-linear workflows.
<!--more-->
## Add a git configuration

To add/alter a git configuration setting use the following command:

```sh
# git config [options] <configuration.item> <value>
git config --global http.sslverify true
```

You can make the configuration change global by using the `--global` option.

## Shallow clone

Use `git clone --depth=1 <url>` to do a shallow clone. 
These clones truncate the commit history to reduce the clone size. 
This creates some unexpected behavior issues, limiting which Git commands are possible. 
These clones also put undue stress on later fetches, so they are **strongly discouraged for developer use** however they are 
**helpful for some build environments** where the repository will be deleted after a single build.

See [this post](https://github.blog/2020-12-21-get-up-to-speed-with-partial-clone-and-shallow-clone/) for more information.

## Showing progress

`git clone --progress --verbose .....`

## Add a remote

To add a remote for the given git project use:

```sh
# git remote add [-t <branch>] [-m <master>] [-f] [--[no-]tags] [--mirror=(fetch|push)] <name> <url>
git remote add gogs https://gogs.example.org/repoGroup/project.git
```

## Changing upstream for a branch

To change the default upstream that a particular branch is set to:

```sh
# git push -u <remote_name> <local_branch_name>
git push -u gogs main
```

## Submodules

[Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) allow you to keep a Git repository as a subdirectory 
of another Git repository. This lets you clone another repository into your project and keep your commits separate.

### Adding a submodule

```shell
git submodule add https://github.com/chaconinc/DbConnector
```

### Cloning a Project with Submodules

When you clone a project which has submodules, by default you get the directories that contain submodules, 
but none of the files within them yet.

You can override this behaviour and use `--recurse-submodules` when cloning the project. This will clone the main project
and all the submodules. For example:

```shell
git clone --recurse-submodules https://github.com/chaconinc/MainProject
```

#### Populate (pull) contents of submodule

If you have cloned a git repo without using the `--recurse-submodules` you will need to run some addition commands to
pull in the contents of the submodules.

```shell
git submodule init
git submodule update
```

## Handy Commands

| Command                             | Description                                      |
| ----------------------------------- | ------------------------------------------------ |
| `git remote -v`                     | Prints the remotes for this git repo             |
| `git branch -vv`                    | Prints the remotes associated with each branch   |
| `git commit --amend --reset-author` | Reset the author information on your last commit |
| `git config http.sslverify false` | Set the current project to disable ssl certificate checks, handy for self hosted repos | 

## Troubleshooting

### git checkout/pull returns 'error: invalid path'

This is caused by git complaining about invalid paths, typically encountered on Windows.

The workaround is to set the git config flag: `git config core.protectNTFS false`

    Note: core.protectNTFS

    If set to true, do not allow checkout of paths that
    would cause problems with the NTFS filesystem, 
    e.g. conflict with 8.3 "short" names.
    Defaults to true on Windows, and false elsewhere.
