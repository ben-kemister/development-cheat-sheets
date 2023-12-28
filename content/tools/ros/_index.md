---
title: Robot Operating System (ROS)
tags:
- ros
- robotics
---

[Robot Operating System](https://www.ros.org/) (ROS or ros) is an open-source robotics middleware suite. 
<!--more-->
Although ROS is not an operating system (OS) but a set of software frameworks for robot software development, 
it provides services designed for a heterogeneous computer cluster such as hardware abstraction, low-level device control, 
implementation of commonly used functionality, message-passing between processes, and package management.

### Check Versions & Set up

You can use the ``ros2 doctor`` commands to check on the ROS installation and version numbers of the installed packages,
for example:

```shell
# For full report
ros2 doctor --report
```

## ROS environment

To make sure that the ROS tools and packages are available on your shell you need to source the ROS setup script using 
`source /opt/ros/humble/setup.bash`.

You can add this to your `~/.bashrc` file so that it is automatically done for each terminal session:

```shell
echo -e "# Source the ROS2 setup files\nsource /opt/ros/humble/setup.bash" \
>> ~/.bashrc 
```

Or alternatively use a text editor like `nano` to add the command to your `~/.bashrc` file:

```shell
nano ~/.bashrc
# source /opt/ros/humble/setup.bash
```

## Child pages

{{% children sort="title" description="true" %}}

## Links

* [ROS driver for iRobot Create 1 and 2](https://github.com/AutonomyLab/create_robot)