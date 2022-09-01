---
title: Robot CNC 5 axis
description: Description of configuration specifics, examples, axis flavours, G-Code variants
published: true
date: 2022-09-01T09:25:01.790Z
tags: robot
editor: markdown
dateCreated: 2022-08-31T22:53:13.376Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# 5 axis CNC

A 5 axis CNC is built from three cartesian axes (including the possibility to double axes, like optionally for gantry) and two rotary axes:
* the rotary axis which is parallel to the X axis is called A axis, to Y is the B axis and to Z is the C axis when they are in 0 degree position. Two of them are used, AC, AB or BC.
* a rotating spindle has only one important orientation, the Z axis. Two rotations are sufficient to describe it's orientation.
* the rotary axes can be installed at the spindle ("head") or table, resulting in subtypes head/head, head/table, table/table
* the possibilities have different results in respect to workpiece size, stability etc., but it is not discussed here.

If for some reason the rotational axes are named UVW, the default firmware behaviour is to handle them as linear. To change it to rotational, use M485 R1 for the letters. ABCD are handled by firmware as rotational by default. The firmware must know whether its rotational to calculate speed limits and distances correctly, e. g.

In G-Code G0, G1 the XYZ letters are cartesian coordinates and AC (or BC, AB) are the rotary angles, i. e. different units of measurement is used. An alternative G-Code uses IJK tool vectors. They are converted into AC/BC/AB angles internally by firmware.

When A, B, C rotational changes are required, the XYZ positions change, if there is no change of the three linear/prismatic actuators. To stay at the XYZ positions while rotating, a method called RTCP is used. The required recalculation and change of the three linear actuators is achieved by segmentation of the rotation and caculation of the segment's positions.

# G-Code

A CAD, CAM program, slicer or postprocessor will create G-Code which can be executed and is used to control the 5 axis CNC axes. There are two common addressing modes:
* using XYZ and AB or AC or BC, XYZ being mm positions and AB... being degrees
* using XYZ and IJK, XYZ being mm positions and IJK being tool vectors, the numbers are real numbers between -1.0 to +1.0 and normalized to I²+J²+K²=1. The tool vector desribes the tilt of the tool with respect to the Z axis.

The IJK is more machine independent, but less often used. It is possible to convert the system with postprocessor or alike into the other system. For example,
G1 X10 Y10 Z10 I0.5 J0.5 K0.707106
is the same as
G1 X10 Y10 Z10 B45 C45
on a BC system.

# DH parameters

If AC or BC in table/table mode are used, the calculation is in workpiece mode, the transformations are as follows:

![cnc5axisactransform.png](/manual/configuration/cnc5axisactransform.png)

Starting from the base of the C axis plate, the coordinate system is translated d up to be in line with the A/B axis. Then changing by the A/B angle and then tranlating down to the base again by -d. Then rotaing by C axis, and then adding XYZ of the linear axes and some tool offsets to get the endpoint position and orientation. Because it is in workpiece mode, which means the transformation shall start from the workpiece and not the base, most matrix transformations must be inverted must take place to calculate forward and inverse kinematics.

Forward calculates A/C or B/C to XYZ IJK position and orientation, while inverse kinematics can be calculated from XYZ IJK, resulting in XYZ AC or XYZ BC values. IJK is the tool vector with orientation vertical on the workpiece surface.
