---
title: User Prompts
tags:
 - scripting
 - scripts
 - powershell
 - ps1
 - prompts
 - pause
---

This page contains information about implementing various user prompts within PowerShell scripts.
<!--more-->

## Wait for user (pause)

To simply wait for **any key** to be pressed before continuing the script, use the following:

```powershell
Read-Host -Prompt "Press any key to continue"
```

## Specific User input

A simple way to check for a specific input from a user is:

```powershell
$continue = Read-Host -Prompt "Continue? [y/n]"

if ( $continue -eq 'n' ) { 
    Exit
}
```

Or a more restrictive way which only accepts specific inputs from the user is:

```powershell
$title    = 'Question'
$question = 'Are you sure you want to continue?'
$choices  = '&Yes', '&No'

$decision = $Host.UI.PromptForChoice($title, $question, $choices, 1)
if ($decision -eq 0) {
    Write-Host 'confirmed'
} else {
    Write-Host 'cancelled'
    Exit
}
```