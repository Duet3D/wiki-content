---
title: Robot CNC 5 axis
description: Description of configuration specifics, examples, axis flavours, G-Code variants
published: true
date: 2022-08-31T23:00:33.293Z
tags: robot
editor: markdown
dateCreated: 2022-08-31T22:53:13.376Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# 5 axis CNC

5 axis CNC has subtypes head/head, head/table, table/table and AC, AB, BC.

A 5 axis CNC is built from three cartesian axes (including the possibility to double axes, like optionally for gantry) and two rotary axes.

Each of the rotary axes can be installed at the spindle or at the table, resulting in 4 combinations. The possibilities have different results in respect to workpiece size, stability etc., but it is not discussed here.

It is common practice to name the rotary axes ABC matching XYZ coordinates, so if AC is used, A is parallel to the X axis and C is parallel to the Z axis when they are in 0 degree positions.

If for some reason the rotational axes are named UVW, the default firmware behaviour is to handle them as linear. To change it to rotational, use M485 R1 for the letters. ABCD are handled by firmware as rotational by default. The firmware must know whether its rotational to calculate speed limits and distances correctly, e. g.

In G-Code G0, G1 the XYZ letters are cartesian coordinates and AC (or BC, AB) are the rotary angles, i. e. different units of measurement is used. An alternative G-Code uses IJK tool vectors. They are converted into AC/BC/AB angles internally by firmware.

When A, B, C rotational changes are required, the XYZ positions change, if there is no change of the three linear/prismatic actuators. To stay at the XYZ positions while rotating, a method called RTCP is used. The required recalculation and change of the three linear actuators is achieved by segmentation of the rotation and caculation of the segment's positions.

