---
title: TypeScript Transpiler
---

This page contains some information about the TypeScript transpiler.

# *tsconfig.json*

The presence of a `tsconfig.json` file in a directory indicates that the directory is the **root** of a TypeScript project. The `tsconfig.json` file specifies the root files and the transpiler options to use when transpiling the project into JavaScript.

## *target* property

Sets the ES version to transpile the TypeScript code to when 

# tsc

`tsc` is the command to start the TypeScript Transpiler.

## Watch mode ( `-w` flag)

You can start the transpiler in watch mode which will watch your files and re-compile them into JavaScript code when it detects changes.

{% highlight powershell %}

    PS > tsc -w
{% endhighlight %}