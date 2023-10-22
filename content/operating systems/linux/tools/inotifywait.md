---
title: inotifywait
tags:
 - unix
 - linux
 - files
---

[inotifywait](https://linux.die.net/man/1/inotifywait) a linux tool that waits for changes to files using `inotify` 
<!--more-->

## Watch 

Below is a single line command which will print out the details of the first file for the give events.

```shell
inotifywait -r -e modify -e create -e delete -e move -q --format '%e %w%f' /source/ \
| awk '{ print "Detected " $1 " event(s) on file: " $2 }')
```

