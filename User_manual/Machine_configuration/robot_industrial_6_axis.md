---
title: Robot industrial 6 axis
description: 
published: true
date: 2023-01-22T09:35:36.374Z
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

# 6Axis complete solution

Currently, different configurations are solved separately. Later they will be merged by branching:
* whether two axes have common XY coordinates and intersect or not
* whether two axes intersect, have skew lines or are parallel
* how axes 4 to 6 are organized
* whether linear axes are involved

# 6Axis_type1

Type 1:
* axis 1 and 2 intersect, i. e. they meet at one point
* axis 3 is arbitrary positioned
* axes 4 to 6 intersect, i. e. they build a spheric hinge

This differs from the DH example, where axis 1 and 2 have an X offset of 70.

The Paden-Kahan (PK) subproblems are used in the order PK3-PK2-PK2-PK1 (Details about the PK subproblems on the screw theory page):
* PK3 gives one or two solutions for axis 3
* first PK2 gives one or two solutions for axes 1 and 2
* second PK2 gives one or two solutions for axes 4 and 5
* PK1 gives one solution for axis 6

There are maximum of 2 * 2 * 2 * 1 = 8 solutions of the inverse kinematics calculation. When no solution is found for an angle, the nearest solution is returned. Less than 8 solutions probably always means singularity or near singularity situations. (to be verified)

Performance results: on 2 GHz laptop 30 microseconds for all solutions.

The algorithm follows Pardos-Gotor, Wikipedia and other sources. The calculation order is 3 by PK3 - 1/2 by PK2 - 4/5 by PK2 - 6 by PK1.

# 6Axis_type2

This type is like the DH example:
* axis 1 is arbitrary (but do not intersect or parallel with axis 2)
* axis 2 and 3 are parallel
* axes 4 to 6 intersect (i. e. spheric)

This type allows modeling like the DH model with an X offset of 70 between axis 1 and 2. The algorithm is implemented following Dimovski et al. The order of calculation is:
axis 1 with PK2Dim - 2/3 with PK2Dim, 4/5 with PK2, 6 with PK1. PK2Dim uses PK1 and PK3 as subproblem solvers also.

# configuring gst(0), HST(0), M

The names mean the same, are named differently in literature. It is meant a reference configuration, often when all angles are 0 or are in home position. The gst(0) is the transformation matrix T in this 0 position, containing the 3x3 rotation matrix and the 3x1 positions, as explained on the firmware page. gst(0) can be calculated by forward kinematics.

Beginning with the base axes and 0,0,0 position, the screws are chained and multiplied with gst(0). Result is the end effector pose (orientation and position). This formula is valid for all axis angles and linear movements.

When the DH parameters are set, gst(0) can be calculated from the properties, the home angles and the tool properties.

# configuring axes and axis positions

In screw theory, every axis is defined as screw, including rotation and translation. The properties differ for rotary and linear axes:
* rotary: axis direction omega and a point q somewhere on the axis
* linear: direction v of the movement

omega, q and v are all 3-value vectors. omega is normalized.

# configuring point

Paden-Kahan is based on rules which uses common axis crossing points. The points need to be defined. The fastest calculation is if those points are identical to the points on the axes in the previous step.

Example: for spherical axes 4, 5, 6, the point should be set to the point where the three axes cross. Point p is the end effector's pose, also called TCP or noap.

To easily follow Pardos-Gotor code, the points are named as
* k intersection of axis 1 and 2
* r point on axis 3
* f intersection of axes 4, 5 and 6
* p end effector TCP

The point names will be used in the object model later.

# unsorted:

Especially important is the identification of singularity areas, which must be avoided.

Care must be taken when the robot is powered off: the arms need protection against falling down with damaging result. The following possibilities avoid it
* using brakes at the joints
* weight balance of every joint, e.g. by using counterweights, so the force downward is below the detent torque and friction of the steppers/gears (servos don't have much detent torque and cannot be used for this method)
* protection by using springs or dampers

# G10 tool offsets

It is an advantage to have XY offsets, otherwise a rotation of the 6th axis doesn't have an effect on the XY positions in respect of tool's coordinate system, loosing a rank and limited orientation control.
