---
title: "Install ROS2 on Raspberry Pi"
date: 2023-12-22T06:28:00+11:00
draft: false
tags:
- raspberry_pi
- ros
- ubuntu
- robotics
---

A walk through of installing [ROS](../tools/ros) on a Raspberry Pi (RPi 4 Model B 2GB), for use in a robotic project.
<!--more-->

## Hardware and OS selection

The [ROS getting started page](https://www.ros.org/blog/getting-started/) recommends using a 'Tier 1' OS for new users 
as it reduces the work and complexity of installing ROS.

At the time of writing this post the page recommends using the **Humble Hawksbill** as the ROS 2 LTS (Long Term Support)
version of ROS to use. 

Humble Hawksbill is supported from May 2022 - May 2027 and has Tier 1 support for Ubuntu Jammy (22.04) on amd64 and arm64
devices.

For my case I wanted to use ROS on a mobile robot platform so a Raspberry Pi with an _arm64_ architecture was the way to 
go. Not all Raspberry Pi's support a 64 bit instruction set [^1], however most newer models do. 

For this project I used a **RPi 4 Model B 2GB** which I pilfered from my k3s cluster.

[^1]: https://en.wikipedia.org/wiki/Raspberry_Pi#Specifications

## Installing the OS (Ubuntu server)

I used the [Raspberry Pi imager](https://www.raspberrypi.com/software/) to install **Ubuntu Server 22.04.3 LTS (64-Bit)** on a
spare 128 GB micro SD card I had lying around.

Make sure to set up your WiFi network, hostname, user and SSH to support remote (headless) logins to the Raspberry Pi.

## Power up & Connect to Raspberry Pi

Insert the SD Card and power up the Raspberry pi. It takes a while on first boot, after the activity LED is not constantly 
lit you should be OK to connect via a ssh session using `ssh <USER>@<HOSTNAME>`.

## Installing ROS

There are multiple ways to install ROS, with the [Debian packages](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html) 
option of installing the binaries the easiest.

Much of the steps below follow the [Debian packages](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html)
guide, with a few minor tweaks along the way...

### Locale

I set the locale to `en_AU` with the following commands:

```shell
sudo apt update && sudo apt install locales
sudo locale-gen en_AU en_AU.UTF-8
sudo update-locale LC_ALL=en_AU.UTF-8 LANG=en_AU.UTF-8
export LANG=en_AU.UTF-8

locale # verify settings
```

### Setup Sources

Enable Ubuntu Universe repository.

```shell
sudo apt install software-properties-common
sudo add-apt-repository universe
```

Now add the ROS 2 GPG key with apt.

```shell
sudo apt update && sudo apt install curl -y
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
```

Then add the repository to your sources list.

```shell
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```

### Install ROS 2 packages

```shell
sudo apt update && sudo apt upgrade
```

> We have skipped the installation of the ROS desktop components as we will be running ROS on a headless 
> (no monitor, keyboard, or mouse) Raspberry Pi

ROS-Base Install (Bare Bones): Communication libraries, message packages, command line tools. No GUI tools.

```shell
sudo apt install ros-humble-ros-base
```

Development tools: Compilers and other tools to build ROS packages.

```shell
sudo apt install ros-dev-tools
```

### Environment setup

Set up your environment by sourcing the following file.

```shell
# Replace ".bash" with your shell if you're not using bash
# Possible values are: setup.bash, setup.sh, setup.zsh
source /opt/ros/humble/setup.bash
```

### Check Versions & Set up

You can use the ``ros2 doctor`` set of commands to check on the ROS installation, for example:

```shell
# For full report
ros2 doctor --report

# Or to check for broken components with
ros2 doctor --report-failed --include-warnings
```

You can also check the default middleware that ROS is using with `ros2 doctor --report | grep middleware`

## Run simple (talker-listener) example

> These example package are typically installed with the `ros-humble-desktop`, but as we skipped this step these examples 
> need to be installed manually

### Install demo packages

```shell
sudo apt install ros-humble-demo-nodes-cpp ros-humble-demo-nodes-py
```

### Run 'talker-listener' demo

In one terminal, source the setup file and then run a C++ talker:

```shell
source /opt/ros/humble/setup.bash
ros2 run demo_nodes_cpp talker
```

In another terminal source the setup file and then run a Python listener:

```shell
source /opt/ros/humble/setup.bash
ros2 run demo_nodes_py listener
```

You should see the `talker` saying that it’s Publishing messages and the `listener` saying I heard those messages. 

This verifies both the C++ and Python APIs are working properly. **Awesome!**