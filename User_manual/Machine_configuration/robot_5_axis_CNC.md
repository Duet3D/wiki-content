---
title: Robot CNC 5 axis
description: Including Pentarod, Open5, CoreXY 5 axis
published: true
date: 2022-09-27T16:16:50.515Z
tags: robot
editor: markdown
dateCreated: 2022-08-31T22:53:13.376Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# 5 axis CNC

A 5 axis CNC is built from three cartesian axes (including the possibility to double axes, like a gantry CNC) and two rotary axes. This kinematics subtype includes Pentarod, Open5x and CoreXY based 3D printers also. This kinematics allows
* work on 5 sides without workpiece rearranging
* nonplanar work by tilting the tool

Properties:
* the rotary axis which is parallel to the X axis is called A axis, to Y is the B axis and to Z is the C axis when they are in 0 degree position. Two of them are used, AC, AB or BC. For AC, AB, BC there are 2 different assemblies possible, e.g. A with C assembled on it (in this case, traditionally A is called master or 4th axis and C slave or 5th axis because C is linked and depends on A), or C with A assembled on it.
* a rotating spindle has only one important orientation, the Z axis. Two rotations are sufficient to describe it's orientation.
* the rotary axes can be installed at the spindle ("head") or table, resulting in subtypes head/head, head/table, table/table
* the possibilities have different results in respect to workpiece size, stability etc., but it is not discussed here.

If for some reason the rotational axes are named UVW, the default firmware behaviour is to handle them as linear. To change it to rotational, use M485 R1 for the letters. ABCD are handled by firmware as rotational by default. The firmware must know whether its rotational to calculate speed limits and distances correctly, e. g.

In G-Code G0, G1 the XYZ letters are cartesian coordinates and AC (or BC, AB) are rotary angles, i. e. different units of measurement are used. An alternative G-Code uses IJK tool vectors, used e. g. by Fanuc. The letters IJ conflict with G2/G3 commands, where they have a different meaning, so IJK is not used until there is a standardized solution.

# Calculation

The following calculation is done by firmware:

![cnc5axis_forware_inverse2.jpg](/cnc5axis_forware_inverse2.jpg)

* when G-Code arrives, it is interpreted as tool tip position and orientation (tool or workpiece). I. e. specific implementations like rotary axis offsets  and where the Z distance come from (tool length, print bed thickness) do not play a role. Forward kinematics transfers this machine independent information to motor positions by calculating using the transformation matrices and tool offsets. Important is, that the G-Code AC values often do not match the AC rotary angles. They match only, if the rotary axes are spheric, i. e. without any displacements and the C axis is directly below the endpoint midpoint.
* the inverse kinematics starts from the motor positions and calculates the XYZAC tool tip positions and orientations, once again XYZAC has nothing to do with the concrete AC angles. Even XYZ do not match, because through rotation of the AC axes, they differ from the XYZ motor positions.
* a planned move is segmented into smaller straight lines with XYZAC positions. The kinematics will calculate the true motor positions for every segment. This is called RTCP.
# Segmentation

A move is segmented into small straight lines. The segmentation is calculated and planned in the main process of RRF and is not part of the kinematics. The kinematics calculates forward and inverse information for the true XYZ positions, so this is equal to which is named RTCP mode.

RTCP means, that when rotating AB/AC/BC without XYZ correction, the movement would be wrong (because rotary angle changes change the XYZ position). XYZ actuators must be changed also, which is achieved by recalculation of the inverse kinematics for every segment's correct position. The smaller the segments, the better the approach to the true line. But at a cost, more processing time needed.

# G-Code

A CAD, CAM program, slicer or postprocessor will create G-Code which can be executed and is used to control the 5 axis CNC axes. There are two common addressing modes:
* using XYZ and AB or AC or BC, XYZ being mm positions and AB... being degrees
* IJK is not used, because it conflicts with G2/G3 IJ

With AC and BC, one should be aware of the gimbal lock at A = 0 degrees and B = 0 degrees position. At this position, the C axis is parallel to the Z axis, which means lost rank. For some movements, the C axis wants to rotate 180 degree with infinite speed, which is not possible.
* one solution is to restrict movements to all negative or all positive angles for A or B, in most cases negative one, because the angle range is often the greatest for negative angles. E. g. a typical A angle range is -120 to +30 degrees, so negative angles are preferred. In the CAM program there is often a setting to prefer positive or negative angles.
* another solution can be to set the C rotation speed to 0 while being in the 0 degree area. But the result will be an inexact movement. This may or may not be acceptable

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

After describing how to get from the base to the workpiece by DH transformations which can be described by D0, D1 and D2, the view must be changed to workpiece view, because the tool orientation is measured against the workpiece surface. D0, D1 and D2 transformations must be inverted, order is important. Starting from D2 back to D0, the chain is from base to tool through D3, D4, D5 being movements of X, Y, Z axes, and D6 being tool offsets and length.

The Dn parts between workpiece, i. e. the chain workpiece - C axis - B axis - base must be inverted with Dn! commands.

The D0 to D6 parameters describe physical setup and coordinate systems of base, joints and tool. The G10 offsets are added to D6, so after a tool change the calculation will still be correct.

From this transformation, the forward kinematics can be calculated: starting from G-Code XYZBC (XYZ are in mm, BC in degrees) can be calculated XYZ cartesian and IJK tool vector.

The inverse kinematics is calculated by the jacobian, generalized inverse method and gets from XYZ position and IJK orientation the XYZ machine position of the linear axes and the BC angles.

The calculations allow correct positions and orientations for every segment of a move, like RTCP. It is however important to have a good path planner on the CAM side.

# CoreXY in CNC 5 axis mode

A new configuration option allows to set most configuration settings fast:

M669 K13 B"robotType=CoreXY:K1:AC"
if the rotary axis A is parallel to the X axis
or
M669 K13 B"robotType=CoreXY:K1:BC"
if the rotary axis B is parallel to the Y axis.
The rotary axis C is parallel to the Z axis.

sets some parameters already:
* axisTypes=PPPRR is set for prismatic axes XYZ and rotary axes AC
* Dn chain, starting from the workpiece, C, A, Z inverted to the base, up to Y and X connected axes to the tool.
* orientationType is set to zaxis
* default drivers are configured for XYZAC axes

What should be added:
* A axis min, max and home angle/position settings
* Dn parameters if the rotary axes have displacements of the axes and displacements between the linear axes, so the distance between hotend and print bed is correct

# unsorted

CNC 5 axis has a spindle with only one orientation in Z direction (orientationType=zaxis). Two rotational axes are used to change the angle of the spindle in respect to the workpiece surface. Letters AB, AC or BC are used: A is a rotational axis in the same direction like the X axis, B like Y, C like Z axis. The angle of the spindle in respect to the workpiece surface is described as tool vector values. In the documentation about firmware is a detailed description about orientation types.
