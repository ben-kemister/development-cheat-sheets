---
title: "Flow control"
tags:
- sh
- bash
- linux
- terminal
- scripts
- scripting
---

Information about linux script/command flow control.
<!--more-->
In computer science, control flow is the order in which individual statements, instructions or function calls of an imperative program are executed or evaluated. 
The emphasis on explicit control flow distinguishes an **imperative** programming language from a **declarative** programming language

## Loops

### While loops

You can create a simple single line while loop using `while true; do foo; sleep 2; done`

```shell
# Loop to keep printing a console output
while true; do echo "Still alive at: $(date)"; sleep 60; done
```

### For loops

Increment by 1 each loop:

```shell
for i in {1..5}
do
    echo "Welcome $i times"
done
```

Increment by another value:

```shell
# Use the {START..END..INCREMENT syntax}
for i in {0..10..2}
do
    echo "Welcome $i times"
done
```

## Ignoring the exit code of a command

To ignore errors, you can use the `command || true` construct.
This construct allows you to execute a command, and if it returns a non-zero exit status,
the command following the `||` operator (in this case, true) will be executed instead.

```shell
# Create a group, ignoring any errors if a group with the same ID already exists
addgroup -g $PGID -S backup-group || true
```