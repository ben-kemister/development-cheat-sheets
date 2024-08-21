---
title: Processes
tags:
- linux
- processes
---


A process in Linux starts every time you launch an application or run a command. 
<!--more-->
While each command creates one process, applications can create and run multiple processes for different tasks. 
By default, each new process starts as a foreground process. This means it must finish before a new process begins.

## PIDs

The Process ID (PID) is a unique identifier assigned to each process running in a Linux operating system.

Although PIDs can be recycled after a process is complete, at any point of time, no two processes with the same PID exist in the system.

## PS

`ps` (process status) displays information about a selection of the active processes.

Handy Flags
* `-e` - list all processes
* `-f` - detailed output

### List all processes

```shell
ps -e
```

## pgrep - Finding a particular process

`pgrep` is a simple command for returning process information through a partial pattern. 
For instance, the command uses a regular expression (a*) as the wildcard pattern. 

The syntax of this command is: `pgrep <flags> <pattern>`

You can use the following flags with the `pgrep` command:

* `-l` - Lists the PIDs and the names of the processes.
* `-n` - Returns the most recent process.
* `-o` - Returns the oldest process.
* `-u` - Returns the processes associated with a particular user.
* `-x` - Returns the processes that precisely fit the specified pattern.

Examples:

```shell
handbrake-5f5df9cf85-67bww:/tmp# ps
PID   USER     TIME  COMMAND
    ...
 1011 app       0:04 /usr/bin/ghb --config /config
    ...
    
handbrake-5f5df9cf85-67bww:/tmp# pgrep ghb
1011
```

## Ending a process

A user might need to use the Linux kill process for various reasons, however you should carefully use the kill process 
command because it may result in system instability or even a crash if you terminate a crucial system process.

During the process, the system sends a termination message to the process associated with the PID. 
The termination signal process can be one of the following:

* `SIGKILL`: This is the most effective Linux kill process method. Note that SIGKILL invariably results in an abrupt termination of all child processes. This could result in data loss because it leaves the process in an uncontrolled state.
* `SIGTERM`: Unlike `SIGKILL`, `SIGTERM` offers a more delicate way of killing a process. It can perform certain actions before exiting.

Out of these two, `SIGKILL` is usually the quickest and most efficient way to end the child processes. 
However, `SIGTERM` is recommended as it allows you to save the signal’s state and carry out cleanup activities.

### pkill

The `pkill` command is similar to the `pgrep` command in that it will kill a process after finding the process based on specific qualifying criteria. 

The command issues a `SIGTERM` signal by default.

The syntax of the command is: `pkill <flags> <pattern>`

You can use the following flags with the `pkill` command:

* `-n` - Only terminates the most recent process it finds.
* `-o` - Only terminates the oldest processes.
* `-u` - Only terminates the processes that belong to a specific user.
* `-x` - Only terminates the processes that precisely fit the mentioned pattern.
* (signal) - Instead of `SIGTERM`, send a different signal to the process.

### kill

If you know the process id, use the `kill` command to terminate the process. 

The syntax of this command is: `kill <processID>`

The `kill` command ends the specified processes one at a time. It notifies a process to stop by sending out a `SIGTERM` signal. 
It waits for the shutdown process of the program to complete.

You can send a different signal with the `-signal` flag.

#### kill -9/-SIGKILL

The `kill -9-SIGKILL` command is useful when you need to kill an unresponsive service.

You can execute this with either:

* `kill -9 <processID>` or
* `kill -SIGKILL <processID>`

A service that receives the `kill -9/-SIGKILL` command is instructed to stop working **immediately** (with the `SIGKILL` signal)


## References & Links

* [Linux ps Command - baeldung](https://www.baeldung.com/linux/ps-command)
* [ps - man page](https://man7.org/linux/man-pages/man1/ps.1.html)
* [How to Kill a Process in Linux? Commands to Terminate](https://www.redswitches.com/blog/how-to-kill-a-process-in-linux/)