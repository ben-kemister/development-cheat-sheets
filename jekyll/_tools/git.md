---
tool: git
name: git
tags:
 - git
 - version_control
--- 

Git  is a distributed version-control system for tracking changes in source code during software development. It is designed for coordinating work among programmers, but it can be used to track changes in any set of files. Its goals include speed, data integrity, and support for distributed, non-linear workflows.
<!--more-->
## Add a git configuration

To add/alter a git configuration setting use the following command:

```sh
# git config [options] <configuration.item> <value>
git config --global http.sslverify true
```

You can make the configuration change global by using the `--global` option.

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
