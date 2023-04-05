---
title: "Photo Finder Script(s)"
date: 2023-04-03T08:47:49+10:00
draft: false
tags:
 - powershell
 - scripting
 - photos
---

This post contains information about creating various Windows powershell scripts to assist with managing our photos.
<!--more-->

## Do I have these files in another directory?

```powershell
#
# Description: This script tries to locate the files from one directory -InputPath within another -SearchPath
#
# Created: 03 April 2023
#

param (
    [string]$InputPath = $(throw "-InputPath is required"),
    [string]$FileFilter = "*",
    [bool]$MatchSize = $true,
    [bool]$Verbose = $false,
    [string]$SearchPath = $(throw "-SearchPath is required")
)

Write-Host ""
Write-Host "File Finder script started" -ForegroundColor Green
Write-Host ""

$filesToFind = Get-ChildItem -Recurse -File -Filter $FileFilter -Path $InputPath

$message = [string]::Format("Looking for {0} files", $filesToFind.Length)
Write-Host $message
if($MatchSize){
    Write-Host "    with matching file sizes"
}
Write-Host ""

$filesFound = [System.Collections.Generic.List[System.Object]]::new()
$filesNotFound = [System.Collections.Generic.List[System.Object]]::new()


function AddToNotFound {
    param($FileNotFound)

    if($Verbose){
        Write-Host "Cound not file: '$FileNotFound'" -ForegroundColor Yellow
    }
    [void]$filesNotFound.Add($FileNotFound)
}


foreach( $file in $filesToFind){

    $found = Get-ChildItem -Recurse -File -Filter $file -Path $SearchPath
    # $found = Get-ChildItem -Recurse -File -Filter "not.here" -Path $SearchPath

    if(-not $Verbose){
        Write-Host "." -NoNewline
    }

    if($found){

        if( -not $MatchSize){
            if($Verbose) {
                Write-Host "Found file '$file' in"$found.Count"locations: "
                foreach ($matchingFile in $found) {
                    Write-Host "    "$matchingFile.Directory
                }
            }
            [void]$filesFound.Add($found)
        } else {
            # Length matching
            $matchesLenght = $false
            foreach($matchingFile in $found) {

                if( $file.Length -eq $matchingFile.Length){
                    # Matches length
                    $matchesLenght = $true

                    if($Verbose) {
                        Write-Host "Found '$file' in: "$matchingFile.Directory
                    }
                    [void]$filesFound.Add($found)
                }
            }

            if( -not $matchesLenght){
                # Does not match length
                AddToNotFound -FileNotFound $file
            }
        }

    } else {
        AddToNotFound -FileNotFound $file
    }
}
Write-Host ""

# Write-Host "Files to find Length: " $filesToFind.Length
# Write-Host "Files found Length: " $filesFound.Count
# Write-Host "Files not found Length: " $filesNotFound.Count

if($filesToFind.Length -eq $filesFound.Count) {
    Write-Host ""
    $message = [string]::Format("All {0} files found in {1}", $filesToFind.Length, $SearchPath)
    Write-Host $message -ForegroundColor Blue
    Write-Host ""
} else {
    Write-Host ""
    $message = [string]::Format("Only {0}/{1} files found!", $filesFound.Count, $filesToFind.Length)
    Write-Host $message -ForegroundColor Red

    # Missing files
    Write-Host ""
    $message = [string]::Format("Missing {0} files!", $filesNotFound.Count)
    Write-Host $message -ForegroundColor Yellow

    foreach($file in $filesNotFound){
        Write-Host "    $file" -ForegroundColor Yellow
    }
}
```
