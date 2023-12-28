---
title: Packages
tags:
- ros
- packages
---

Many of the coolest and most useful capabilities of ROS already exist somewhere in its community. Often, stable resources exist as easily downloadable debian packages.
<!--more-->

## Check if package is installed

Use the command: `ros2 pkg prefix <PACKAGE_NAME>` for example:

```shell
$ ros2 pkg prefix demo_nodes_cpp
# Output if package is not installed
Package not found
```

## List all packages

```shell
ros2 pkg list
```

## Install a package

### Using `apt`

ROS packages can be installed with `apt` just like other linux packages.

However, the package name needs to be prefixed with `ros-<ROS_DISTRIBUTION_NAME>`. 
For example to search for the ROS2 `humble` version of `demo-nodes-cpp` use:

```shell
sudo apt search ros-humble-demo-nodes-cpp
```

Which can be installed with:

```shell
sudo apt install ros-humble-demo-nodes-cpp
```

## Remove a package

### Using `apt`

```shell
sudo apt remove ros-humble-create-robot
```
