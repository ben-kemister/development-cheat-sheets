---
title: "Loops"
tags:
- sh
- bash
- linux
- terminal
- scripts
- scripting
---

Information about loops in linux.
<!--more-->

## While loops

You can create a simple single line while loop using `while true; do foo; sleep 2; done`

```shell
# Loop to keep printing a console output
while true; do echo "Still alive at: $(date)"; sleep 60; done
```

## For loops

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

## Skipping iterations

To skip the current iteration in a loop and move directly to the next one use the `continue` statement.

```shell
for i in {1..5}; do
    if [ "$i" -eq 3 ]; then
        continue # Skips the rest of the loop for number 3
    fi 
    echo "Number: $i"
done
```
