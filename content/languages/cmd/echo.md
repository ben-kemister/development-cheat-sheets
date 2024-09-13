---
title: echo
tags: 
 - windows
 - terminal
 - cmd
---

[echo](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/echo) displays messages or turns 
on or off the command echoing feature.
<!--more-->

## Echo a blank line

Any of the below three options will echo a blank line:

* `echo[`
* `echo(`
* `echo.`

For example:

```powershell
@echo off
echo There will be a blank line below
echo[
echo Above line is blank
echo(
echo The above line is also blank.
echo.
echo The above line is also blank.
```