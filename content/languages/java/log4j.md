---
title: log4j
tags:
- java
- log4j
- logging
---

This page contains information, syntax, and simple code examples, about the use of Apache's Log4j.
<!--more-->

## Configuration file location

Upon initialization Log4J core scans the classpath for a configuration file named `log4j2.<extension>` 
(as well as a bunch of other files which include the context name). For more information see the 
[Log4j configuration doco](https://logging.apache.org/log4j/2.x/manual/configuration.html).

Typically, for a simple Java application (like a Springboot fat jar) you can just add the Log4J configuration file in 
the same directory as the `*.jar` file.

You can also set the location of the configuration file using the ``LOG4J_CONFIGURATION_FILE`` environmental variable.
Also see the [Log4J configuration properties page](https://logging.apache.org/log4j/2.x/manual/systemproperties.html#log4j2.configurationFile) for more information.