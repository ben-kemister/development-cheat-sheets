---
title: "Windows Package Manager (Winget)"
date: 2026-11-15T08:45:00+11:00
draft: false
tags: ["windows", "winget", "cli", "package-manager", "automation"]
---

An in-depth guide to Microsoft's **Winget** CLI tool, covering what it is, installation methods, core commands, 
security considerations, and comparisons with Chocolatey and Scoop.
<!--more-->

## Overview

**Winget** (Windows Package Manager) is Microsoft’s official, open-source command-line tool designed to streamline 
software discovery, installation, upgrading, and removal on Windows 10 and Windows 11. 

Operating similarly to package managers like `apt` on Ubuntu or `brew` on macOS, it frees developers and system 
administrators from hunting down application installers across the web.

---

## Installation & Prerequisites

Winget is distributed via the **App Installer** package and comes pre-installed on modern versions of Windows 10 and Windows 11.

If it is missing or needs to be updated:
1. **Microsoft Store:** Open the Microsoft Store app, search for **App Installer**, and click **Get / Update**.
2. **Manual Installation:** Download the latest `.msixbundle` release directly from the official GitHub repository.

---

## Core Usage & Commands

Open **Windows Terminal**, **PowerShell**, or **Command Prompt** to run Winget commands:

### Search for an application

```powershell
winget search <app-name>
```
(Example: `winget search powertoys` — inspect the Id column to target the correct package).

### Install and application

```powershell
winget install --id <Package.Id> -e
```
(Example: `winget install --id Microsoft.PowerToys -e`)

### Upgrade all installed applications

```powershell
winget upgrade --all
```

### Uninstall an application

```powershell
winget uninstall --id <Package.Id> -e
```

### Export and Import app configurations (ideal for provisioning new machines)

```powershell
winget export -o apps.json
winget install -m apps.json
```

## Security Model & Safety

Winget is designed with built-in safety mechanisms:

* **Manifests & Hashing**: The default community repository (`winget-pkgs`) stores YAML manifests referencing vendor installers. 
    Every install command validates a SHA-256 hash of the downloaded file against the manifest, preventing tampering in transit.
* **SmartScreen Scans**: Submissions undergo automated scans via Microsoft Defender SmartScreen.
* **Best Practice**: Always verify the Id and Publisher fields before executing third-party package installations.


## Package Manager Comparison: Winget vs. Chocolatey vs. Scoop

| Feature | Winget | Chocolatey | Scoop |
| --- | --- | --- | --- | 
| **Maintainer** | Microsoft (Official) |  Chocolatey Software, Inc. | Open-source community | 
| **Pre-installed** | Yes (Windows 10/11) | No | No |
| **Default Permissions** | Mixed (User or Admin, per app) | Admin required by default | Standard User (No Admin) |
| **Best for** | General GUI apps & OS provisioning | Enterprise fleets & legacy software | CLI tools, runtimes, and developers |

## External Resources & Further Reading

* [Official Microsoft WinGet Documentation](https://learn.microsoft.com/en-us/windows/package-manager/)
* [WinGet CLI GitHub Repository (microsoft/winget-cli)](https://github.com/microsoft/winget-cli)
* [WinGet Community Package Manifests (microsoft/winget-pkgs)](https://github.com/microsoft/winget-pkgs)
