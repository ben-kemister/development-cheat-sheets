---
title: krew
tags:
 - container
 - kubernetes
 - kubectl
---

[Krew](https://krew.sigs.k8s.io/) is the plugin manager for kubectl command-line tool.
<!--more-->

## Handy plugins

### oidc-logon plugin

Adds OpenID Connect (OIDC) capabilities to kubectl, see: [kubelogin GitHub repo](https://github.com/int128/kubelogin?tab=readme-ov-file)

To install in Windows; open an **Admin** PowerShell window and run `kubectl oidc-login` 

## Troubleshooting

### failed to retrieve plugin indexes

When running `kubectl krew update` you get an error message like:
```shell
failed to retrieve plugin indexes: failed to list the remote URL for index default: command execution failure, output="": exit status 1
```

This is caused by git, which is used by krew, not trusting the `~\.krew\index\default` directory 
(re: [stackoverflow post](https://stackoverflow.com/questions/78193875/failed-to-install-krew-failed-to-list-the-remote-url-for-index-default)).

To fix:

```powershell
cd .\.krew\index\default\

# Check/verify issue
git status

# Fix
git config --global --add safe.directory C:/Users/benke/.krew/index/default
```

## Links & References

* [Windows Install - Krew](https://krew.sigs.k8s.io/docs/user-guide/setup/install/#windows)