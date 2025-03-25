---
title: remotes
tags:
- git
- remotes
---

[Git Remotes](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes) allow you to collaborate on a Git project. 
Remote repositories are versions of your project that are hosted on the Internet or network somewhere.
<!--more-->

## Add a remote

To add a remote for the given git project use:

```sh
# git remote add [-t <branch>] [-m <master>] [-f] [--[no-]tags] [--mirror=(fetch|push)] <name> <url>
git remote add gogs https://gogs.example.org/repoGroup/project.git
```

## Changing a remote ULR

First check the current remote(s) with:

```shell
git remote -v
```
```text
origin  https://github.com/my-project/my-repo.git (fetch)
origin  https://github.com/my-project/my-repo.git (push)
```

Then you can set/change the URL for a remote with:
```shell
git remote set-url <REMOTE_NAME> <NEW_GIT_URL>
```

For example:
```shell
git remote set-url origin https://github.com/new-project/new-repo.git
```