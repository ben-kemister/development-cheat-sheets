---
title: PowerShell
tags:
 - scripting
 - windows
 - powershell
 - terminal
---

[PowerShell](https://learn.microsoft.com/en-au/powershell/) is a task automation and configuration management framework from Microsoft, 
consisting of a command-line shell and associated scripting language.
<!--more-->

## Multi-line commands

You can use the backtick character **`** to run commands across multiple lines in PowerShell.

```shell
date; `
hostname
```

## Stream input (piped from another command)

```cmd
git config -l | Select-String -Pattern 'email'
```

### Using piped input in scriptblocks

Scriptblock and some commands in PowerShell do not accept a pipeline input. A workaround for this is to use 
`ForEach-Object` or its alias `%`.

For example:

```powershell
"World!" | ForEach-Object { Write-Host "Hello $_" }
```
or
```powershell
"World!" | %{ Write-Host "Hello $_" }
```

## Open a file

You can use the command `Invoke-Item` or the shortcut version of `ii` to open a file. For example:
```powershell
ii index.html
```

## Base64 encode/decode

To encode a (UTF8) string use the following:

```powershell
[System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("passw0rd"))
# cGFzc3cwcmQ=
```

To decode use the following:

```powershell
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("cGFzc3cwcmQ="))
# passw0rd
```

You can also use `ForEach-Object` or `%` for a piped input, for example:

```powershell
"cGFzc3dvcmQ=" | %{ [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($_)) }
```