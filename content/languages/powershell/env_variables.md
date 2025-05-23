---
title: Environment variables
tags:
- scripting
- windows
- powershell
- terminal
- variables
---

This page provides examples about the use of environment variables in PowerShell.
<!--more-->

## Environmental Variables

Environment variables are global settings for your Linux, Mac, or Windows computer, stored for the system shell to use when executing commands. For more information see [HowTo: Set an Environment Variable in Windows - Command Line and Registry](http://www.dowdandassociates.com/blog/content/howto-set-an-environment-variable-in-windows-command-line-and-registry/).

### Standard Environmental Variables

The table below lists some of the standard/common Windows environmental variables: 

| Variable            | Description                             |
|---------------------|-----------------------------------------|
| `$Env:UserName`     | Returns the current username in Windows |
| `$Env:ComputerName` | Returns the current computer name       |

### Print Environmental Variables

To list all environment variables.

```shell
Get-ChildItem Env:
``

To print a particular environment variable:

```shell
echo $Env:ProgramFiles
C:\Program Files
```

### *Temporary* variables

You can set a *temporary* environmental variables using the following command:

```shell
    PS > $Env:FOO = "hello world"
    PS > $Env:FOO
    hello world
```

YOu can Remove/Clear a *temporary* variable

```shell
    PS > $env:SLS_DEBUG = ""
    PS > $Env:SLS_DEBUG
    PS >
```

### *User* variables

You can set user environmental variables using the following command:

```shell
    PS > setx GLOBAL_AGENT_HTTP_PROXY 'http://userId:password@proxy:8080'
    PS > $Env:GLOBAL_AGENT_HTTP_PROXY
    http://userId:password@proxy:8080
```