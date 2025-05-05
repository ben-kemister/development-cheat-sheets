---
title: scripts
tags:
- windows
- wsl
- scripts
---

This page contains information about how to run (bash) scripts in WSL.
<!--more-->

## Syntax

To run a (bash) script in WSL use the `--exec` or `-e` option:

```shell
wsl -e ./path/to/script.sh
```

## Scripts located in Windows

If you have a bash script on the Windows file system you can run it in a wsl distribution using:    
`wsl [--distribution <DISTRIBUTION_NAME>] -e <path_to_script>`.

For example for a file called `.\basic_script.sh`:
```text
#!/bin/bash
echo "Hello from basic script"
```

You can run it in WSL using:
```shell
PS C:\> wsl -e ./basic_script.sh
Hello from basic script
```

> If your script lacks a shebang line you can invoke it via the shell's executable, e.g. `wsl -e bash ./basic_script.sh`


> **Important**: If you get `ERROR: CreateProcessEntryCommon:502: execvpe ./basic_script.sh failed 8`, 
> it is likely that your .sh file is either lacking a shebang line, the shebang line is malformed, 
> or the file is using the wrong encoding or line endings.
>     
> Make sure the file is UTF-8-encoded without a BOM and uses Unix-format LF-only newlines.

I found this nugget of information here: [How to run a bash script on wsl with powershell?](https://stackoverflow.com/questions/72151630/how-to-run-a-bash-script-on-wsl-with-powershell)

### Passing arguments to the script

For a file called `.\basic_script.sh`:
```text
#!/bin/bash
echo "Hello from basic script"
echo "Script arguments: '$*'"
```

You can run it in WSL passing arguments using:
```shell
PS C:\> wsl -e ./basic_script.sh one two
Hello from basic script
Script arguments: 'one two'
```

## Scripts located in WSL

You can run scripts located in the WSL file system by specifying the full path to the file:

```shell
PS C:\> wsl -e /home/user/scripts/basic.sh
Hello from basic script
```