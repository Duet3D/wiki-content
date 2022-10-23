---
title: Robot industrial 6 axis
description: 
published: true
date: 2022-10-23T20:06:29.078Z
tags: robot
editor: markdown
dateCreated: 2022-09-13T11:49:01.371Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.


> This page is very draft. This will change when the 5 axis robots are all working ok.
{.is-info}


# Industrial 6 axis robot
following will be a description of the specifics


# orientation

Full orientation of a 6 axis robot will be described by using quaternions. G-Code ABCD are the real and 3 imaginary numbers in this order. 6 axis robot can also operated in AC or BC mode. G-Code AC or BC commands will be translated into the full orientations of the robot. Internally, all orientations are stored as rotation information as part of a transformation matrix with full information of all three coordination axes.

* G1 or G2/G3 XYZABCD describes a 6 DOF position with orientation

Currently not implemented is quaternion based segmentation, this would require a change in the main firmware code. Quaternion based rotations (slerp) have constant velocity and sound interesting for nonplanar printing or drilling, especially for B-Splines or Nurbs.

# unsorted:

Configuration is made by defining the Denavit-Hartenberg parameters, see the wiki about DH.

Especially important is the identification of singularity areas, which must be avoided.

Care must be taken when the robot is powered off: the arms need protection against falling down with damaging result. The following possibilities avoid it
* using brakes at the joints
* weight balance of every joint, e.g. by using counterweights, so the force downward is below the detent torque and friction of the steppers/gears (servos don't have much detent torque and cannot be used for this method)
* protection by using springs or dampers

# G10 tool offsets

It is an advantage to have XY offsets, otherwise a rotation of the 6th axis doesn't have an effect on the XY positions in respect of tool's coordinate system, loosing a rank and limited orientation control.
