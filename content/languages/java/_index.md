---
title: Java
tags:
- java
- jvm
- properties
---

Java is a general-purpose programming language that is class-based, object-oriented, and designed to have as few implementation dependencies as possible. It is intended to let application developers write once, run anywhere, meaning that compiled Java code can run on all platforms that support Java without the need for recompilation
<!--more-->

## Java Virtual Machine (JVM) options

There is a bunch of options you can add to the command line when run Java which can be very handy.
Below is some of the more common or handy option which I have come across, or have a look at 
[this handy cheat sheet](https://www.jrebel.com/sites/rebel/files/pdfs/cheat-sheet-rebel-jvm-options.pdf) for more.

### Xms and Xmx (Memory allocation)

The flag `Xmx` specifies the **maximum memory** allocation pool for a Java Virtual Machine (JVM), while `Xms` specifies the **initial memory** allocation pool.

This means that your JVM will be started with Xms amount of memory and will be able to use a maximum of Xmx amount of memory. For example, starting a JVM like below will start it with 256 MB of memory and will allow the process to use up to 2048 MB of memory:

``` cmd
java -Xms256m -Xmx2048m
```
The memory flag can also be specified in different sizes, such as kilobytes, megabytes, and so on.
``` cmd
-Xmx1024k
-Xmx512m
-Xmx8g
```

### -Dproperty (System properties)

You can set a Java system property by using the `-D<property>` syntax, for example:

```shell
# Sets the 'name' system property to 'HelloWorld'
-Dname=HelloWorld

# The property can be retrieved at runtime using
System.getProperty("name")
//HelloWorld
```

## Finding a class within *.jar files

You can use the `jar ef some.jar` command with some PowerShell commands to effectively `grep` through the results to 
find a class within a folder full of jar files:

```powershell
foreach( $jar in Get-Children .\*.jar)
{
    jar tf $jar | Select-String 'com.example.Utils'.replace('.', "/")
}
```

## Sub Pages

{{% children sort="title" %}}