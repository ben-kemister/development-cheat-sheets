---
title: uname (Unix Name)
date: "2026-08-15"
tags: [Linux, Command Line, System Administration, Kernel, Unix]
---

`uname` is a standard Unix/Linux utility used to print detailed system information, including the kernel version, 
architecture, and operating system name. 
<!--more-->

## Core Functionality

The `uname` (Unix Name) command provides a snapshot of the current system's runtime environment. It is essential for 
identifying kernel releases, machine architecture, and operating system details.

## Common Command Options
| Flag | Description                                               | Example Output                  |
|:-----|:----------------------------------------------------------|:--------------------------------|
| `-a` | Prints all available system information in a single line. | `Linux host 5.15.0-generic ...` |
| `-s` | Displays the kernel name.                                 | `Linux`                         |
| `-n` | Shows the network node hostname.                          | `my-laptop`                     |
| `-r` | Outputs the specific kernel release version.              | `5.15.0-generic`                |
| `-v` | Reveals the kernel version including build timestamps.    | `#1~22.04.1-Ubuntu SMP...`      |
| `-m` | Identifies the hardware machine architecture.             | `x86_64`                        |
| `-o` | Prints the name of the operating system.                  | `GNU/Linux`                     |

## Practical Usage Examples

**Checking the Kernel Release**
To quickly identify the running kernel version for compatibility checks:
```bash
uname -r
```

**Comprehensive System Audit**
To get a full overview of the system identity and architecture:
```bash
uname -a
```

## Alternative Methods for Kernel Information

While `uname` is the standard, other methods exist depending on the distribution:
* **`cat /proc/version`**: Provides detailed compilation info, including the GCC version used.
* **`hostnamectl | grep Kernel`**: Useful on systemd-based distributions to view running kernel details.

## Handy Links

* [Linux Kernel Documentation](https://www.kernel.org/)
* [GNU Coreutils Manual](https://www.gnu.org/software/coreutils/)

