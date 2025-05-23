---
title: Loops
tags:
 - scripting
 - powershell
 - loops
---

This page provides examples about the use of PowerShell **loops**.
<!--more-->

## while

```powershell
while ($True) { Get-Counter '\Processor(_Total\% Processor Time)'} 
```

```powershell
while($val -ne 3){$val++; Write-Host $val}
```

For more information see: [about_While](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_while?view=powershell-7.4)

### Simple linux `watch` alternative

You can use a while loop as an analog to the linux `watch` command.

For example, to continually run `kubectl get nodes` use the following command:
```powershell
watch (1) { cls; date; kubectl get nodes; sleep 5}
```

> Note: `sleep 5` is an alias to the Powershell command: `Start-Sleep -Seconds 5`


## foreach - file input

You can you use combination of `Get-ChildItem` and the `foreach` loop to process a set of files:

```powershell
# Get all sql files...
$files = Get-ChildItem $DataDir\*.sql
foreach ($file in $files) {
    Write-Output 'Processing sql script: ' $file.Fullname
    ## Do something
}
```

## foreach - piped input

You can use `foreach` to iterate over the output piped from another command using the syntax 
`<commands that load up the pipeline> | foreach { <cmdlets that do something with the pipeline contents> }`

A simple example:

```powershell
"Wally",7, 33,"Cloud" | foreach { "Pipeline contains:" + $_ }
```

A more complex example:

```powershell
kubectl get pods -o custom-columns=NAME:metadata.name | Select-String '^nginx' | foreach { "Found Pod Named: " + $_ }
```

## continue statement

The `continue` statement allows you to skip the remaining part of the code, returning you to the beginning of the loop without exiting it.

For example:
```powershell
for ($var = 1; $var -le 5; $var++) {
    if ($var -eq 3) {Continue}
    Write-Host The value of Var is: $var
}
Write-Host End of for loop.
```
Results in:
```txt
The value of Var is: 1
The value of Var is: 2
The value of Var is: 4
The value of Var is: 5
End of for loop.
```
