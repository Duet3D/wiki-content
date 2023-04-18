---
title: Robot CNC 5 axis
description: Including Pentarod, Open5, CoreXY 5 axis. 5 Bar Parallel Scara
published: true
date: 2023-04-18T08:19:40.085Z
tags: robot
editor: markdown
dateCreated: 2022-08-31T22:53:13.376Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# Supported robot types described on this page

The following description includes the following robot types:
* CNC 5 axis AC or BC, head/head, head/table or table/table
* CoreXY or CoreXZ with AC or BC rotational axes. Z can move the table or the head similar to a CNC
* Pentarod or Open5x like
* 5BarParallelScara with AC or BC rotational axes (planned)

Those types have in common
- three linear axes for XYZ position without orientation (rotary) change. Part of the XYZ may be rotary (e. g. the 5 Bar Scara)
- two rotary axes for orientation change. They change the XYZ position as well a little bit (see RTCP below). They are named AC or BC. The axes can be intersecting or skewing, but may not be parallel.
- the current tool is added to the position with its G10 offset value

# AC, BC

The types above have a specific method to move X, Y, Z and addional two rotational axes which are called AC or BC:
* AC is using rotational axes which are parallel to the X and Z axis
* BC are axes parallel to Y and Z axis
* head/head means both rotational axes are assambled at the head, head/table is mixed and in table/table mode, both are assembled at the bottom. The possibilities have different results in respect to workpiece size, stability etc., but it is not discussed here.
* in head/head and table/table mode, one rotary axis is assembled on top of the other. Tradionally, they are named master and slave, e.g. if C is assembled on top of A, A is master, C is slave.
* a rotating spindle has only one important orientation, the Z axis. The two parameters A+C or B+C are sufficient to describe its orientation.
* if for some reason the G-Code doesn't use AC, but other letters like UV, they can be remapped with the M669 P"mapDriveLetterDn" parameter.

The most often used version is the table/table AC type. Sometimes, especially for drilling machines, A is at the head.

Defining the axes by DH parameters allow all combinations and offsets between the axes. As example, the A and C axes need not to cross.

# Configuration

The axis definitions are described on the configuration and screw pages. Denavit-Hartenberg parameters can be used as input in the RobotViewer application and transfered to screw.

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
