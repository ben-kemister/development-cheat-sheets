---
title: OpenHAB
tags:
 - linux
 - java
--- 

Open Home Automation Bus ([openHAB](https://www.openhab.org/)) is an open source home automation software written in Java. 
It is deployed on premises and connects to devices and services from different vendors.
<!--more-->
## Rules

The openHAB rule syntax is based on [Xbase](https://www.eclipse.org/Xtext/#xbase) and as a result it is sharing many details with [Xtend](https://www.eclipse.org/xtend/), which is built on top of Xbase as well.

### Loops

```java
        // The time between each command
        var Number loopTime = 1000
        
        // Loop to Turn volume down
        var Number i = 0
        while((i=i-1) >= iterations) {
            logInfo(logger_name, "Volume down")
            AV_Receiver_Volume_Down_Switch.sendCommand(ON)
            Thread::sleep(loopTime)
        }
```



Connecting to docker engine on another pc
