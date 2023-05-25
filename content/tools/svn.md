---
title: SVN
tags:
 - svn
 - version_control
---

[Subversion](https://subversion.apache.org/) (SVN) is an open source version control system developed as a project of the Apache Software Foundation. 

[TortoiseSVN](https://tortoisesvn.net/) is an Apache™ Subversion (SVN)® client, implemented as a Windows shell extension. It's intuitive and easy to use, since it doesn't require the Subversion command line client to run. And it is free to use, even in a commercial environment.
<!--more-->
## Status
Use the [svn status](https://www.visualsvn.com/support/svnbook/ref/svn/c/status/) command to print the status of working copy files and directories.
`svn status [PATH...]`

For example:
```shell
PS> svn status
?   README.md
?   package-lock.json
M   src\App.js
```

```shell
PS> svn --no-ignore status
I   .ideas
?   README.md
I   node_modules
?   package-lock.json
M   src\App.js
```

## Viewing properties
You can view the properties on a particular folder, file or revsision using the [snv proplist](https://www.visualsvn.com/support/svnbook/ref/svn/c/proplist/) command:
`svn proplist [options] [target]`

For example:
* `snv proplist -v .` - list the properties for the current folder

## Setting a property
You can set a property using the [svn propset](https://www.visualsvn.com/support/svnbook/ref/svn/c/propset/) command:
`svn propset PROPNAME [PROPVAL | -F VALFILE] PATH`

For example:
* `svn propset svn:gloabl-ignores -F .\ignore.txt .` - will globally ignore the files and folders listed in the `.\ignore.txt` file

## Editing properties
You can use the [svn propedit](https://www.visualsvn.com/support/svnbook/ref/svn/c/propedit/) command to edit properties.
`svn propedit PROPNAME TARGET...`

For example:
* `svn propedit svn:gloabl-ignores --editor-cmd notepad .` - Uses the Windows notepad app to edit the contents of the `svn:gloabl-ignores` property.

## Ignoring files
There are several ways to ignore files in SVN, depending on which version you are using. 
Below is a simple example of ignoring a folder (and its contents) for a recent version (1.8 and higher) of SVN:

Examples:
* `svn propset svn:gloabl-ignores node_modules .` - will globally ignore the `node_modules` folder and its contents.

