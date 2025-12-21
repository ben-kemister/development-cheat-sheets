---
title: Configuration
tags:
 - openhab
 - configuration
---

This page covers OpenHAB configuration
<!--more-->

## The ``addons.cfg`` file

The main entry point for configuration is the `/openhab/conf/conf/services/addons.cfg` file.
An example of this file can be found below:

```txt
# The installation package of this openHAB instance
# Note: This is only regarded at the VERY FIRST START of openHAB
# Note: If you want to specify your add-ons yourself through entries below, set the package to "minimal"
# as otherwise your definition might be in conflict with what the installation package defines.
#
# Valid options:
#   - standard : Standard setup for normal use of openHAB with persistence (rrd4j) and additional UI add-ons (basic,habpanel).
#   - minimal  : Installation of core components without additional add-ons.
#
#package = standard
package = minimal

# Access Remote Add-on Repository
# Defines whether the remote openHAB add-on repository should be used for browsing and installing add-ons. (default is true)
#
remote = true

# Some add-on services may provide add-ons where compatibility with the currently running system is not expected.
# Enabling this option will include these entries in the list of available add-ons.
#
includeIncompatible = false

# A comma-separated list of automation services to install (e.g. "automation = groovyscripting")
automation = jsscripting,ohrt

# A comma-separated list of bindings to install (e.g. "binding = knx,sonos,zwave")
binding = astro,broadlinkthermostat,chromecast,http,mqtt,network,bluetooth

# A comma-separated list of miscellaneous services to install (e.g. "misc = openhabcloud")
misc = openhabcloud,

# A comma-separated list of persistence services to install (e.g. "persistence = jpa,rrd4j")
persistence = influxdb

# A comma-separated list of transformation services to install (e.g. "transformation = jsonpath,map")
transformation = jsonpath,regex,map

# A comma-separated list of UIs to install (e.g. "ui = basic,habpanel")
ui = basic,habpanel

# A comma-separated list of voice services to install (e.g. "voice = googletts,marytts")
voice = voicerss
```

> On startup OpenHAB uses the `addons.cfg` file to populate the `/openhab/conf/userdata/config/org/openhab/addons.config`
> file.