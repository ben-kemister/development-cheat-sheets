---
title: xargs
tags:
 - unix
 - linux
---

[xargs](https://man7.org/linux/man-pages/man1/xargs.1.html) is a command on Unix and most Unix-like operating systems 
used to build and execute commands from standard input. 
<!--more-->
It converts input from standard input into arguments to a command. 
Some commands such as grep and awk can take input either as command-line arguments or from the standard input.

## Running a piped in command/script

```shell
ls -1 /tmp/some_script-* | xargs -I {} sh {} -i script_args
```