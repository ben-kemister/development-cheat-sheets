---
title: Environmental Variables (Linux)
tags:
- operating_system
- linux
- variables
- environment
---

This page provides information about the use of **environmental variables** in linux.
<!--more-->
Environment variables are variables that contain values necessary to set up a shell environment. 
Contrary to shell variables, environment variables persist in the shell’s child processes.

## Set an Environment Variable
[How to link](https://phoenixnap.com/kb/linux-set-environment-variable)

## How to Check Environment Variables

View All Environment Variables
Use the printenv command to view all environment variables. Since there are many variables on the list, use the less command to control the view:

```sh
printenv | less
```

## Set a Shell Variable in Linux

The simplest way to set a variable using the command line is to type its name followed by a value:

```sh
[VARIABLE_NAME]=[variable_value]
```

> Note: variables created in this way is a shell variable. It will not persist or be passed to child processes.

## How to Export an Environment Variable

If you want to turn a shell variable into an environment variable, return to the parent shell and export it with the export command:

```sh
export [VARIABLE_NAME]
```

The environment variable created in this way disappears after you exit the current shell session.

## Set an Environment Variable in Linux Permanently

If you wish a variable to persist after you close the shell session, you need to set it as an environmental variable permanently. You can choose between setting it for the current user or all users.

### To set permanent environment variables for a single user

1. edit the .bashrc file `sudo nano ~/.bashrc`
2. Write a line for each variable you wish to add using the following syntax:
    ```sh
    export [VARIABLE_NAME]=[variable_value]
    ```
3. Save and exit the file. The changes are applied after you restart the shell. If you want to apply the changes during the current session, use the source command: `source ~/.bashrc`

### To set permanent environment variables for all users

1. create an .sh file in the /etc/profile.d folder
    ```sh
    sudo nano /etc/profile.d/[filename].sh
    ```
2. Write a line for each variable you wish to add using the following syntax:
    ```sh
    export [VARIABLE_NAME]=[variable_value]
    ```
3. Save and exit the file. The changes are applied **at the next login**.