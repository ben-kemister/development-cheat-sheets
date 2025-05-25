---
title: Broadlink (Black Bean) Devices
tags:
 - openhab
 - automation
 - infra_red
---

The Broadlink (RM Mini) black bean is a small universal Infra Red (IR) remote control which connects to and receives 
commands over WiFi.
<!--more-->
It is often used in Home Automation setups due to its low price and relatively easy integration with other systems.

## IR Commands

The IR commands used by the Black Bean need to be in a specific format. Typically, this is in a Base64 encoded byte array.

## Converting Pronto IR codes

Often IR codes are published on the internet in Pronto format, which looks something like this:
```text
0000 006D 0000 0022 00AC 00AB 0015 0041 0015 0041 0015 0041 0015 0016 0015 0016 0015 0016 0015 0016 0015 0016 0015 
0041 0015 0041 0015 0041 0015 0016 0015 0016 0015 0016 0015 0016 0015 0016 0015 0041 0015 0016 0015 0016 0015 0041 
0015 0016 0015 0041 0015 0041 0015 0041 0015 0016 0015 0041 0015 0041 0015 0016 0015 0041 0015 0016 0015 0016 0015 
0016 0015 0689
```

To use these with a Broadlink device you will need to convert them to a Base 64 encoded byte array.

I found [Sensus web interface](https://pasthev.github.io/sensus/) the easiest way to do this conversion.

Just paste the Pronto formatted string, making sure there are spaces between the groups of 4 digits, and press _convert_.
The Broadlink B64 field will contain the value which you can use with your Broadlink device.

