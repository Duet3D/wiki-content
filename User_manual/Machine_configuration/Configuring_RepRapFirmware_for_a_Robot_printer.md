---
title: Configuring RepRapFirmware for a Robot printer
description: 
published: true
date: 2022-06-17T09:58:40.389Z
tags: robot
editor: markdown
dateCreated: 2022-03-03T13:05:06.424Z
---

# Configuring a Robot printer
Following is a description how to setup a 1 to 6 axis robot for 3D printing, with primary focus on an industrial 6 axis robot. The RepRapFirmware **robot firmware is in development**, binaries for testing will be provided.

Explanation and examples of Denavit-Hartenberg parameters:
[Robot Denavit-Hartenberg parameters](/User_manual/Machine_configuration/Configuring_Robot_DH_parameters)

The DWC plugin to create and visualize robot configurations is [RobotViewer](https://docs.duet3d.com/en/User_manual/Machine_configuration/RobotViewer_DWC_plugin)

The kinematics is developed for Duet3Ds RepRapFirmware and is hosted at: [RepRapFirmware_Robot](https://github.com/JoergS5/RepRapFirmware_Robot) See the readme files how to compile. There are also example config files and binaries.

The robot is dicussed in the Duet forum at: [robot thread](https://forum.duet3d.com/topic/17421/robotic-kinematics/285) and in a few additional forum threads about robot prototypes.

# Construction
![robot_main.jpg](/manual/configuration/robot_main.jpg)
The firmware can be configured to run a typical industrial 6 axis robot with 6 rotational axes, but other types with mixed rotational and prismatic axes as well, e. g. Stanford manipulator. Robots with more or less than 6 axes will be possible also.
# Denavit-Hartenberg parameters
To describe the robot setup like properties of the axes, arm lenghts and other properties, the Denavit-Hartenberg parameters are used to describe most of the properties.


# Workspace and Singularities
Workspace is the space where an object can be reached by the robot. Calculation is a combination of position and orientation. Positions near the edges should be avoided, because rotations of axes become critical and movement precision is reduced.

![robotworkspace.jpg](/manual/configuration/robotworkspace.jpg)
(image from https://www.mdpi.com/2218-6581/9/2/27/htm)

Singularities are unreachable robot positions or positions where movement results are undefined. Approaching singularities is also probelmatic, because some angle velocities are approaching infinity. See a good overview of [singularities for 6 axis robots here](https://www.mecademic.com/en/what-are-singularities-in-a-six-axis-robot-arm){target=_blank}.

Being in a singularity may be necessary when homing. In Example 2 of the DH parameter explanation, the homing position is in a singularity (a small movement of most of the axes result in massive X movement and minimal Z movement). Setting the stepper positions by homing may be a necessity. A solution is to home and set the positions, then moving specific axes with G1 H2 to a position where the robots positions are outside the singularity, then proceed. Those G1 H2 moves can be placed into the homing file.

To avoid printing in a singularity, M208 can be set accordingly. Please see the chapter about M208 for details.

Singularities will be solved by adding Moore-Penrose inverse calculation in the next release.
# M669 configuration
M669 must set kinematics type to robot by caling M669 K13. After the type is selected, the parameters values can be set. It is recommended to set the first line to define kinematics type and types of axes like: M669 K13 ARRRRRR. Every additional A setting needs a separate line.

**K13** needs to be defined.

**A** is used to define the properties of the robot.

* AT:... defines the actuator type, rotary or prismatic and how they are assembled
* AL:... defines the letters of the actuators
* AM:... defines handling of orientation
* A0...n:parameters define DH parameters with optional Y


AT:"[R]|[P]*" defines the overall configuration and number of axes. R mean revolute (rotational), P is prismatic (translational, linear) joint, T is where the tool is attached, O is where the object to be printed is attached. Branches are marked by parantheses.
* AT:"RRRRRR" means 6 axis robot with rotational axes
* AT:"RRP" means serial scara with Z axis being prismatic (prismatic means linear movement)
* AT:"PPP" means 3 axis cartesian printer.
* AT:

AL:"[X-Z,U-W,A-D*]" defines the order of axis letters assigned to the AT parameters. Example: AT:"PRR" AL:"ZXY" means the prismatic actuator is the Z axis, the two rotary ones are X and Y. The order of joint connection is PRR/ZXY, the Z axis being first. (Don't confuse ZXY axis letters with cartesian XYZ coordinates).

AM:n defines G-Code modes and whether orientation is used:
* M0 means X, Y, Z, A, B, C G-Code (axis rotation angles)
* M1 means X, Y, Z, I, J, K, U, V, W G-Code (tool vectors)
* M2 means X, Y, Z without orientation information. Forward and inverse kinematics calculate only coordinates and ignore endpoint's orientation.


**DH: Ajoint:d:theta:a:alpha:home:minangle:maxangle**

* DH (Denavit-Hartenberg) parameters are defined by:
* joint 1 is describing the transformations, so that the resulting coordinate system at axis 2
* d offset in Z direction
* theta rotation by Z axis, added to the variable theta angle (so the position of 0 degrees can be altered)
* a is the distance between Z and former Z axis. If alpha is 0, 90 or -90, it is the arm length
* alpha, which is the X axis rotation and is as high as the Z and former Z angle difference. Often 0, 180, 90 or -90 degrees
* home, min, max angles of theta if rotatioal axis. Home, min and max position in mm for a prismatic axis.

Instead of DH parameters, all 6 translations and rotations can be defined by:
**Aaxisnr:d:theta:ytr:yrot:a:alpha:home:minangle:maxangle**

ytr is transformation in direction of Y axis, yrot is a rotation for a rotational axis and translation for a prismatic axis.

When 8 values are defined, DH parameters are expected. When 10 values are defined, it is interpreted as general setting of all transformations.

Adding the possibility to define Y axes' parameters rotation and translation to give full flexibility to define the coordinates. The Z axis is rotated and translated first, then Y, then X, according to the roll-pitch-yaw (RPY) order.

8- and 10-parameter settings can be mixed, using Y translation and rotation only where needed.

A1 to A6 are the equivalent to DH parameters. A0 allows a displacement of the first axis and axis rotations.

**B** reserved

**Pn** defines 6th axis behaviour

P0 sixth axis is vertical and always in 0 degree position
P2 sixth axis is vertical and headed parallel to the x axis. Default

**Qn** quality of calculation

Q1 is fast but lowest, Q5 is slow but highest quality of calculation. The time needed to calculate depends on the processor speed. Slow and high quality means the algorithms takes more time to calculate exact results. Default is Q1. Quality can be changed anytime, e. g. to print specific object details with higher quality.

**R** Reporting modes (this parameter will change often)
* R0 no reporting, default.
* R1 prints out time information and calculation precision to the console about running core methods of robot kinematics. Helps to find the best Q value.

**Sn** Segments per second (because smooth XYZ motion is approximated by means of segmentation)

**Tn** Minimum segment length (mm) (because smooth XYZ motion is approximated by means of segmentation)

The higher S and lower T are, the better (more straight) straight line moves are, but at the cost of processing and time needed to calculate kinematics. The simulation B1:S will give information about the qualitity of the settings.
# Drive configuration
The 6 axes are named XYZUVW. Additional axes for 7 and more axis robots will be named ABC... M92 values are steps per degree for rotational axes and steps per mm for prismatic axes.
# G10 tool offset
At the end of the last axis, a tool is attached. The robot's kinematics is calculation with the G10 offsets of the currently selected tool:
* X, Y, Z are the tool's offsets in mm. Default is 0, 0, 0.
* U, V, W are the tool's roll, pitch, yaw in degrees. Default is 0, 0, 0. I'll provide an example.
# M208 configuration
M208 limits the allowable cubic area by setting X, Y, Z limits. Printing is only allowed inside this area (an execption is while homing). M208 setting can follow two strategies:
* defining a secure area where printing is always possible
* defining an area surrounding and using all "donut" shaped area. This will result in a possibility to address coordinates which are inside M208, but not reachable. The Kinematics will mention it and throw an error, because it checks M208, whether the position is reachable by arm lengths and restrictions (e. g. allowed angles), and whether it is near a singularity.
# Homing
The homing angles are specified in the M669 A parameter and can be impemented e. g. by endstops between joint's axes or by reading absolute encoder positions. G1 H2 addressing specific axes (joints). Setting the position with G1 H1 is not possible, because M208 X, Y, Z values do not match any stepper positions. When an endstop is triggered, the homing position is set by firmware code to the A value. If necessary, the value can be changed later by G92, the 6 axes being XYZUVW. For a rotational axis, the value the stepper angle * microsteps * gear ratio must be taken. The current stepper position is the nth count value when running M122.

If the homing position is in a singularity or near it, after homing the robot arms should be rotated away from it (this can be done with G1 H2 moves), before starting normal operation. The arm positions shall remain in this work mode for all following operation if possible.
# Mesh compensation
For mesh compensation to work, the print head must be in a specific orientation in respect to the XY axis while measuring. This can be done by choosing the P2 mode of M669, which means the probe stays in the same orienting in respect to the hotend and XY coordinates. While printing, other P modes can be used, because the probe offset is not important anymore. Collisions of the probe should be avoided, if the endpoint doesn't stay vertical.
# Firmware development and compilation
For installation and running robot kinematics, taking the binaries is the easiest solution. The following is only interesting if one wishes to compile or change something static inside the firmware code (e.g. using more than 6 axes).

For indidivual compilation of source, the guide https://github.com/Duet3D/RepRapFirmware/wiki/Building-RepRapFirmware should be followed. For the robot, the official Duet3D is based on the 3.4.0inputshaper branch, changed by:
* Kinematics.h and .cpp the variables robot and include RobotKinematics are added. robot is used instead of robot5axis to use K13
* Config/Pins.h set all other Kinematics SUPPORT_... to 0
* disabled delta code in ... (tbd where)
* RobotKinematics.h and .cpp in folder Movement/Kinematics
