---
title: Robot CNC 5 axis
description: Including Pentarod, Open5, CoreXY 5 axis. 5 Bar Parallel Scara
published: true
date: 2022-10-24T09:13:47.109Z
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

# AC, BC

The types above have a specific method to move X, Y, Z and addional two rotational axes which are called AC or BC:
* AC is using rotational axes which are parallel to the X and Z axis
* BC are axes parallel to Y and Z axis
* head/head means both rotational axes are assambled at the head, head/table is mixed and in table/table mode, both are assembled at the bottom. The possibilities have different results in respect to workpiece size, stability etc., but it is not discussed here.
* in head/head and table/table mode, one rotary axis is assembled on top of the other. Tradionally, they are named master and slave, e.g. if C is assembled on top of A, A is master, C is slave.
* a rotating spindle has only one important orientation, the Z axis. Two rotations are sufficient to describe it's orientation.
* if for some reason the G-Code doesn't use AC, but other letters like UV, they can be remapped with the M669 P"mapDriveLetterDn" parameter.

# Denavit-Hartenberg (DH)

CNC 5 axis properties are defined by DH parameters: the direction of the axes, where the 0 degree angle is located and in which direction the rotation is a positive angle, axes offsets and positional offsets of linear axes. There is a dedicated document to describe DH parameters and give examples.

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

The configuration of the DH parameters, which are specified by the Dn parameters, depends on where the axes are located, their direction and where the angle 0 degree is located. The DWC plugin RobotViewer shall help configuring (this tool is in development).

A good approach is:
* set B"robotType=..." similar to the existing hardware. This will set part of the parameters.
* issue M669 to see the current settings, especially the Dn parameter values
* decide where the reference coordination point is located, where the position and orientation doesn't change. This is called base or origin.
* build the chain from base to the hotend. Often this is base-Y-X-Tool or base-Y-X-Z-Tool
* build the chain from base to the workpiece/printobject. This chain must then be inverted and the chain elements reversed (by D!n and changing order). Often this is base-A-C-workpiece or base-Z-A-C-workpiece.
* put together the two chains, starting from the workpiece Dn, removing one base entry, ending with tool's Dn.
* assign the drives to Dn
* verify movements by issuing G1 H2 commands

From this descriptions it becomes clear that a rotary A axis located near the hotend (head mode) must be handled differently than if it is located near C and workpiece (table mode). The first is in the base-...-tool chain, the second is located in the inverted base-...-workpiece chain. It is also a difference where the Z axis is located: at the hotend like CNC gantry systems are constructed or at the print bed like a CoreXY.

# DH example AC, BC table/table mode


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

M669 K13 B"robotType=CoreXY:AC"
if the rotary axis A is parallel to the X axis
or
M669 K13 B"robotType=CoreXY:BC"
if the rotary axis B is parallel to the Y axis.
The rotary axis C is parallel to the Z axis.
The C rotary table is mounted on top of the A/B axis, this one is mounted on the Z axis. Z is connected to the base, as are XY axes, which are CoreXY connected. The base is a fixpoint (0,0,0), which doesn't change position and orientation and is a reference.

sets some parameters already:
* axisTypes=PPPRR is set for prismatic axes XYZ and rotary axes AC
* Dn chain, starting from the workpiece, C, A, Z inverted to the base, up to Y and X connected axes to the tool.
* orientationType is set to zaxis
* default drivers are configured for XYZAC axes

What should be added:
* A axis min, max and home angle/position settings
* Dn parameters if the rotary axes have displacements of the axes and displacements between the linear axes, so the distance between hotend and print bed is correct
* mapDriveLetterDn to assign drive numbers to Dn. 

Example:
* P"mapDriveLetterDn=0X4:1Y3:2Z5:3A2:4C1" for a CoreXZ system will assign the X0 and Z2 (m584) drives to D4 and D5, so they can work together. The chain will start at the workpiece, so C first, then A, then Y, then the connected XZ. D6 will be the tool, D0 if needed the offsets between workpiece and rotating C plate.

CoreXZ with Z parameter is supported to set a ratio between X and Z movement.

# unsorted

CNC 5 axis has a spindle with only one orientation in Z direction (orientationType=zaxis). Two rotational axes are used to change the angle of the spindle in respect to the workpiece surface. Letters AB, AC or BC are used: A is a rotational axis in the same direction like the X axis, B like Y, C like Z axis. The angle of the spindle in respect to the workpiece surface is described as tool vector values. In the documentation about firmware is a detailed description about orientation types.

# 5 Bar Parallel Scara

There is already a kinematics for 5 Bar Parallel Scara https://docs.duet3d.com/User_manual/Machine_configuration/Configuration_five_bar_parallel_scara but it can be used by robot kinematics also. The config is more complicated that the other robot types, because there are specific additional parameters, which must be defined somehow.

Currently the following method is used to define the config:
* B"robotType=5BarParallelScara"
* Dn and Dn+1 specifies the connected actuators to drive the two arms. The Dn xtr (= DH a) parameter specifies the proximal arm lenghts. The mapDriveLetterDn assignments of X and Y are used to get the correct Dn numbers
* Dn+2 and Dn+3 are the passive driven arms connected to Dn and Dn+1 respectively. Dn+2 and Dn+3 are then connected to close the chain. The xtr (= DH a) parameter specify the distal arm lengths from hinge to hinge (the hotend has an additional distance in cantilevered case)
* workmode and cantilevered modes are specified through special P parameters (tbd), same with angle restrictions
* the Z axis is specified like a normal prismatic axis

To summarize: D!0 base offsets, D!1 Z axis workpiece mode, D2 and D3 proximal arms, D4 and D5 distal arms, optionally D6 cantilevered, D7 tool. X and Y must be assigned to D2 and D3, D4 and D5 must be without actuators, D6 as well without actuator if cantilevered mode is used. If there would be an actuator assembled on the cantilevered arm, this would be D7 and the tool D8.

Compared to the dedicated 5BarParallelScara kinematics, the robot kinematics will allow to finetune axis properties and support the planned calibration (calculating back from hotend to joint/link properties). Disadvantage is, that calculation effort maybe higher.
