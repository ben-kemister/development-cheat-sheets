---
title: Strings (PowerShell)
tags:
 - scripting
 - windows
 - powershell
 - terminal
 - strings
---

This page provides examples about the use of PowerShell **strings** and **string functions.**
<!--more-->

## Select-String (i.e. Grep for Powershell)

The `Select-String` command is the PowerShell equivalent to `grep` in linux.

Examples:
```powershell
# Select 2 lines before and 3 after the match
Select-String -Pattern "match" -Context 2,3

# From a file input
Select-String -Path "Users\*.csv" -Pattern "Joe"
```

### -NotMatch

You can also use the `-NotMatch` flag to return the strings that do not match the pattern.
```powershell
Select-String -Pattern "#" -NotMatch
```

See [here](https://adamtheautomator.com/powershell-grep/) for more information and examples.

## Concatenation

The `+` operator is used to concatenate strings in Powershell, for example:

```powershell
$firstName = "Shell"
$lastName = "Geek"

$fullName = $firstName + $lastName

Write-Host "Concatenate String:"
$fullName
```
For more information see [shellgeek.com](https://shellgeek.com/powershell-concatenate-string/)

## Replace

There are a few methods of String replacement

### Using the Replace() Method

```powershell
$string = 'hello, world'
PS> $string.replace('hello','hi')
hi, world
```

### Using the PowerShell Replace Operator

```powershell
$string = 'hello, world'
PS> $string -replace 'hello','hi'
hi, world
```

## Null or Whitespace check

```powershell
if (![string]::IsNullOrWhiteSpace($MyVariable)) {
  Write-Host "Variable is null or whitespace"
} else {
  Write-Host "Variable has a value of: $MyVariable"
}
```

## StartsWith

```powershell
if ($MyVariable.StartsWith("#")) {
  Write-Host "String starts with '#'"
}
```