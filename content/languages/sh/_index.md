---
title: "sh (Bourne Shell)"
tags:
- sh
- bash
- linux
- terminal
- bourne_shell
---

The Bourne shell `sh` (or the Shell Command Language) is a programming language described by the POSIX standard. 
It has many implementations (ksh88, dash, ...). bash can also be considered an implementation of sh.
<!--more-->
Because `sh` is a **specification**, not an implementation, `/bin/sh` is a symlink (or a hard link) to an actual 
implementation on most POSIX systems.

## What is *bash*
bash started as a sh-compatible implementation (although it predates the POSIX standard by a few years), but as time passed it has acquired many extensions.
Many of these extensions may change the behavior of valid POSIX shell scripts, so by itself bash is not a valid POSIX shell. 
Rather, it is a dialect of the POSIX shell language.

## Command/Topic Specific Pages

{{% children sort="title" description="true" %}}

## Command line Shortcuts (and helpers)

### Jump to a word

`Ctrl + E` - go to the end of the line  
`Ctrl + A` - go to the start of the line  
`Alt + left` - go back one word  
`Alt + right` - go right one word  
`Ctrl + W` - delete the last word  

## Search command history (reverse-i-search)

You can search the command line history by using `Ctrl + R` then entering the search term.

From this prompt `Ctrl + R` will cycle **backwards** and `Ctrl + S` (if supported) will cycle **forwards**.

## History

`history` will display the command line history.

## nohup

The nohup command executes another program specified as its argument and ignores all SIGHUP (hangup) signals. 
SIGHUP is a signal that is sent to a process when its controlling terminal is closed.

To run a command in the background using the nohup command, type:
```shell
$ nohup <command> &

nohup: ignoring input and appending output to 'nohup.out'
```
If you log out or close the terminal, the process is not terminated.

## Exit Codes

In linux a `0` exit status means the command was successful without any errors. 
A non-zero (1-255 values) exit status means command was a failure.

To return the last exit code use the command:

```shell
echo $?
```

## base64

To base64 encode data in Linux, you use the `base64 [FILE]` command. 

For example:

```shell
echo "password" | base64
```
```text
cGFzc3dvcmQK
```

To decode, you use `base64 -d [FILE]` 

For example:

```shell
echo "cGFzc3dvcmQ=" | base64 --decode
```
```text
password
```