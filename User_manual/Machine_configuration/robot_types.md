---
title: Robot types and their specifics
description: Supported robot types with description of properties and how to configure them, prototype recommendations
published: true
date: 2022-08-31T23:01:05.333Z
tags: robot
editor: markdown
dateCreated: 2022-08-14T06:59:05.328Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# Types of Robots
The robot kinematics supports different types. Roughly, they can be separated into
* serial robots: the joints and arms are connected in one serial chain, called open chain kinematics. Industrial robots (6 rotational axes), CNC 5 axis (3 prismatic, two rotational axes), cartesian (three prismatic axes), serial scara (one prismatic and two rotational axes) and polar (two prismatic and one rotational axes) printers are examples. Configuration allows mixing any prismatic and rotational axes, e.g. a cartesian with spheric head (3 prismatic and 3 rotational axes).
* parallel robots: the joints and arms are completely or partially connected in parallel. Often some joints are without an actuator. The kinematic is more difficult to calculate and need dedicated formulae, so only defined types are supported. Examples are delta, 5 arm parallel scara, stewart/hexapod, 4 axis palletized robot. Delta is not supported in robot kinematics, because RepRapFirmware has dedicated delta support. 4 axis palletized is supported, stewart is planned, 5 arm parallel scara has dedicated RepRapFirmware support.

The configuration parameters allow many open robot configurations, but a limited number is tested:
* 6 axis industrial robot (like Kuka KR 30, Meca500)
* CNC 5 axis (like Open5x, Pentarod)
* 4 axis palletized robot (like ABB IRB 460)

CNC 5 axis and 4 axis palletized robots are described in separate documents. Following is a description of a 6 axis robot, which is also valid for 2 to 6 axes.

# 6 axis industrial robot
Configuration is made by defining the Denavit-Hartenberg parameters, see the wiki about DH.

Especially important is the identification of singularity areas, which must be avoided.

Care must be taken when the robot is powered off: the arms need protection against falling down with damaging result. The following possibilities avoid it
* using brakes at the joints
* weight balance of every joint, e.g. by using counterweights, so the force downward is below the detent torque and friction of the steppers/gears (servos don't have much detent torque and cannot be used for this method)
* protection by using springs or dampers

