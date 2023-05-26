---
title: Robot CNC 5 axis
description: Including Pentarod, Open5, CoreXY 5 axis. 5 Bar Parallel Scara
published: true
date: 2023-05-26T06:00:28.504Z
tags: robot
editor: markdown
dateCreated: 2022-08-31T22:53:13.376Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# Supported robot types 5 axis

This robot type is built from the XYZ axes and the AC/BC axes:
- XYZ is the common Cartesian, CoreXY, Delta, 5-bar-parallel-Scara, Serial Scara and are linear/rotary/combined type axes.
- AC/BC are two rotary axes each. Naming: A if parallel to the X axis, B if parallel to Y and C if parallel to Z, with 0 degrees position each. ABC can be connected to the endpoint (hotend, spindle) or table or mixed (head/table mode etc).

The following robot type are therefore supported:
- CNC 5 axis AC/BC
- Open5x, Pentarod, Prusa with AC/BC axes
- Cartesian, CoreXY with additional AC/BC
- Delta with additional AC/BC
- 5-bar-parallel Scara with additional AC/BC. It has the additional difficulty that XYZ rotates the endpoint in the Z axis.
- Serial Scara based are supported by the common robot kinematics anyway (optionally mixed with linear axes)

The current tool is added to the position with its G10 offset values, so toolchangers are supported.

RTCP mode is supported in all cases, i. e. the XYZ change due to AC/BC changes are considered and corrected by small XYZ changes.

# Configuration

The following values are for a given angle/position (for rotary/linear axes) value of the actuators.

robot type:
- parameter robotType
- additional parameters robotType-specific (additional arms, tilting etc)

for each of the 5 axes:
- type of axis, rotary or linear
- axis angle/position limits min, max and homing value
- axis direction of the main (Z) axis
- an arbitrary point on the axis

endpoint:
- reference starting point is the object's (0,0,0) point on the print bed
- position
- full orientation information (x, y, and z axis)
- for the calculation, the print bed's thickness must be considered
- the defaultToolLength is added (only Z value, XY are 0). For forward/inverse calculations, it is replaced by the G10 offset values of the selected tool.

The tool (and as starting point the defaultToolLength) is always added to the Z axis direction, no matter how the chain is defined and with which axis it ends. The length is subtracted from the Z position. (e. g. a Z position of 300 and tool length of 100 will result in a Z200 height).

In the following examples, the direction of A, B, C is in the same direction like X, Y, Z axes. This defines in which direction the axes rotate with positive and negative angles. To change this behaviour, the axis direction needs to be reverted by negating all Z axis values and the homing definitions be changed.

# Segmentation

A move is segmented into small straight lines. The segmentation is calculated and planned in the main process of RRF and is not part of the kinematics. The kinematics calculates forward and inverse information for the true XYZ positions, so this is equal to which is named RTCP mode.

RTCP means, that when rotating AB/AC/BC without XYZ correction, the movement would be wrong (because rotary angle changes change the XYZ position). XYZ actuators must be changed also, which is achieved by recalculation of the inverse kinematics for every segment's correct position. The smaller the segments, the better the approach to the true line. But at a cost, more processing time needed.

# G-Code

A CAD, CAM program, slicer or postprocessor will create G-Code which can be executed and is used to control the 5 axis CNC axes. There are two common addressing modes:
* using XYZ and AB or AC or BC, XYZ being mm positions and AB... being degrees
* UV can be used as is the case of Open5x and used as BC with the M669 P"mapDriveLetterDn" parameter
* IJK is not used, because it conflicts with G2/G3 IJ

With AC and BC, one should be aware of the gimbal lock at A = 0 degrees and B = 0 degrees position. At this position, the C axis is parallel to the Z axis, which means lost rank. For some movements, the C axis wants to rotate 180 degree with infinite speed, which is not possible.
* one solution is to restrict movements to all negative or all positive angles for A or B, in most cases negative one, because the angle range is often the greatest for negative angles. E. g. a typical A angle range is -120 to +30 degrees, so negative angles are preferred. In the CAM program there is often a setting to prefer positive or negative angles.
* another solution can be to set the C rotation speed to 0 while being in the 0 degree area. But the result will be an inexact movement. This may or may not be acceptable

# general procedure to configure

The configuration of the DH parameters, which are specified by the Dn parameters, depends on where the axes are located, their direction and where the angle 0 degree is located. The DWC plugin RobotViewer shall help configuring (this tool is in development).

A good approach is:
* set B"robotType=..." similar to the existing hardware. This will set part of the parameters. Possible values are documented on the configuration page
* issue M669 to see the current settings, especially the Dn parameter values
* decide where the reference coordination point is located, where the position and orientation doesn't change. This is called base or origin.
* build the chain from base to the hotend. Often this is base-Y-X-Tool or base-Y-X-Z-Tool
* build the chain from base to the workpiece/printobject. This chain must then be inverted and the chain elements reversed (by D!n and changing order). Often this is base-A-C-workpiece or base-Z-A-C-workpiece.
* put together the two chains, starting from the workpiece Dn, removing one base entry, ending with tool's Dn.
* assign the drives to Dn
* verify movements by issuing G1 H2 commands

From this descriptions it becomes clear that a rotary A axis located near the hotend (head mode) must be handled differently than if it is located near C and workpiece (table mode). The first is in the base-...-tool chain, the second is located in the inverted base-...-workpiece chain. It is also a difference where the Z axis is located: at the hotend like CNC gantry systems are constructed or at the print bed like a CoreXY.

# CNC, Cartesian, Prusa with 5 axis example

This robot type has 3 linear axes which control X, Y, Z positions, and two rotary axes which are assembled at head or table. Gantry type constructions have two connected Y guides and can be driven by 2 motors. They have one common drive number and are handled together by the core RRF.

The general starting point is
M669 K13 B"robotType=5AxisAC" or 5AxisBC

This defines a starting point of configuration. The configuration can be seen by calling M669 without parameters. Default are axes PPPRR (X, Y, Z are prismatic, A/B and C are rotary).

Then find out the chain. Open5x e.g. has the forward part base-Z-X-hotend and the to be inverted part base-Y-A-C-workpiece. After inverting the second part, they can be joined. In this example, D0 is workpiece, D1 the C rotary axis, D2 the A, D3 the Y, D4 is base, D5 the Z, D6 the X and D7 the tool.

# BC table/table example

B means the B axis is parallel to the Y axis and is master, C is parallel to the Z axis and is slave, i. e. it is assembled on top of the B axis.

The following settings were deduced from the article "Transformation of CAM Data for 5-Axis CNC Machine Spinner U5-620" by My, Cong, Hong and Bohez.

After describing how to get from the base to the workpiece by DH transformations which can be described by D0, D1 and D2, the view must be changed to workpiece view, because the tool orientation is measured against the workpiece surface. D0, D1 and D2 transformations must be inverted, order is important. Starting from D2 back to D0, the chain is from base to tool through D3, D4, D5 being movements of X, Y, Z axes, and D6 being tool offsets and length.

The Dn parts between workpiece, i. e. the chain workpiece - C axis - B axis - base must be inverted with Dn! commands.

The D0 to D6 parameters describe physical setup and coordinate systems of base, joints and tool. The G10 offsets are added to D6, so after a tool change the calculation will still be correct.

From this transformation, the forward kinematics can be calculated: starting from G-Code XYZBC (XYZ are in mm, BC in degrees) can be calculated XYZ cartesian and IJK tool vector.

The inverse kinematics is calculated by the jacobian, generalized inverse method and gets from XYZ position and IJK orientation the XYZ machine position of the linear axes and the BC angles.

The calculations allow correct positions and orientations for every segment of a move, like RTCP. It is however important to have a good path planner on the CAM side.

# CoreXYAC or CoreXYBC

A new configuration option allows to set most configuration settings fast:

M669 K13 B"robotType=CoreXYAC"
M669 K13 B"robotType=CoreXYBC"
AC if the rotary axis A is parallel to the X axis
or BC if the rotary axis B is parallel to the Y axis.
The rotary axis C is parallel to the Z axis.

The C rotary table is mounted on top of the A/B axis, this one is mounted on the Z axis. Z is connected to the base, as are XY axes, which are CoreXY connected. The base is a fixpoint (0,0,0), which doesn't change position and orientation and is a reference.

The firmware decides by the used letter where the connected steppers are located. The assignement between driver and Dn is done by the P"mapDriveLetterDn=..." parameter.

sets some parameters already:
* axisTypes=PPPRR is set for prismatic axes XYZ and rotary axes AC
* Dn chain, starting from the workpiece, C, A, Z inverted to the base, up to Y and X connected axes to the tool.
* orientationType is set to zaxis
* default drivers are configured for XYZAC axes

What should be added:
* A axis min, max and home angle/position settings
* Dn parameters if the rotary axes have displacements of the axes and displacements between the linear axes, so the distance between hotend and print bed is correct
* mapDriveLetterDn to assign drive numbers to Dn. 


# unsorted

CNC 5 axis has a spindle with only one orientation in Z direction (orientationType=zaxis). Two rotational axes are used to change the angle of the spindle in respect to the workpiece surface. Letters AB, AC or BC are used: A is a rotational axis in the same direction like the X axis, B like Y, C like Z axis. The angle of the spindle in respect to the workpiece surface is described as tool vector values. In the documentation about firmware is a detailed description about orientation types.

# Literature

* Open5x printer Hong et al "Open5x: Accessible 5-axis 3D printing and conformal slicing" https://arxiv.org/abs/2202.11426
