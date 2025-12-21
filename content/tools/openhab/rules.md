---
title: Rules
tags:
 - openhab
 - rules
---

The OpenHAB rule syntax is based on [Xbase](https://www.eclipse.org/Xtext/#xbase) and as a result it is sharing many details with [Xtend](https://www.eclipse.org/xtend/), 
which is built on top of Xbase as well.
<!--more-->

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