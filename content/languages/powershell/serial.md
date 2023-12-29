---
title: Serial Port
tags:
- powershell
- serial
- port
---

You can use PowerShell to send and receive information over a serial port.
<!--more-->

## List of available com ports

Use command below to list the available COM ports:

```powershell
[System.IO.Ports.SerialPort]::getportnames()
#COM3
```

## Create (and use) a port instance

```powershell
$port= new-Object System.IO.Ports.SerialPort COM#,Baudrate,None,8,one

$port.open()
$port.WriteLine("some string")
$port.ReadLine()
$port.Close()
```