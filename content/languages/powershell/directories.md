---
title: Directory commands
tags:
- scripting
- windows
- powershell
- terminal
- utilities
- file
- directory
---

This page provides examples about the use of PowerShell **path** and **directory** commands.
<!--more-->

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