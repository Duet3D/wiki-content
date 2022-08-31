---
title: Robot types and their specifics
description: Supported robot types with description of properties and how to configure them, prototype recommendations
published: true
date: 2022-08-31T22:57:32.412Z
tags: robot
editor: markdown
dateCreated: 2022-08-14T06:59:05.328Z
---

> this page will be deleted after content transfer
{.is-warning}



This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# Types of Robots
The robot kinematics supports different types. Roughly, they can be separated into
* serial robots: the joints and arms are connected in one serial chain, called open chain kinematics. Industrial robots (6 rotational axes), CNC 5 axis (3 prismatic, two rotational axes), cartesian (three prismatic axes), serial scara (one prismatic and two rotational axes) and polar (two prismatic and one rotational axes) printers are examples. Configuration allows mixing any prismatic and rotational axes, e.g. a cartesian with spheric head (3 prismatic and 3 rotational axes).
* parallel robots: the joints and arms are completely or partially connected in parallel. Often some joints are without an actuator. The kinematic is more difficult to calculate and need dedicated formulae, so only defined types are supported. Examples are delta, 5 arm parallel scara, stewart/hexapod, 4 axis palletized robot. Delta is not supported in robot kinematics, because RepRapFirmware has dedicated delta support. 4 axis palletized is supported, stewart is planned, 5 arm parallel scara has dedicated RepRapFirmware support.

The configuration parameters allow many open robot configurations, but a limited number is tested:
* 6 axis industrial robot (like Kuka KR 30, Meca500)
* CNC 5 axis (like Open5x, Pentarod)
* 4 axis palletized robot (like ABB IRB 460)

# 6 axis industrial robot
Configuration is made by defining the Denavit-Hartenberg parameters, see the wiki about DH.

Especially important is the identification of singularity areas, which must be avoided.

Care must be taken when the robot is powered off: the arms need protection against falling down with damaging result. The following possibilities avoid it
* using brakes at the joints
* weight balance of every joint, e.g. by using counterweights, so the force downward is below the detent torque and friction of the steppers/gears (servos don't have much detent torque and cannot be used for this method)
* protection by using springs or dampers

# 5 axis CNC

5 axis CNC has subtypes head/head, head/table, table/table and AC, AB, BC.

A 5 axis CNC is built from three cartesian axes (including the possibility to double axes, like optionally for gantry) and two rotary axes.

Each of the rotary axes can be installed at the spindle or at the table, resulting in 4 combinations. The possibilities have different results in respect to workpiece size, stability etc., but it is not discussed here.

It is common practice to name the rotary axes ABC matching XYZ coordinates, so if AC is used, A is parallel to the X axis and C is parallel to the Z axis when they are in 0 degree positions.

If for some reason the rotational axes are named UVW, the default firmware behaviour is to handle them as linear. To change it to rotational, use M485 R1 for the letters. ABCD are handled by firmware as rotational by default. The firmware must know whether its rotational to calculate speed limits and distances correctly, e. g.

In G-Code G0, G1 the XYZ letters are cartesian coordinates and AC (or BC, AB) are the rotary angles, i. e. different units of measurement is used. An alternative G-Code uses IJK tool vectors. They are converted into AC/BC/AB angles internally by firmware.

When A, B, C rotational changes are required, the XYZ positions change, if there is no change of the three linear/prismatic actuators. To stay at the XYZ positions while rotating, a method called RTCP is used. The required recalculation and change of the three linear actuators is achieved by segmentation of the rotation and caculation of the segment's positions.

