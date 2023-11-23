---
title: PowerShell
tags:
 - scripting
 - windows
 - powershell
 - terminal
---

[PowerShell](https://learn.microsoft.com/en-au/powershell/) is a task automation and configuration management framework from Microsoft, 
consisting of a command-line shell and associated scripting language.
<!--more-->

## Multi-line commands

You can use the backtick character **`** to run commands across multiple lines in PowerShell.

```shell
date; `
hostname
```

## Stream input (piped from another command)

``` cmd
git config -l | Select-String -Pattern 'email'
```

## Open a file

You can use the command `Invoke-Item` or the shortcut version of `ii` to open a file. For example:
```powershell
ii index.html
```

