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

### Key Variables for Arguments

| Variable        | Description                                                                                                                                                         |
|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `$@`            | Represents all arguments. When quoted as `$@`, it preserves spaces within individual arguments.                                                                     |
| `$*`            | Represents all arguments as a single string. If quoted as `$*`, all arguments are merged into one string separated by the first character of IFS (usually a space). |
| `$#`            | The total number of arguments passed to the script.                                                                                                                 |
| `$0`            | The name of the script itself (not included in `$@` or `$*`).                                                                                                       |
| `$1`, `$2`, ... | Individual positional parameters (1st, 2nd, etc.).                                                                                                                  |

### Individual arguments

From inside the script you can use the value of this argument with `$n` with `n` being the position of the argument.

```shell
echo "Argument 1 = $1"
echo "Argument 2 = $2"
```

### All Arguments

To output using Double Quotes, use single quotes to define the format, placing literal double quotes around the `%s` placeholder:
```shell
printf '"%s" ' "$@"
# Output: "apple" "banana bread" "cherry"
```

To output using Single Quotes, use double quotes to define the format, placing literal single quotes around the `%s`:
```shell
printf "'%s' " "$@"
# Output: 'apple' 'banana bread' 'cherry'
```

> Pro Tip: Adding a Final Newline    
> Because `printf` doesn't automatically add a newline at the very end of its execution, your terminal prompt might appear immediately after the last argument.    
> To fix this, add an empty `echo` or `printf '\n'` after the command: `printf '"%s" ' "$@"; echo`

