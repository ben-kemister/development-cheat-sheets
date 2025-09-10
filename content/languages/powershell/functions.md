---
title: Functions
tags:
- scripting
- windows
- powershell
- functions
---

This page provides examples about creating and using functions in PowerShell.
<!--more-->

## Function with parameter

```powershell
function My-Function() {
    param (
        [string]$Name
    )
    Write-Host "The paramter value is: $Name"
}
```

### With default value

```powershell
function My-Function() {
    param (
        [bool]$IsOn=$True
    )
    Write-Host "The paramter value is: $IsOn"
}
```