---
title: If statement
tags:
 - scripting
 - powershell
 - conditions
---

This page provides examples about the use of PowerShell's 
[if](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_if?view=powershell-7.5) command.
<!--more-->
The use of `if` commands allow you to control the flow of execution of your script based on one or more conditional tests.

## Syntax

```powershell
if (<test1>)
    {<statement list 1>}
[elseif (<test2>)
    {<statement list 2>}]
[else
    {<statement list 3>}]
```

## Logical Operators (`not`, `and`, `or`)

[Logic Operators PowerShell module](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_logical_operators?view=powershell-7.5)

### Not

Logical not (-not) or (!) - Negates the statement that follows.

```powershell
-not (1 -eq 1)             # Result is False
!(1 -eq 1)                 # Result is False
```