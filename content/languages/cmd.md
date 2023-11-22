---
title: Command Prompt (cmd.exe)
tags: 
 - windows
 - terminal
---

**cmd.exe** is the default command-line interpreter for OS/2, eComStation, Windows, Windows CE, and the ReactOS operating systems. 
<!--more-->
The name refers to its executable filename. It is also commonly referred to as cmd or the Command Prompt, referring to the default window title on Windows.

## type

The `type` command is similar to linux's `cat` command, it can be used to output the contents of a file.
For example:

```shell
>type test.bat
type test.bat
@echo off
echo Content-Type: text/plain
echo.
echo Hello, World!
```

## Environmental Variables

Environment variables are global settings for your Linux, Mac, or Windows computer, stored for the system shell to use when executing commands. For more information see [HowTo: Set an Environment Variable in Windows - Command Line and Registry](http://www.dowdandassociates.com/blog/content/howto-set-an-environment-variable-in-windows-command-line-and-registry/).

### Print Environmental Variables

To list all environment variables.
```shell
C:\> set
```
To print a particular environment variable:
```shell
C:\> echo %JAVA_HOME%
```

