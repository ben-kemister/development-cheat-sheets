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

## Join-Path

If you need to combine (directory) paths in PowerShell you can use `Join-Path`:

```shell
PS C:\> Join-Path -Path "path" -ChildPath "childpath"

path\childpath
```

For more information see the [PowerShell doco](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/join-path.)

## Remove-Item - Delete File(s)

You can use the `Remove-Item` function to delete files:

```shell
# For a single file
Remove-Item 'D:\temp\Test Folder\test.txt'

# Or a folder and sub-files/folders recursively with
Remove-Item 'D:\temp\Test Folder' -Recurse
```

## Check if a path is a file or folder

```shell
# Check if file (works with files with and without extension)
Test-Path -Path 'C:\Demo\FileWithExtension.txt' -PathType Leaf
Test-Path -Path 'C:\Demo\FileWithoutExtension' -PathType Leaf
 
# Check if folder
Test-Path -Path 'C:\Demo' -PathType Container
```


## Get full path of executing script - $PSScriptRoot

`$PSScriptRoot` is an automatic variable in PowerShell which contains the current file's/module's directory.

## Current Directory Path

`$PWD` (print working directory) in PowerShell gets current path of the current working directory to the standard output.

```shell
PS C:\Dev_Apps\projects\GitHub\development-cheat-sheets> $pwd

Path
----
C:\Dev_Apps\projects\GitHub\development-cheat-sheets
```

## Environmental Variables

Environment variables are global settings for your Linux, Mac, or Windows computer, stored for the system shell to use when executing commands. For more information see [HowTo: Set an Environment Variable in Windows - Command Line and Registry](http://www.dowdandassociates.com/blog/content/howto-set-an-environment-variable-in-windows-command-line-and-registry/).

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
