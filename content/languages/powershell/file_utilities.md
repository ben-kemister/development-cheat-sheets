---
title: PowerShell file utilities
tags:
- scripting
- windows
- powershell
- terminal
- utilities
- grep
- tail
- head
- file
- directory
- checksum
---

This page provides examples about the use of PowerShell **file utilities**.
<!--more-->

## Get-Content

You can use `Get-Content` (or the alias `gc`) to be able to view the contents of files in the console.

This is particularly useful when dealing with log files.

### Get-Content | Select (head & tail)

By piping the results of `Get-Content` through `select` you can get the equivalent of bash's `head` and `tail` commands.
```powershell
# Head
gc log.txt | select -first 10

# Tail (insce PSv3) and much faster than pipe through select
gc -Tail 10 log.txt

# Tail piping through select
gc log.txt | select -less 10

# Tail with follow
Get-Content log.txt -Tail 10 -Wait
```

### Get-Content | Select-String (grep)

By piping the results of `Get-Content` through `Select-String` you can get the equivalent of bash's `grep` command.
```powershell
# Select 2 lines before and 3 after the match
Get-Content log.txt | Select-String -Pattern "match" -Context 2,3
```

## Get-FileHash

The [Get-FileHash](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/get-filehash) can be used
to calculate the hash value of a file (or string) using a specific hash algorithm.

The acceptable values for the `-Algorithm` parameter are:
* SHA1
* SHA256
* SHA384
* SHA512
* MD5

* If no value is specified, or if the parameter is omitted, the default value is `SHA256`.

For example:
```powershell
Get-FileHash C:\Users\user1\Downloads\7z2201-x64.exe -Algorithm SHA512 | Format-List


Algorithm : SHA512
Hash      : 965D43F06D104BF6707513C459F18AAF8B049F4A043643D720B184ED9F1BB6C929309C51C3991D5AAFF7B9D87031A7248EE32748965
21ABE955D0E49F901AC94
Path      : C:\Users\user1\Downloads\7z2201-x64.exe
```
