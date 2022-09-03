---
title: Robot CNC 5 axis
description: Description of configuration specifics, examples, axis flavours, G-Code variants
published: true
date: 2022-09-03T23:34:22.846Z
tags: robot
editor: markdown
dateCreated: 2022-08-31T22:53:13.376Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# 5 axis CNC

A 5 axis CNC is built from three cartesian axes (including the possibility to double axes, like optionally for gantry) and two rotary axes:
* the rotary axis which is parallel to the X axis is called A axis, to Y is the B axis and to Z is the C axis when they are in 0 degree position. Two of them are used, AC, AB or BC. For AC, AB, BC there are 2 different assemblies possible, e.g. A with C assembled on it (in this case, traditionally A is called master or 4th axis and C slave or 5th axis because C is linked and depends on A), or C with A assembled on it.
* a rotating spindle has only one important orientation, the Z axis. Two rotations are sufficient to describe it's orientation.
* the rotary axes can be installed at the spindle ("head") or table, resulting in subtypes head/head, head/table, table/table
* the possibilities have different results in respect to workpiece size, stability etc., but it is not discussed here.

If for some reason the rotational axes are named UVW, the default firmware behaviour is to handle them as linear. To change it to rotational, use M485 R1 for the letters. ABCD are handled by firmware as rotational by default. The firmware must know whether its rotational to calculate speed limits and distances correctly, e. g.

In G-Code G0, G1 the XYZ letters are cartesian coordinates and AC (or BC, AB) are the rotary angles, i. e. different units of measurement is used. An alternative G-Code uses IJK tool vectors. They are converted into AC/BC/AB angles internally by firmware.

# Segmentation

A move is segmented into small straight lines. The segmentation is calculated and planned in the main process of RRF and is not part of the kinematics. The kinematics calculates forward and inverse information for the true XYZ positions, so this is equal to which is named RTCP mode.

RTCP means, that when rotating AB/AC/BC without XYZ correction, the movement would be wrong (because rotary angle changes change the XYZ position). XYZ actuators must be changed also, which is achieved by recalculation of the inverse kinematics for every segment's correct position. The smaller the segments, the better the approach to the true line. But at a cost, more processing time needed.

# G-Code

A CAD, CAM program, slicer or postprocessor will create G-Code which can be executed and is used to control the 5 axis CNC axes. There are two common addressing modes:
* using XYZ and AB or AC or BC, XYZ being mm positions and AB... being degrees
* using XYZ and IJK, XYZ being mm positions and IJK being tool vectors, the numbers are real numbers between -1.0 to +1.0 and normalized to I²+J²+K²=1. The tool vector desribes the tilt of the tool with respect to the Z axis.

The IJK is more machine independent, but less often used. It is possible to convert the system with postprocessor or alike into the other system. For example,
G1 X10 Y10 Z10 I0.5 J0.5 K0.707106
is the same as
G1 X10 Y10 Z10 B45 C45
on a BC system.

With AC and BC, one should be aware of the gimbal lock at A = 0 degrees and B = 0 degrees position. At this position, the C axis is parallel to the Z axis, which means lost rank. A solution is to restrict movements to all negative or all positive angles for A or B, in most cases negative one, because the angle range is often the greatest for negative angles. E. g. a typical A angle range is -120 to +30 degrees, so negative angles are preferred. In the CAM program there is often a setting to prefer positive or negative angles.

# DH parameters

If AC or BC in table/table mode are used, the calculation is in workpiece mode, the transformations are as follows:

![cnc5axisactransform.png](/manual/configuration/cnc5axisactransform.png)

Starting from the base of the C axis plate, the coordinate system is translated d up to be in line with the A/B axis. Then changing by the A/B angle and then translating down to the base again by -d. Then rotaing by C axis, and then adding XYZ of the linear axes and some tool offsets to get the endpoint position and orientation. Because it is in workpiece mode, which means the transformation shall start from the workpiece and not the plate, the matrix transformations between workpiece and plate must be inverted before multiplications to calculate forward and inverse kinematics.

* d translate and rotate of coordinate system so the Z axis is in the A/B axis direction: for A axis: D0:d:0.0:0:90:0.0:0.0 rotating 90 degrees by Y axis, for B axis: D0:d:0.0:0.0:90.0 rotating by 90 around the X axis. The goal of the coordinate system rotation is to get the Z axis into the direction of the A/B axis: the arrow for the A axis to the right, for B axis to the front. This defines positive angles for the A axis to be CCW if looking from right to the axis and for B axis positive angle to be CCW when looking from front to the axis.
* rotate A/B around the Z axis and change back coordinate system for case B: for A axis: D1:0:0:0:-90:0:0 and for B axis: D1:0:0:0:-90
* translate coordinate system back to plate, then rotate C axis: D2:-d:0:0:0
* D3 to D5 are the linear axes movements
* D6 are tool properties, later overwritten by G10 offset values

When calculating the chain, D0, D1 and D2 must be inverted.

Forward calculates A/C or B/C to XYZ IJK position and orientation, while inverse kinematics can be calculated from XYZ IJK, resulting in XYZ AC or XYZ BC values. IJK is the tool vector with orientation vertical on the workpiece surface.

# BC table/table example

B means the B axis is parallel to the Y axis and is master, C is parallel to the Z axis and is slave, i. e. it is assembled on top of the B axis.

The following settings were deduced from the article "Transformation of CAM Data for 5-Axis CNC Machine Spinner U5-620" by My, Cong, Hong and Bohez.

After describing how to get from the base to the workpiece by DH transformations which can be described by D0, D1 and D2, the view must be changed to workpiece view, because the tool orientation is measured against the workpiece surface. D0, D1 and D2 transformations must be inverted, order is important. Starting from D2 back to D0, the chain is from base to tool through D3, D4, D5 being movements of X, Y, Z axes, and D6 being tool offsets and length. The complete chain can be described by the M669 B parameter:

B"dnOrder=!2:!1:!0:3:4:5:6"
where ! means transformation matrix is inverted for workpiece mode.
The D0 to D6 parameters describe physical setup and coordinate systems of base, joints and tool. The G10 offsets are added to D6, so after a tool change the calculation will still be correct.

From this transformation, the forward kinematics can be calculated: starting from G-Code XYZBC (XYZ are in mm, BC in degrees) can be calculated XYZ cartesian and IJK tool vector.

The inverse kinematics is calculated by the jacobian, generalized inverse method and gets from XYZ position and IJK orientation the XYZ machine position of the linear axes and the BC angles.

The calculations allow correct positions and orientations for every segment of a move, like RTCP. It is however important to have a good path planner on the CAM side.
