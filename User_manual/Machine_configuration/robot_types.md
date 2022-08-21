---
title: Robot types and their specifics
description: Supported robot types with description of properties and how to configure them, prototype recommendations
published: true
date: 2022-08-21T15:15:56.702Z
tags: robot
editor: markdown
dateCreated: 2022-08-14T06:59:05.328Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

> This wiki is new and under construction
{.is-info}


# Introduction
Robot kinematics supports different kinds of robots. Following is a description of some of them, categorized by number of joints/axes and whether they are serial/open chain or parallel/closed chain.

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

Each of thehe rotary axes can be installed at the spindle or at the table, resulting in 4 combinations. The possibilities have different results in respect to workpiece size, stability etc., but it is not discussed here.

It is common practice to name the rotary axes ABC matching XYZ coordinates, so if AC is used, A is parallel to the X axis and C is parallel to the Z axis when they are in 0 degree positions.

In G-Code G0, G1 the XYZ letters are cartesian coordinates and AC (or BC, AB) are the rotary angles, i. e. different units of measurement is used. An alternative G-Code uses IJK tool vectors. They are converted into AC/BC/AB angles internally by firmware.

# 4 axis palletized robot

The endpoint's horizontal orientation is ensured by mechanical parallelogram (often two connected parallelograms) based parallel arms. ABB IRB 460, EEZYbotARM (thingiverse 1454048), MK2 plus (thingiverse 2520572) and R290 3 axis are constructions following this principle.

Advantages and disadvantes probably are
* higher stiffness and precision than a pure serial scara, probably higher payload
* three actuators are near the base, so less weight toward the endpoint
* parallel is more difficult to calculate (but that's not your problem)

Currently, 2 * 2 subtypes are defined
* endpoint like a hotend is assembled at the end of the robot arms
* endpoint is used as print bed or workpiece bed and moved by the robot arms, the hotend/drill is in fixed position. The XYZ movements of the robot are reverse to the G-Code movements.
* each of the two types can be with or without the 4th axis at the endpoint. Without the 4th axis, the hotend cannot be rotated, but for most tasks it may be sufficient. Should be called 3 axis palletized then, but the kinematics are very similar, so they are described (and implemented) together.

### Homing, measurement:

As example a 3 axis palletize robot which can be found as R290 3 axis robot (a 4th actuator can be installed at the endpoint to be able to rotate the hotend):

![robot_3axispallet_measure_v2.png](/manual/configuration/robot_3axispallet_measure_v2.png)

The first stepper drives axis 1 and results in the polar direction. Stepper 2 drives the first big arm. Stepper 3 changes the second big arm by small arms inside the first big arm. Stepper 4, if it exists, allow rotation of the endpoint around the Z axis and will change polar direction also if the tool has an XY offset from axis 4. A parallelogram based arm construction assures that the endpoint platform is always parallel to the base.

Axis 1 and if it exists axis 4 is configured as usual: homing and setting the angle to the correct value in respect to the choosen coordinate system.

When being homed, axis 2 and axis 3 references are the two red long lines: the lines between the joints, not the arms themselves.

The short red line is the distance between the last joint and the assembly plate in Z direction. The same offsets can be set for X and Y direction. The offsets can be set at the G10 tool offsets alternatively. When using a tool changer, it will be easier to set them separately.

The arm lengths are the lengths of the red lines also, i. e. the distance of the joints, not the physical arm lengths. The arms could have different forms (e. g. curved), this doesn't change the configured values. Curved arms could have the advantage to give bigger min&max angles.

### Mesh compensation

When no 4th axis is installed, the endpoint rotates when axis 1 rotates, so mesh compensation with a probe with XY offset <> 0 is not possible by default. A solution is to define the probe as 0,0 by cheating the tool offset, measuring and storing the results with the correct coordinates, and for normal operation with mesh compensation to change tool offset back to the tool.
