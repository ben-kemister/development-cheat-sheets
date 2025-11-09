---
title: (git) ignore
tags:
- git
- config
- gitignore
---

The `.gitignore` file is a plain text file used in Git-based projects to specify which files and directories should be ignored by Git. 
<!--more-->
This means that files and patterns listed in `.gitignore` are not tracked by version control; 
they will not show up as untracked files, be added, or committed to the repository.


## Global `gitignore`

A global `.gitignore` file allows users to specify files and directories that Git should ignore across all repositories 
on their system. 

This is particularly useful for ignoring files specific to one's local development environment, 
such as IDE configuration files, operating system generated files/folders (e.g. `/.idea/`, `Thumbs.db`), or 
temporary build artifacts, without needing to add them to every project's local `.gitignore`.

### Setup

Create a new file in a location of your choice, typically in your home directory, and name it something like `.gitignore_global`.
```shell
touch ~/.gitignore_global
```

Add patterns to the global `.gitignore_global` file for files and directories you wish to ignore globally. For example:
```text
# OS generated files
Thumbs.db

# Intellij files and folders
/.idea/
```

Configure Git to use the global `.gitignore_global` file:
```shell
git config --global core.excludesfile ~/.gitignore_global
```

