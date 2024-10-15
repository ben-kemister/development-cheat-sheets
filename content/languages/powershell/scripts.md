---
title: Scripts (ps1 files)
tags:
 - scripting
 - scripts
 - powershell
 - ps1
---

A [script](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_scripts) is a plain 
text file that contains one or more PowerShell commands. PowerShell scripts have a `.ps1` file extension.
<!--more-->
Running a script is a lot like running a cmdlet. You type the path and file name of the script and use parameters to submit data and set options. 
You can run scripts on your computer or in a remote session on a different computer.

## Enable execution of PowerShell scripts

> Only members of the Administrators group on the computer can change the execution policy.

1. Start Windows PowerShell with the "Run as Administrator" option. 
2. Enable running unsigned scripts by entering:
    ```powershell
    set-executionpolicy remotesigned
    ```

This will allow running unsigned scripts that you write on your local computer and signed scripts from Internet.
For more information see: [How to allow scripts to run](https://learn.microsoft.com/en-us/previous-versions//bb613481(v=vs.85)?redirectedfrom=MSDN#how-to-allow-scripts-to-run)

## Block Comments

To use block/multiline comments in a script enclose them in `<#` and `#>`. 
For example:

```powershell
<#
   Lots of multi line comments
   go here...
#>
```

You can use the [comment base help](https://learn.microsoft.com/en-us/powershell/scripting/developer/help/syntax-of-comment-based-help) 
function of powershell to give more context. 
For example:

```powershell
<#
    .Description
    The Get-Function function displays the name and syntax of all functions in the session.
#>
```
