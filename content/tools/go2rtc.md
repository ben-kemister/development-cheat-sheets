---
title: "go2rtc"
date: "2026-08-15"
tags: [go2rtc, camera streaming, RTSP, WebRTC, FFmpeg, Go]
---

[go2rtc](https://github.com/AlexxIT/go2rtc) is a high-performance, zero-dependency camera streaming application designed for low-latency stream 
processing and protocol conversion. 
<!--more-->

## Core Functionality
* **Protocol Support:** Handles dozens of input, output, and ingest formats/protocols.
* **Low Latency:** Provides zero-delay streaming for many supported protocols.
* **Transcoding:** Performs on-the-fly transcoding via FFmpeg only when required.
* **Audio Handling:** Supports two-way audio and allows mixing tracks from multiple sources into a single stream.
* **Streaming Ingest/Publish:** Capable of ingesting various formats and publishing streams to services like YouTube or Telegram.
* **Codec Management:** Features automatic codec matching and negotiation between clients and sources.

## Deployment and Installation

go2rtc can be run from:
* the `go2rtc` binary
* the Docker image, or
* from Home Assistant

### Binary Execution

> To run the binary you will need to have Go installed on your system.

Download the platform-specific binary (Windows, macOS, Linux, FreeBSD) from the [latest release](https://github.com/AlexxIT/go2rtc/releases/).
On Linux and macOS, ensure the file has execution permissions:
```bash
chmod +x go2rtc_linux_amd64
./go2rtc_linux_amd64
```

#### Raspberry Pi Camera Module v1

I found instruction on how to run `go2rtc` on a Raspberry Pi to capture a stream from the camera module [here](https://community.home-assistant.io/t/raspberry-pi-camera-as-dumb-h264-stream-for-frigate/565784/6).

The post in the forum has the `go2rtc` binary installed in `/var/lib/go2rtc` uses a `systemd` service to load a configuration
in `/var/lib/go2rtc/go2rtc.yaml` as follows:

Install `go2rtc` binary:
```shell
wget https://github.com/AlexxIT/go2rtc/releases/latest/download/go2rtc_linux_armv6
sudo mkdir -p /var/lib/go2rtc
sudo mv go2rtc_linux_armv6 /var/lib/go2rtc
chmod +x /var/lib/go2rtc/go2rtc_linux_armv6
```

Create the `/var/lib/go2rtc/go2rtc.yaml` configuration file:
```shell
sudo nano /var/lib/go2rtc/go2rtc.yaml
```
```yaml
# /var/lib/go2rtc/go2rtc.yaml 
streams:
  # The `libcamera-vid` package is deprecated, and has been replaced by `rpicam-vid`
  # cam: exec:libcamera-vid --width 1024 --height 576 --framerate 15 -t 0 --inline -o -
  cam: exec:rpicam-vid --width 1280 --height 720 --framerate 15 -t 0 --inline -o -
```

> Note the original post used the `libcamera-vid` package which is deprecated, and has been replaced by `rpicam-vid`.    
> For more information see [Raspberry Pi - Camera Software](https://www.raspberrypi.com/documentation/computers/camera_software.html).    


Create `systemd` service:
```shell
sudo nano /etc/systemd/system/go2rtc_server.service
```
```text
# /etc/systemd/system/go2rtc_server.service

[Unit]
Description=go2rtc
After=network.target rc-local.service

[Service]
Restart=always
WorkingDirectory=/var/lib/go2rtc/

ExecStart=/var/lib/go2rtc/go2rtc
# Use the following for the armv6l version for an older Raspberry Pi Model A/B, Pi Zero, or Pi 1
# ExecStart=/var/lib/go2rtc/go2rtc_linux_armv6

[Install]
WantedBy=multi-user.target
```

Start the `systemd` service:

```shell
sudo systemctl daemon-reload
sudo systemctl start go2rtc_server
sudo systemctl status go2rtc_server
```

This will make:
* The configuration webpage available at: `http://<RASPBERRY_PI>:1984/`
* The RTSP available at: `rtsp://<RASPBERRY_PI>:8554/<NAME_OF_CAMERA>`
   With the configuration above this would be `rtsp://<RASPBERRY_PI>:8554/cam`


### Docker
Use the official containers which include pre-installed FFmpeg and Python. Multi-architecture support is available for `386`, `amd64`, `arm/v6`, `arm/v7`, and `arm64`.
```bash
docker pull alexxit/go2rtc
# or
docker pull ghcr.io/alexxit/go2rtc
```

### Configuration and Interface
* **Web Interface:** Accessible via `http://localhost:1984/`.
* **Stream Management:** Streams are configured by adding entries to the configuration file.

### Handy Links
* [go2rtc GitHub Repository](https://github.com/AlexxIT/go2rtc)

