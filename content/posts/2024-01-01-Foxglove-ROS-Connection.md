---
title: "2024 01 01 Foxglove ROS Connection"
date: 2024-01-01T08:41:48+11:00
draft: false
tags:
- ros
- robotics
- foxglove
---

This post covers the setup and connection of [foxglove studio](../tools/ros/foxglove) with my [ROS2 controlled Roomba robot](./2023-12-24-ROS-iRobot-Roomba).
<!--more-->
The aim here is to be able to have a (web based) platform which can be used to visualise the robot data and provide basic 
teleoperation capabilities running from a separate remote platform (i.e. not on the Raspberry Pi on the Roomba).

This post follows on from the steps in the [ROS - IRobot Roomba connection post](./2023-12-24-ROS-iRobot-Roomba).

## Running foxglove

For this post we are going to run foxglove studio in a docker/podman container which will provide us with some freedom 
around where to host the application.

To get the basic foxglove studio app running use:

```shell
docker run --rm -p "8080:8080" ghcr.io/foxglove/studio:latest
```

The UI will then be available at http://localhost:8080/

## Install ROS Foxglove bride

The recommended way to connect Foxglove with ROS is to use the [ROS Foxglove bridge](https://docs.foxglove.dev/docs/connecting-to-data/ros-foxglove-bridge/).

Power up and connect to the host running ROS (i.e. `ssh <USER>@<ROS_HOSTNAME>`).

Install the `foxglove-bridge` using the command `sudo apt install ros-$ROS_DISTRO-foxglove-bridge`, for example:

```shell
sudo apt update && sudo apt install ros-humble-foxglove-bridge
```

> Tip: You can check your ROS distribution by using: `ros2 doctor -r`

## Starting the foxglove-bridge ROS node

To launch the foxglove-bridge ROS node use the command:

```shell
ros2 launch foxglove_bridge foxglove_bridge_launch.xml
```

When launching the default `foxglove-bridge` configuration should be sufficient to allow remote unencrypted connections 
to your ROS stack.

You can view the default `foxglove-bridge` configuration values using: `cat /opt/ros/$ROS_DISTRO/share/foxglove_bridge/launch/foxglove_bridge_launch.xml`
with documentation on the [foxglove website](https://docs.foxglove.dev/docs/connecting-to-data/ros-foxglove-bridge/#configuration).

## Connect Foxglove studio to ROS

To create the connection open the foxglove studio in a web-browser and:

* Select _Open connection_
* Select _Foxglove WebSocket_
* In the _WebSocket URL_ textbox, enter the IP address or hostname of your ROS machine using the following format ``ws://<IP_OR_HOSTNAME>:8765``, for example `ws://192.168.0.205:8765`
* Select _Open_

With a successful connection you should be able to view some of the standard ROS topics in the _Topics_ tab.

> I believe the Foxglove connection information is stored/cached on the client/browser side.

## Starting foxglove_bridge with a launch file

You can use a [launch file](https://docs.ros.org/en/humble/Tutorials/Intermediate/Launch/Creating-Launch-Files.html?highlight=launch%20file)
to be able to start the foxglove_bridge with other ROS packages (such as `create_robot`'s `create_bringup` package I 
used to connect the Roomba) with a single command.

To do this create a ``launch.yaml`` file with the following contents:

```yaml
#
# Description: A ROS2 launch file to start multiple packages
# To run use the command:
#     ros2 launch launch.yaml
#
---
launch:

  # Launch the create_robot create_bringup package to connect to the Roomba
  - include:
      file: "$(find-pkg-share create_bringup)/launch/create_2.launch"

  # Launch the foxglove_bridge package to allow remote visualisation/controls
  - include:
      file: "$(find-pkg-share foxglove_bridge)/launch/foxglove_bridge_launch.xml"
```
This file can then be launched with:

```shell
ros2 launch launch.yaml
```

## Teleop panel

The Foxglove Teleop panel can be used to publish [geometry_msgs/msg/Twist](https://github.com/ros2/common_interfaces/blob/master/geometry_msgs/msg/Twist.msg) 
messages to a topic.

For the `create_bringup` package it uses the `/cmd_vel` topic for these messages to control the Roomba's movement.

To use the Teleop panel, add it to the foxglove layout and in the panel's cog/settings menu select the 
_Import/export settings..._ option, copy in the configuration below, and press _Apply_.

```json
{
  "topic": "/cmd_vel",
  "publishRate": 5,
  "upButton": {
    "field": "linear-x",
    "value": 0.1
  },
  "downButton": {
    "field": "linear-x",
    "value": -0.1
  },
  "leftButton": {
    "field": "angular-z",
    "value": 0.1
  },
  "rightButton": {
    "field": "angular-z",
    "value": -0.1
  },
  "foxglovePanelTitle": "Roomba Movement"
}
```

With the Roomba connected and the `launch.yaml` file running on the ROS host (Raspberry Pi) you should be able to move 
the Roomba using the arrow buttons in the **Roomba Movement** panel.

## Exporting the Foxglove layout

You can export the foxglove panel layout so that it can be imported and used without having to configure it from scratch.

From the Foxglove UI menu select View --> Export layout to file...

You can then import this file (using the UI), or make it the default layout by using a container volume appearing as `/foxglove/default-layout.json`.
For example:

```shell
docker run --rm -p "8080:8080" -v /path/to/custom_layout.json:/foxglove/default-layout.json ghcr.io/foxglove/studio:latest
```

## References and Links

Below are the links to some of the references used for this post:

* [ROS2 - Foxglove](https://docs.foxglove.dev/docs/connecting-to-data/frameworks/ros2/)
* [ROS Foxglove bridge](https://docs.foxglove.dev/docs/connecting-to-data/ros-foxglove-bridge/)
* [Launch file examples - ROS Humble](https://docs.ros.org/en/humble/How-To-Guides/Launch-file-different-formats.html#launch-file-examples)
