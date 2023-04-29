---
title: Robot industrial 6 axis
description: 
published: true
date: 2023-04-29T06:46:04.689Z
tags: robot
editor: markdown
dateCreated: 2022-09-13T11:49:01.371Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

![robot_dh_main.jpg](/manual/configuration/robot_main.jpg)

> work in progress, will be changed often to change to screw theory and geometric algebra solution
{.is-info}

# Introduction

Following is a description of the common 6 axis industrial robot. It has the following axes:
* axis 1 is vertical and axis 2 is horizontal. They cross in one point
* 3 is horizontal, but need not to be parallel to axis 2
* axis 4 "twists" the next arm
* axis 5 is parallel to 2 and 3 again (if axis 4 is 0 degrees)
* axis 6 rotates the endpoint
* axes 4 to 6 cross in one point (intersect)

The basic configuration setting is 
M669 B"robotType=6Axis"

6 axis robots are capable to control all 6 degrees of freedom (6DOF).

# Configuration

The configuration page describes the code to setup the robot, the screw theory page describes how axes' properties are specified.

# G-Code

For the 6 axes, the letters XYZABC are used, all axes being rotary axes, referenced in the document as axes 1 to 6. The arms are referenced as arms 1 to 6, being behind the axes (e. g. arm 2 is between axis 2 and 3).

IJK is not usable, as IJ is used for G2/G3 code.

# safety

Care must be taken when the robot is powered off: the arms need protection against falling down with damaging result. The following possibilities avoid it
* using brakes at the joints
* weight balance of every joint, e.g. by using counterweights, so the force downward is below the detent torque and friction of the steppers/gears (servos don't have much detent torque and cannot be used for this method)
* gears like harmonic drive or cycloidal gears change the effect of detent torque. A 1:30 gear will change detent torque of 2 Ncm to 60 Ncm braking effect.
* protection by using springs or dampers

# G10 tool offsets

Kinematics uses the G10 settings of the currently selected tool.

# Testcases

The following setups were used to test forward and inverse kinematics, so if one uses this configuration, chances are good that the robot works correctly.

# Case 1, 2 intersected, 4-6 intersected

This is the setup of the DH example, with the change that axes 1 and 2 intersect.

|-|-|-|
|axis #|direction|a point on the axis|
|1|0, 0, 1|0, 0, 352|
|2|0, 1, 0|0, 0, 352|
|3|0, 1, 0|0, 0, 712|
|4|1, 0, 0|380, 0, 712|
|5|0, 1, 0|380, 0, 712|
|6|1, 0, 0|380, 0, 712|

tool: z = length 100 set with G10
GSt(0) must be the tool tip pos/ori

GSt(0), endposition for given angles/positions (rotary/prismatic):

|-|-|
|direction|value|
|x|0, 0, 1|
|y|0, -1, 0|
|z|1, 0, 0|
|pos|545, 0, 712|
|angles/pos|0, 0, 0, 0, 0, 0|

angle limits:

|-|-|-|-|
|axis|angle min|angle max|homing angle|
|1|-150|150|0|
...



The workspace is ...

singularities are at...
