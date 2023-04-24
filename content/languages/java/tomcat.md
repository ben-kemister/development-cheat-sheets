---
title: (Apache) Tomcat
tags:
- java
- tomcat
- webserver
---

Apache Tomcat is a free and open-source implementation of the Jakarta Servlet, Jakarta Expression Language, and WebSocket technologies. 
It provides a "pure Java" HTTP web server environment in which Java code can also run.
<!--more-->
It is a Java web application server, although not a full JEE application server.

## Environmental Variables

The `setenv.[bat|sh]` script can be used to set all environment variables apart from `CATALINA_HOME` and `CATALINA_BASE`.

The script is placed either into `CATALINA_BASE/bin` or into `CATALINA_HOME/bin` directory and is named `setenv.bat` (on Windows) 
or `setenv.sh` (on *nix). The file has to be readable and by default the setenv script file is absent.

You can use this script to configure the JRE_HOME variable (for example) by creating following script file:

```cmd
On Windows, %CATALINA_BASE%\bin\setenv.bat:

set "JRE_HOME=%ProgramFiles%\Java\jre8"
exit /b 0
```

For more information see https://tomcat.apache.org/tomcat-10.0-doc/RUNNING.txt

## Manager Interface

The Tomcat Manager App is a web application that is packaged with the Tomcat server. 
It provides the basic functionality needed to manage deployed web applications.

There two management interface options here:
1. A web-based (HTML) application - for humans
2. A text-based web service - for scripting

The web-based management management interface at `http[s]://<server>:<port>/manager/html/`

A user account is needed to access the management interface which needs to be configured in the `<TOMCAT_HOME>/conf/tomcat-users.xml` file.

