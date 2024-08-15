---
title: "scripts"
tags:
- sh
- script
- linux
- terminal
---

This page contains information and example for writing sh scripts.
<!--more-->

## Script Arguments

You can pass (positional) arguments to a script using the syntax:

```shell
./my_script.sh my_argument
```

From inside the script you can use the value of this argument with `$n` with `n` being the position of the argument.

```shell
echo "Argument 1 = $1"
echo "Argument 2 = $2"
```