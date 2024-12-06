---
title: awk
tags:
 - unix
 - linux
---

AWK is a domain-specific language designed for text processing and typically used as a data extraction and reporting tool. 
<!--more-->
Like `sed` and `grep`, it is a filter, and is a standard feature of most Unix-like operating systems.

## Simple output formatting

```shell
> echo "ONE two" | awk '{ print $1, "Something else", $2 }'
ONE Something else two
```

## Use a different Separator

By default, AWK uses a space as a separator. You can change the separator as follows:

```shell
# Use the colon (:) as the separator
> echo "ONE:two" | awk -F ':' '{ print $1 }'
ONE
```

## Convert bytes to GB

You can use ```awk '{ print $1/1024/1024/1024 " GB "}'```.    

For example to convert kb to Gb:

```shell
# Check available space before continuing
SPACE_REMAINING=$(df /watch | tail -n 1 | awk '{ print $3 }')
SPACE_REMAINING_GB=$(echo "$SPACE_REMAINING" | awk '{ print $1/1024/1024 " GB "}' )
echo "$SPACE_REMAINING_GB available space in /watch"
if [ "$SPACE_REMAINING" -lt 10000000 ]; then
  echo "Not enough available space in /watch, exiting..."
  echo ""
  exit 0;
fi
```


