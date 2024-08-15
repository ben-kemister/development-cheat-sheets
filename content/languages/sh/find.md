---
title: "find"
tags:
- linux
- terminal
---

[find]() search for files in a directory hierarchy
<!--more-->
GNU [find](https://man7.org/linux/man-pages/man1/find.1.html) searches the directory tree rooted at each given starting-point
by evaluating the given expression from left to right, according to the rules of precedence (see section OPERATORS), 
until the outcome is known (the left hand side is false for and operations, true for or), at which point find moves on to the next file name.
If no starting-point is specified, `.` is assumed.

## -maxdepth

You can set the search depth of `find` using the `-maxdepth` argument. For example:

```shell
find /media/user/* -maxdepth 0
```

## -printf

You can use the `-printf` argument to change how the output is return. 
For example to only output the file name you can use `-printf "%f\n"`

```shell
find /media/user/* -printf "%f\n"
```

## -newerXY <reference>
          Compares the timestamp of the current file with reference.   The
          reference  argument  is  normally the name of a file (and one of
          its timestamps is used for the comparison) but it may also be  a
          string  describing  an  absolute time.  X and Y are placeholders
          for other letters, and these letters select which time belonging
          to how reference is used for the comparison.

          a   The access time of the file reference
          B   The birth time of the file reference
          c   The inode status change time of reference
          m   The modification time of the file reference
          t   reference is interpreted directly as a time

### Example

Find all of xml files in that have a modification time newer than `2022-03-08`

```sh
find -type f -name *.xml -newermt 2022-03-08
```