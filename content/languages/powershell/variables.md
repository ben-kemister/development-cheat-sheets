---
title: Variables
tags:
- scripting
- windows
- powershell
- terminal
- variables
---

This page provides examples about the use of variables in PowerShell scripts.
<!--more-->

## Setting a Variable 

```shell
PS C:\> $My_Variable = "Some Value"
PS C:\> $My_Variable
Some Value
```

## Testing a variable

Test if it matches a given String.
```powershell
PS> $Variable -eq 'some string'
False
```

To test if a variable exists we can compare it to `$null` (`$null` is an automatic variable that essentially represents "does not exist").
```powershell
PS> $Variable -eq $null
True
```