---
title: Drives
date: 2025-04-05T07:58:18+11:00
tags:
- powershell
- drives
- dvd
---

This page contains information about dealing with Drives (HDD, DVD, CD, etc) in PowerShell.
<!--more-->

## Get List of Drives

```shell
PS C:\> Get-CimInstance Win32_LogicalDisk

DeviceID DriveType ProviderName VolumeName   Size          FreeSpace
-------- --------- ------------ ----------   ----          ---------
C:       3                      Windows-SSD  509722226688  141354569728
D:       3                      Samsung SSD  2000381014016 253009960960
E:       5                      UTOPIA_S2_D1 6038728704    0
```

### Get Drive Letter for Optical Drive

```shell
PS C:\> (Get-CimInstance Win32_LogicalDisk | ?{ $_.DriveType -eq 5}).DeviceID
E:
```

### Eject Optical Drive

```shell
(New-Object -ComObject Shell.Application).Namespace(17).ParseName("E:").InvokeVerb("Eject")
```