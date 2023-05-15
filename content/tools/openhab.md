---
title: openHAB
tags:
 - openhab
 - linux
 - java
 - automation
--- 

Open Home Automation Bus ([openHAB](https://www.openhab.org/)) is an open source home automation software written in Java. 
It is deployed on premises and connects to devices and services from different vendors.
<!--more-->

## Handy Links & Info

* [Lost admin password](https://community.openhab.org/t/lost-admin-password/118047)
* [openHAB console](https://www.openhab.org/docs/administration/console.html)

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

