---
title: Robot industrial 6 axis
description: 
published: true
date: 2022-09-17T21:19:05.335Z
tags: robot
editor: markdown
dateCreated: 2022-09-13T11:49:01.371Z
---

# Industrial 6 axis robot
following will be a description of the specifics


# orientation

I am not aware of G-Code describing the orientation of a 6 axis DOF robot, so I'm currently using the following method:
* B"orientationType=full" must be set to use it
* describe the orientation by quaternions and use ABCD letters for the values: A for the real value of rotation and BCD for the imaginary values of the rotation axis vector. Quaternions can be directly translated into the rotation information of the transformation matrix.
* G1 or G2/G3 XYZABCD will then describe a 6 DOF position with orientation

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
