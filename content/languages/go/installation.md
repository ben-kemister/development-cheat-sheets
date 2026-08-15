---
title: Installing Go
tags:
 - go
 - installation
---

This guide provides instructions for installing Go on Windows and Linux, including specific steps for armv6l architectures.
<!--more-->

## Windows

There are a number of [installation methods for windows](https://gohugo.io/installation/windows/).

My preference is to download the `.msi` file from the [Go releases page](https://go.dev/dl/) and install.


## Linux

### armv6l device - for older Raspberry Pi Models

To install Go on an `armv6l` device (such as an older Raspberry Pi Model A/B, Pi Zero, or Pi 1), 
you must manually download the pre-compiled `armv6l` binary archive from the [official Go repository](https://go.dev/dl/), 
extract it, and configure your environment variables. 

> Avoid using `apt-get install golang`, as your package manager will often install an outdated version or a binary 
> compiled for a different ARM variant.

Follow the step-by-step instructions to get Go up and running:

#### 1 - Download and Extract Go

First, update your package repository `sudo apt update && sudo apt upgrade`

Then fetch the latest matching `armv6l` compressed archive from the [Official Go Download Page](https://go.dev/dl/).
```shell
# For example to download v1.26.6
wget https://go.dev/dl/go1.26.6.linux-armv6l.tar.gz -O go.tar.gz
```

Remove existing installations and extract:
```shell
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go.tar.gz
```
This completely clears out older binaries to prevent conflicts and cleanly unpacks Go into `/usr/local/go`.

Clean up the installer:
```shell
rm go.tar.gz
```

#### 2. Configure Environment Variables

You need to add the Go binary path to your system's `PATH` variable so you can run the `go` command from any directory.

Open your profile configuration:
```shell
nano ~/.bashrc
```

Append the following lines to the bottom of the file:
```shell
export GOPATH=$HOME/go
export PATH=/usr/local/go/bin:$PATH:$GOPATH/bin
```
Save and exit: Press `Ctrl+O`, then Enter to save. Press `Ctrl+X` to close nano.

Apply the changes immediately:
```shell
source ~/.bashrc
```

#### 3. Verify the Installation

Confirm that your system recognizes the installation and that it is targeted to the correct `arm` architecture.

Check the version:
```shell
go version
```
Expected output: 
```text
go version go1.26.6 linux/arm
```

Verify the environment configuration:
```shell
go env GOARCH GOARM
```
Expected output:
```text
arm
6
```

