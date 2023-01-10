---
title: Robot industrial 6 axis
description: 
published: true
date: 2023-01-10T00:37:38.839Z
tags: robot
editor: markdown
dateCreated: 2022-09-13T11:49:01.371Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

![robot_dh_main.jpg](/manual/configuration/robot_main.jpg)

# Introduction

Following is a description of the common 6 axis industrial robot. It has the following axes:
* axis 1 is vertical
* axis 2 and 3 are horizontal and parallel to each other
* axis 4 "twists" the next arm
* axis 5 is parallel to 2 and 3 again (if axis 4 is 0 degrees)
* axis 6 rotates the endpoint

The first 3 axes are mainly for XYZ positioning and axes 4-6 for orientation. Axes 4-6 are often organized as RPY, roll-pitch-yaw, axes, which is a term from aviation, because axis 4 rolls, 5 pitches and 6 yaws the object.

Most industrial robots are 6 axis robots. Leading manufacturers are ABB, Fanuc, Kuka, Yaskawa. There are also some open source ones and smaller companies like Stäubli or Mecademic. The other common type is 4 axis pallletized robot, which is based on a parallelogram as the name suggest, used for palletizing jobs with high payloads. This type is desribed in a separate document (see overview in robot tag).

The basic configuration setting is 
M669 B"robotType=6Axis"
which sets a sample robot, the same which is used as example in the document about DH parameters. The properties are from an itialian professor from a Youtube video. Starting from the basic configuration, the arm lengths and other properties can be modified easily with the single value D parameters.

6 axis robots are capable to control all 6 degrees of freedom (6DOF), but they have the difficulty that some positions and orientations are in so-called singularities. More about orientations and singularities are in the firmware document.


# orientation

Full orientation of a 6 axis robot will be described by using quaternions. G-Code ABCD are the real and 3 imaginary numbers in this order. 6 axis robot can also operated in AC or BC mode. G-Code AC or BC commands will be translated into the full orientations of the robot. Internally, all orientations are stored as rotation information as part of a transformation matrix with full information of all three coordination axes.

* G1 or G2/G3 XYZABCD describes a 6 DOF position with orientation

Currently not implemented is quaternion based segmentation, this would require a change in the main firmware code. Quaternion based rotations (slerp) have constant velocity and sound interesting for nonplanar printing or drilling, especially for B-Splines or Nurbs.

# Screw solution with Paden-Kahan subproblems (PK)

Currently, comparing to the DH example, there is one restriction: axes 1 and 2 must be at the same X Y positions, i. e. the axes must intersect (so no 70 offset in X direction). Axis 3 intersects with the spherical axes 4 to 6, as is the case with the DH example. This restrictions allow application of Paden-Kahan subproblems in closed form without any iterations.

The example is from the book of Pardos-Gotor, but for the properties of the DH example. The Matlab code from github was converted to C++ code and optimized (details about it on the firmware page).

The Paden-Kahan (PK) subproblems are used in the order PK3-PK2-PK2-PK1 (Details about the PK subproblems on the screw theory page):
* PK3 gives one or two solutions for axis 3
* first PK2 gives one or two solutions for axes 1 and 2
* second PK2 gives one or two solutions for axes 4 and 5
* PK1 gives one solution for axis 6

There are maximum of 2 * 2 * 2 * 1 = 8 solutions of the inverse kinematics calculation. When no solution is found for an angle, the nearest solution is returned. Less than 8 solutions probably always means singularity or near singularity situations. (to be verified)

On a laptop, performance is about 3.5 microseconds to calculate the 8 solutions. (performance measuring on Duet tbd)

An alternative to use PG4 with axes 2 and 3 will be tried. (tbd)

# unsorted:

Especially important is the identification of singularity areas, which must be avoided.

Care must be taken when the robot is powered off: the arms need protection against falling down with damaging result. The following possibilities avoid it
* using brakes at the joints
* weight balance of every joint, e.g. by using counterweights, so the force downward is below the detent torque and friction of the steppers/gears (servos don't have much detent torque and cannot be used for this method)
* protection by using springs or dampers

# G10 tool offsets

It is an advantage to have XY offsets, otherwise a rotation of the 6th axis doesn't have an effect on the XY positions in respect of tool's coordinate system, loosing a rank and limited orientation control.
