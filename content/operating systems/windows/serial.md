---
title: Serial (Ports & Terminals)
tags:
- windows
- serial
- terminal
---

This page contains information for dealing with serial ports and terminals in Windows.
<!--more-->

## List of available com ports

You can use the [PowerShell](../../languages/powershell) command below to list the available COM ports:

```powershell
[System.IO.Ports.SerialPort]::getportnames()
#COM3
```

## Serial Terminals

**PuTTY** can be used as a simple serial port terminal, just set the _Serial line_ and _Speed_ fields as required.

