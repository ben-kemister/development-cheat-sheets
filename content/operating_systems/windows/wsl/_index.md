---
title: WSL
date: 2025-04-04T15:58:18+11:00
tags:
- windows
- linux
- wsl
- wsl2
---

This page contains information about the use of the [Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/about) 
(WSL).
<!--more-->

## Sub Pages & Topics

{{% children sort="title" %}}

## Basic Commands

| Command                                 | Description                                                                                                                         | 
|-----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| `wsl --version`                         | [Check WSL version](https://learn.microsoft.com/en-us/windows/wsl/basic-commands#check-wsl-version)                                 |
| `wsl -l`                                | [List the installed distributions](https://learn.microsoft.com/en-us/windows/wsl/basic-commands#list-available-linux-distributions) |
| `wsl --set-default <Distribution Name>` | [Set default Linux distribution](https://learn.microsoft.com/en-us/windows/wsl/basic-commands#set-default-linux-distribution)       |
| `wsl bash -c "<bash command string>"`   | [Run a (bash) command](./commands)                                                                                                  |
| `wsl -e <bash_script>`                  | [Execute a script](./script)                                                                                                        |
| `wsl --terminate <Distribution Name>`   | To terminate a specific WSL distribution                                                                                            |

## Matching `wsl` output

Inexplicably, `wsl --list` produces `UTF-16LE`-encoded ("Unicode"-encoded) output.

v0.64 and above of `wsl.exe` (verify via the first line output by `wsl.exe --version`) now supports setting the `WSL_UTF8`
environment variable to 1 to make `wsl.exe` output UTF-8-encoded output, for example

```powershell
$env:WSL_UTF8 = 1
wsl --list --running | Select-String -Pattern MyDistro
```

