---
title: Linux
layout: page_with_tags
tags:
    - operating_system
    - linux
---

The Linux Debian operating system is used as the base for many other linux operating systems such as Ubuntu and the Raspberry Pi OS (formerly Raspbian).
<!--more-->

## What version of Linux am I running?

To find out what OS version you are running use the following command:

``` sh
cat /etc/os-release
```

## Change the hostname

For more information on changing the hostname see [this webpage](https://phoenixnap.com/kb/linux-hostname-command).

### Temporary change

In the terminal, type the following, replacing *new-hostname* with the name you choose:

```sh
sudo hostname new-hostname
```

### Permanent change

Use `set-hostname` to Change the Hostname, type the following command:

```sh
hostnamectl set-hostname new-hostname
```
Use your own hostname choice instead of *new-hostname*.

You can confirm the change using `hostnamectl` and checking the output.

## Environmental Variables

Environment variables are variables that contain values necessary to set up a shell environment. Contrary to shell variables, environment variables persist in the shell’s child processes.

[How to link](https://phoenixnap.com/kb/linux-set-environment-variable)

### How to Check Environment Variables

View All Environment Variables
Use the printenv command to view all environment variables. Since there are many variables on the list, use the less command to control the view:

```sh
printenv | less
```

### Set a Shell Variable in Linux

The simplest way to set a variable using the command line is to type its name followed by a value:

```sh
[VARIABLE_NAME]=[variable_value]
```

   Note: variables created in this way is a shell variable. It will not persist or be passed to child processes.

### How to Export an Environment Variable

If you want to turn a shell variable into an environment variable, return to the parent shell and export it with the export command:

```sh
export [VARIABLE_NAME]
```

The environment variable created in this way disappears after you exit the current shell session.

### Set an Environment Variable in Linux Permanently

If you wish a variable to persist after you close the shell session, you need to set it as an environmental variable permanently. You can choose between setting it for the current user or all users.

#### To set permanent environment variables for a single user

1. edit the .bashrc file `sudo nano ~/.bashrc`
2. Write a line for each variable you wish to add using the following syntax:

```sh
export [VARIABLE_NAME]=[variable_value]
```

3. Save and exit the file. The changes are applied after you restart the shell. If you want to apply the changes during the current session, use the source command: `source ~/.bashrc`

#### To set permanent environment variables for all users

1. create an .sh file in the /etc/profile.d folder

```sh
sudo nano /etc/profile.d/[filename].sh
```

2. Write a line for each variable you wish to add using the following syntax:

```sh
export [VARIABLE_NAME]=[variable_value]
```

1. Save and exit the file. The changes are applied **at the next login**.

## cgroups

[cgroups](https://www.redhat.com/sysadmin/cgroups-part-one) is a Linux kernel feature that limits, accounts for, and isolates the resource usage of a collection of processes. 
Engineers at Google started the work on this feature in 2006 under the name "process containers"

## Services

### systemctl

Services can be controlled and viewed using `systemctl` for example:

| Command                                                                                | Description                   |
|----------------------------------------------------------------------------------------|-------------------------------|
| `sudo systemctl status <SERVICE_NAME>` <br> `sudo systemctl status pihole-FTL.service` | Print the status of a service |
| `sudo systemctl stop <SERVICE_NAME>`                                                   | Stop a service                |
| `sudo systemctl start <SERVICE_NAME>`                                                  | Start a service               |
| `sudo systemctl restart <SERVICE_NAME>`                                                | Restart a service             |

