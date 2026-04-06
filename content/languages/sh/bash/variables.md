---
title: Variables
tags:
- bash
- script
- variables
---

This page contains information and examples about Bash variables.
<!--more-->

## Variable Defaults

| Syntax            | Behavior	                                            | Effect on Variable                             |
|-------------------|------------------------------------------------------|------------------------------------------------|
| `${VAR:-default}` | Use `default` if `VAR` is unset or null.             | `VAR` stays unset/null.                        |
| `${VAR:=default}` | Assign `default` to `VAR` if it's unset or null.     | `VAR` is now set to default.                   |
| `${VAR-default}`  | Use `default` only if `VAR` is unset.                | `VAR` stays unset (Empty string is kept as-is) |
| `${VAR:?error}`   | Exit with `error` message if `VAR` is unset or null. | Script stops                                   |

The most robust methods use the `${parameter:-word}` or `${parameter:=word}` syntax

### Temporary Fallback (`:-`)

Use the default value only if the variable is unset or empty. The original variable remains unchanged.
```shell
# If NAME is empty or unset, "Guest" is used.
echo "Hello, ${NAME:-Guest}"
```

### Permanent Assignment (`:=`)

Assign the default value to the variable if it is currently unset or empty.
```shell
# If DB_USER is empty, it becomes "admin" permanently.
: "${DB_USER:=admin}"
echo "$DB_USER" # Outputs "admin"
```
> Note: The `:` (colon) is a "no-op" command used to evaluate the expression without trying to execute the resulting string.






