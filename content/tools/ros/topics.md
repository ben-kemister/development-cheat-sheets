---
title: Topics
tags:
- ros
- topics
---

Topics are one of the three primary styles of interfaces provided by ROS 2. 
<!--more-->
Topics are used for continuous data streams, like sensor data, robot state, etc.

## List the topics

```shell
ros2 topic list
```

## Show information about a topic

Use the command `ros2 topic info <TOPIC_NAME>`, for example:

```shell
$ ros2 topic info cmd_vel
# Which returns
Type: geometry_msgs/msg/Twist
Publisher count: 1
Subscription count: 0
```

You can also add the `-v` flag for a verbose version, for example:

```shell
$ ros2 topic info -v /cmd_vel
# Which returns
Type: geometry_msgs/msg/Twist

Publisher count: 1

Node name: teleop_twist_keyboard
Node namespace: /
Topic type: geometry_msgs/msg/Twist
Endpoint type: PUBLISHER
GID: 01.0f.62.1e.c7.1d.b2.7d.01.00.00.00.00.00.11.03.00.00.00.00.00.00.00.00
QoS profile:
  Reliability: RELIABLE
  History (Depth): UNKNOWN
  Durability: VOLATILE
  Lifespan: Infinite
  Deadline: Infinite
  Liveliness: AUTOMATIC
  Liveliness lease duration: Infinite

Subscription count: 0
```

## View the published messages

Use the command `ros2 topic echo <TOPIC_NAME>`, for example:

```shell
ros2 topic echo /cmd_vel
```

## Links

* [Understanding topics - ROS2 Humble](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Topics/Understanding-ROS2-Topics.html)