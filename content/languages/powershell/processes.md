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

## Find a running process

Use `Get-Process` to retrieve all running process, or `Get-Process <PROCESS_NAME>` for getting a particular process.

For example:

```shell
PS C:\> Get-Process vlc

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
    791      86   148052     128976      10.63  29596   2 vlc
```

## Kill a process

You can find and kill a particular process using:

```shell
Get-Process vlc | Stop-Process
```

