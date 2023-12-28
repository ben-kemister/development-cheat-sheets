---
title: "ROS - IRobot Roomba connection"
date: 2023-12-27T08:30:56+11:00
draft: false
tags:
- raspberry_pi
- ros
- ubuntu
- robotics
---

This post covers my efforts to connect [ROS](../tools/ros) (running on a Raspberry Pi) to an old iRobot Roomba 780.
<!--more-->
The goal is to be able to remote control (teleoperate) the Roomba and make it move.  

This post follows on from [installing ROS2 on Raspberry Pi](./2023-12-22-Install-ROS-on-Raspberry-Pi)

## Physical Connection

Butchering an old male-male PS/2 cable I have connected one end to the Roomba's 'External Serial Port Mini-DIN Connector'
and the other end to a [USB to UART Bridge](https://www.silabs.com/interface/usb-bridges/classic/device.cp2102?tab=specs).

The USB to UART Bridge is then connected to one of the USB ports of the _Raspberry Pi 4 Model B 2GB_ I am using.

This set up takes care of converting the conversion between the 0 – 5V logic voltage (sometimes referred to as TTL - Transistor-Transistor Logic) 
used by the Roomba which is different from the 3.3v logic voltage (sometimes referred to as CMOS) serial port on the 
Raspberry Pi.

## Software configuration

I am running ROS2 [Humble Hawksbill distribution](https://docs.ros.org/en/humble/index.html) on **Ubuntu Server 22.04.3 LTS (64-Bit)**
which is a [_Tier 1_](https://www.ros.org/reps/rep-2000.html#humble-hawksbill-may-2022-may-2027) supported configuration 
on the Raspberry Pi's `arm64` architecture.

## ROS Driver (create_robot)

The iRobot Roomba 780 is uses iRobot's Create 2 Open Interface specification.

The [create_robot](https://github.com/AutonomyLab/create_robot/tree/humble) is a ROS driver for iRobot Create 1 and 2, and
specifically lists support for the Roomba 700 Series.

## Installation & Configuration

> Note: the instructions below are based upon the `create_robot` [projects README.md file](https://github.com/AutonomyLab/create_robot/blob/humble/README.md).

Install `create_robot` dependencies:

```shell
sudo apt install python3-rosdep python3-colcon-common-extensions
```

Install the `create_robot` ROS package:

```shell
sudo apt install ros-humble-create-robot
```

To connect to Create over USB, ensure your user is in the dialout group

```shell
sudo usermod -a -G dialout $USER
```

## `source` the ROS environment

To use the ROS CLI tools you will need to `source` the ROS setup file:

```shell
source /opt/ros/humble/setup.bash
```

## Verify ``create_robot`` package has been installed

```shell
ros2 pkg prefix create_robot
# /opt/ros/humble
```

## Create a basic connection

With everything connected you should be able to connect to the Roomba using the `create_robot` default values using:

```shell
ros2 launch create_bringup create_2.launch
```

If everything is working correctly you should hear a _**boop**_ sound from the roomba and see some output in your terminal:

```text
[INFO] [launch]: All log files can be found below /home/pi/.ros/log/2023-12-26-07-50-33-944463-rpi-roomba-4265
[INFO] [launch]: Default logging verbosity is set to INFO
[INFO] [create_driver-1]: process started with pid [4267]
[INFO] [robot_state_publisher-2]: process started with pid [4269]
[robot_state_publisher-2] Warning: link 'base_footprint' material 'Green' undefined.
[robot_state_publisher-2]          at line 84 in ./urdf_parser/src/model.cpp
[robot_state_publisher-2] Warning: link 'base_footprint' material 'Green' undefined.
[robot_state_publisher-2]          at line 84 in ./urdf_parser/src/model.cpp
[robot_state_publisher-2] [INFO] [1703537435.214797640] [robot_state_publisher]: got segment base_footprint
[robot_state_publisher-2] [INFO] [1703537435.215164695] [robot_state_publisher]: got segment base_link
[robot_state_publisher-2] [INFO] [1703537435.215236713] [robot_state_publisher]: got segment front_wheel_link
[robot_state_publisher-2] [INFO] [1703537435.215270972] [robot_state_publisher]: got segment gyro_link
[robot_state_publisher-2] [INFO] [1703537435.215297731] [robot_state_publisher]: got segment left_cliff_sensor_link
[robot_state_publisher-2] [INFO] [1703537435.215323268] [robot_state_publisher]: got segment left_wheel_link
[robot_state_publisher-2] [INFO] [1703537435.215348713] [robot_state_publisher]: got segment leftfront_cliff_sensor_link
[robot_state_publisher-2] [INFO] [1703537435.215375491] [robot_state_publisher]: got segment right_cliff_sensor_link
[robot_state_publisher-2] [INFO] [1703537435.215400750] [robot_state_publisher]: got segment right_wheel_link
[robot_state_publisher-2] [INFO] [1703537435.215425453] [robot_state_publisher]: got segment rightfront_cliff_sensor_link
[robot_state_publisher-2] [INFO] [1703537435.215450620] [robot_state_publisher]: got segment wall_sensor_link
[create_driver-1] [INFO] [1703537435.556371022] [create_driver]: [CREATE] "CREATE_2" selected
[create_driver-1] [INFO] [1703537436.615399791] [create_driver]: [CREATE] Connection established.
[create_driver-1] [INFO] [1703537436.615704216] [create_driver]: [CREATE] Battery level 99.93 %
[create_driver-1] [INFO] [1703537436.664118774] [create_driver]: [CREATE] Ready.
```

> Killing the process (`Ctrl + c`) will terminate the connection between ROS and the Roomba. 

## Teleoperation with Keyboard

We can use the `teleop_twist_keyboard` ROS package to send commands to the Roomba and move the root using a keyboard.

First install the `teleop_twist_keyboard` package with:

```shell
sudo apt update && sudo apt install ros-humble-teleop-twist-keyboard
```

And then you can run the `teleop_twist_keyboard` package with:

```shell
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

> To make the Roomba move you will need a separate terminal running `ros2 launch create_bringup create_2.launch`.   
> The `create_robot` package will then subscribe to messages published on the `/cmd_vel` topic and send these to the Roobma.

### (Optional) Viewing the published messages

You can view the published messages by opening a separate terminal and running:

```shell
ros2 topic echo /cmd_vel
```

## (Optional) Launch file

You can use a [launch file](https://docs.ros.org/en/humble/Tutorials/Intermediate/Launch/Creating-Launch-Files.html?highlight=launch%20file) 
to be able to start one or more ROS packages with a single command.

> Unfortunately, it looks like you can't use this method to start the `teleop_twist_keyboard` package.  
> I believe this is because it needs (full?) access to the console to work.

```yaml
launch:

# Include the create_bringup package launch file
- include:
    # Replaces the 'ros2 launch create_bringup create_2.launch' terminal command.
    file: "$(find-pkg-share create_bringup)/launch/launch/create_2.launch"
```
Which can then be launched with: 

```shell
ros2 launch launch.yaml
```

Done! Keep an eye out for another ROS/Roomba post which builds on this.

## Links

* [ROS create_robot package - GutHub](https://github.com/AutonomyLab/create_robot)
* [ROS teleop_twist_keyboard package - GitHub](https://github.com/ros2/teleop_twist_keyboard/tree/dashing)
* [ROS packages for Humble](https://repo.ros2.org/status_page/ros_humble_ujv8.html)
* [ROS Tutorial on Teleoperation and Vision based Robot Control](https://github.com/SMARTlab-Purdue/ros-tutorial-robot-control-vision/blob/master/README_full.md)