---
title: Output and Logging
tags:
 - powershell
 - logging
 - output
---

This page provides examples about the use of PowerShell (console) logging and output redirection.
<!--more-->

## Write-Host

```powershell
Write-Host "Starting somthing good..."
```

To output text without a newline in PowerShell using `Write-Host`, use the `-NoNewline` parameter.
For example:
```powershell
Write-Host "This text " -NoNewline
Write-Host "is on the same line."
```
Will output:
```text
This text is on the same line.
```

## Write-Debug

You can use `Write-Debug` to write a debug message to the console.

> By default, debug messages are not displayed in the console, but you can display them by using the **Debug** parameter 
> or the `$DebugPreference` variable. See the [Write-Debug powershell documentation](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/write-debug) 
> for more details.
 
You can set `$DebugPreference = "Continue"` to allow all `Write-Debug` messages in a script to be printed to the console.

## Redirect standard output

You can pipe ``|`` or redirect `>` the output of a command to prevent the output showing in the console.
For example:
```powershell
echo "hello" | out-null
```
or
```powershell
echo "hello" > $null
```