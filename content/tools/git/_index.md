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

## Sub-Pages

{{% children sort="title" description="true" %}}

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


## Changing upstream for a branch

To change the default upstream that a particular branch is set to:

```sh
# git push -u <remote_name> <local_branch_name>
git push -u gogs main
```

## Handy Commands

| Command                             | Description                                      |
| ----------------------------------- | ------------------------------------------------ |
| `git remote -v`                     | Prints the remotes for this git repo             |
| `git branch -vv`                    | Prints the remotes associated with each branch   |
| `git commit --amend --reset-author` | Reset the author information on your last commit |
| `git config http.sslverify false` | Set the current project to disable ssl certificate checks, handy for self hosted repos | 

## Troubleshooting

### Error: Permission denied (publickey)

A "Permission denied" error means that the server rejected your connection. 
There could be several reasons why, including the SSH client used by git not finding/using your SSH key.

To debug turn on verbose SSH logging by either using:

1. Git config: `git config core.sshCommand "ssh -vvv"`, or 
2. An environmental variable: `GIT_SSH_COMMAND="ssh -vvv" git pull`

### git checkout/pull returns 'error: invalid path'

This is caused by git complaining about invalid paths, typically encountered on Windows.

The workaround is to set the git config flag: `git config core.protectNTFS false`

    Note: core.protectNTFS

    If set to true, do not allow checkout of paths that
    would cause problems with the NTFS filesystem, 
    e.g. conflict with 8.3 "short" names.
    Defaults to true on Windows, and false elsewhere.
