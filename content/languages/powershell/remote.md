---
title: Remote PS
tags:
 - scripting
 - windows
 - powershell
 - terminal
 - remote
---

This page provides information about the use of using PowerShell's remote features.
<!--more-->

## Open a remote PS Session

```powershell
Enter-PSSession -ComputerName remote-windows.example.com -Credential domain\userid
```

## Run a Command on a remote computer

```powershell
Invoke-Command -ComputerName remote-windows.example.com `
-ScriptBlock { Stop-Service -Name "MyService" } `
-Credential domain\userid
```