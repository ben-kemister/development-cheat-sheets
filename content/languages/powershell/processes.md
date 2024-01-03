---
title: "Processes"
date: 2024-01-02T07:35:27+11:00
tags:
- scripting
- windows
- powershell
- terminal
- process
---

This page provides examples about interacting with processes with PowerShell.
<!--more-->

## Running an EXE file

### In the same terminal/process

To run an `exe` file within the same Powershell terminal/process use ``&``, for example:

```powershell
 & "C:\Program Files\TestProgram\TestProgram (debug mode).exe"
```

### In a new terminal/process

```powershell
Start-Process "C:\Program Files\TestProgram\TestProgram (debug mode).exe"
```

This will open a new terminal window where the `exe` file will be run.
