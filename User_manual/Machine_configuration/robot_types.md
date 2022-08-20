---
title: Robot types and their specifics
description: Supported robot types with description of properties and how to configure them, prototype recommendations
published: true
date: 2022-08-20T08:02:55.377Z
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

# 4 axis palletized robot

The endpoint's horizontal orientation is ensured by mechanical parallelogram (often two connected parallelograms) based parallel arms, which are differently implemented.

Advantages and disadvantes probably are
* higher stiffness and precision than a pure serial scara, probably higher payload
* three actuators are near the base, so less weight toward the endpoint
* parallel is more difficult to calculate (but that's not your problem)

Currently, 2 * 2 subtypes are defined
* endpoint like a hotend is assembled at the end of the robot arms
* endpoint is used as print bed or workpiece bed and moved by the robot arms, the hotend/drill is in fixed position. The XYZ movements of the robot are reverse to the G-Code movements.
* each of the two types can be with or without the 4th axis at the endpoint. Without the 4th axis, the hotend cannot be rotated, but for most tasks it may be sufficient. Should be called 3 axis palletized then, but the kinematics are very similar, so they are described (and implemented) together.
