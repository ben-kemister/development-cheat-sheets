---
title: objects
tags:
- scripting
- windows
- powershell
---

This page contains information about dealing with objects in PowerShell.
<!--more-->

## Discovering objects, properties, and methods

`Get-Member` provides insight into the objects, properties, and methods associated with PowerShell commands. 
You can pipe **any** PowerShell command that produces object-based output to `Get-Member`. 
When you pipe the output of a command to `Get-Member`, it reveals the structure of the object returned by the command, 
detailing its properties and methods.

* Properties - The attributes of an object.
* Methods - The actions you can perform on an object.

For example:

```shell
PS C:\> Get-Volume -DriveLetter E | Get-Member


   TypeName: Microsoft.Management.Infrastructure.CimInstance#ROOT/Microsoft/Windows/Storage/MSFT_Volume

Name                      MemberType     Definition
----                      ----------     ----------
Clone                     Method         System.Object ICloneable.Clone()
Dispose                   Method         void Dispose(), void IDisposable.Dispose()
Equals                    Method         bool Equals(System.Object obj)
GetCimSessionComputerName Method         string GetCimSessionComputerName()
GetCimSessionInstanceId   Method         guid GetCimSessionInstanceId()
GetHashCode               Method         int GetHashCode()
GetObjectData             Method         void GetObjectData(System.Runtime.Serialization.SerializationInfo info, Sys...
GetType                   Method         type GetType()
ToString                  Method         string ToString()
AllocationUnitSize        Property       uint32 AllocationUnitSize {get;}
DriveLetter               Property       char DriveLetter {get;}
FileSystem                Property       string FileSystem {get;}
FileSystemLabel           Property       string FileSystemLabel {get;set;}
...
```
