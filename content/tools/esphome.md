---
title: ESPHome
tags:
- automation
- firmware
---

[ESPHome](https://esphome.io/index.html) is an open-source framework that simplifies creating custom firmware for ESP32 and ESP8266 microcontrollers. 
<!--more-->
It allows users to build smart home devices using YAML configuration files and integrate them with systems like Home Assistant. 
Essentially, ESPHome transforms these microcontrollers into smart devices through a user-friendly configuration process.

## Running ESPHome in Podman/Docker

You can run ESPHome CLI commands using Podman/Docker using the following:

```shell
podman run --rm -p 6052:6052 -it `
    -v ${PWD}/my-config:/config `
    ghcr.io/esphome/esphome:<VERSION_TAG> <ESPHOME_CLI_COMMAND> [<CONFIG_FILE>]
```

## Commands

### `config` - Checks/Verifies a configuration

The `esphome config <CONFIG_FILE>` validates the configuration and displays the validation result.
For example:
```shell
esphome config some-config-file.yaml
```
or using Podman/Docker:
```shell
podman run --rm -it `
    -v ${PWD}/my-configs:/config `
    ghcr.io/esphome/esphome:2025.6.3 config some-config-file.yaml
```


## `run` - Validates, Complies and Uploads

The `esphome run <CONFIG>` command is the most common command for ESPHome. 
It: 
* Validates the configuration
* Compiles a firmware
* Uploads the firmware (over OTA or USB)
* Starts the log view

For example:
```shell
esphome run some-config-file.yaml
```
or using Podman/Docker:
```shell
podman run --rm -it `
    --privileged --device=/dev/ttyUSB0 `
    -v ${PWD}/my-configs:/config `
    ghcr.io/esphome/esphome:2025.6.3 run some-config-file.yaml
```
> Note: `--device=/dev/ttyUSB0` is for Linux hosts only. Docker on Windows and Mac will not be able to access host USB devices.



## `upload` - Uploads the most recent firmware build

The `esphome upload <CONFIG_FILE>` validates the configuration and uploads the most recent firmware build.

```shell
podman run --rm -it `
     --network=host `
     -v ${PWD}/my-configs:/config `
      ghcr.io/esphome/esphome:2025.6.3 upload some-config-file.yaml --device 192.168.21.154
```


## Commonly used Components

### WiFi Component

The (core) [WiFi Component](https://esphome.io/components/wifi) sets up WiFi connections to access points for you.

```yaml
# It is highly recommended to use secrets
wifi:
  # References the 'wifi_ssid' key in the 'secrets.yaml' file
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```


### ESPHome Native API Component

The [ESPHome native API](https://esphome.io/components/api) is used to communicate with clients directly, with a highly-optimized network protocol.

```yaml
# Example with more options
api:
  # (Optional): If present, encryption will be enabled for the API. 
  # Using encryption helps to secure the communication between the device running ESPHome and the connected client(s)
  encryption:
    #  (Optional, string): A 32-byte base64-encoded string to be used as the encryption key. 
    # If not provided, the key may be set at runtime, but encryption will not be used until it is set.
    key: "YOUR_ENCRYPTION_KEY_HERE"
```


## Handy Links

* [Getting Started with the Command Line | ESPHome](https://esphome.io/guides/getting_started_command_line)
* [CLI commands | ESPHome](https://esphome.io/guides/cli)
* [YAML Configuration in ESPHome](https://esphome.io/guides/yaml)
* [Components | ESPHome](https://esphome.io/components/)