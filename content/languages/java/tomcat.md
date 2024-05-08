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

A user account is needed to access the management interface (i.e. a role of `manager-gui`) which needs to be configured 
in the `<TOMCAT_HOME>/conf/tomcat-users.xml` file.

For example:

```xml
<user username="admin" password="admin" roles="manager-gui"/>
```

## Windows Service

Tomcat has some utilities to assist with the creation (and control) of a Windows service to run Tomcat.

The main utilities are:

* `service.bat` - script to manually create the service.
* `tomcat10w.exe` - is a GUI application for monitoring and configuring Tomcat services.
* `tomcat10.exe` - is a service application for running Tomcat 10 as a Windows service.

For more information see the [Windows Service How-To](https://tomcat.apache.org/tomcat-10.1-doc/windows-service-howto.html).

### Edit existing service

The command `./tomcat10w.exe //ES[//ServiceName]` starts the GUI application which allows the service configuration 
to be modified, started and stopped.

For example:

```powershell
./tomcat10w.exe //ES[//ServiceName]
```









