---
title: Robot industrial 6 axis
description: 
published: true
date: 2022-12-14T12:08:28.706Z
tags: robot
editor: markdown
dateCreated: 2022-09-13T11:49:01.371Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.


> This page is very draft. This will change when the 5 axis robots are all working ok.
{.is-info}

# Introduction

Following is a description of the common 6 axis industrial robot. It has the following axes:
* axis 1 is vertical
* axis 2 and 3 are horizontal and parallel to each other
* axis 4 "twists" the next arm
* axis 5 is parallel to 2 and 3 again (if axis 4 is 0 degrees)
* axis 6 rotates the endpoint

Most Kuka, ABB, etc. industrial robots are 6 axis robots. The other common type is 4 axis pallletized robot, which is based on a parallelogram as the name suggest, used for palletizing jobs with high payloads. This type is desribed in a separate document (see overview in robot tag).

The basic configuration setting is 
M669 B"robotType=6Axis"
which sets a sample robot, the same which is used as example in the document about DH parameters. The properties are from an itialian professor from a Youtube video. Starting from the basic configuration, the arm lengths and other properties can be modified easily with the single value D parameters.

6 axis robots are capable to control all 6 degrees of freedom (6DOF), but they have the difficulty that some positions and orientations are in so-called singularities. More about it below.


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
