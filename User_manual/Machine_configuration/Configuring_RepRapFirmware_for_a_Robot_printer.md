---
title: Configuring RepRapFirmware for a Robot printer
description: 
published: true
date: 2022-04-12T08:20:42.301Z
tags: 
editor: markdown
dateCreated: 2022-03-03T13:05:06.424Z
---

# Configuring a Robot printer
Following is a description how to setup a 2 to 10 axis robot for 3D printing, with primary focus on an industrial 6 axis robot. The RepRapFirmware **robot firmware is in development**, binaries for testing will be provided.

The kinematics is developed for RRF 3.4.0 in a github fork at: [robot RRF fork](https://github.com/JoergS5/RepRapFirmware/tree/3.4.0)

Binaries and sample config files will be provided at: [binaries, config samples](https://github.com/JoergS5/RobotBinariesAndConfig)

The robot is dicussed in the Duet forum at: [robot thread](https://forum.duet3d.com/topic/17421/robotic-kinematics/285)

Status and current limitations:
* first release of firmware will be available in second half of April '22, tested for 3 and 6 axis robot
* in first release, robot being in a singularity cannot be solved, solution to come out are G1 H2 moves (see chapter about singularities)

# Construction
![robot_main.jpg](/manual/configuration/robot_main.jpg)
The firmware can be configured to run a typical industrial 6 axis robot with 6 rotational axes, but other types with mixed rotational and prismatic axes as well, e. g. Stanford manipulator. Robots with more or less than 6 axes will be possible also.
# Denavit-Hartenberg parameters
To describe the robot setup like properties of the axes, arm lenghts and other properties, the Denavit-Hartenberg parameters are used to describe most of the properties.

The parameters and examples are described in a separate document:
[Robot Denavit-Hartenberg parameters](/User_manual/Machine_configuration/Configuring_Robot_DH_parameters)
# Singularities
Singularities are unreachable robot positions or positions where movement results are undefined. Approaching singularities is also probelmatic, because some angle velocities are approaching infinity. See a good overview of [singularities for 6 axis robots here](https://www.mecademic.com/en/what-are-singularities-in-a-six-axis-robot-arm){target=_blank}.

Trying to move to a singularity or near a singularity will result in a forbidden movement and stop the printing process.

Corresponding to singularities is being aware of the workspace, the printable area. A typical 6 axis robot has a workspace which resembles a donut, with the cross-section of a top-down nautilus.

![robotworkspace.jpg](/manual/configuration/robotworkspace.jpg)
(image from https://www.mdpi.com/2218-6581/9/2/27/htm)

Being in a singularity may be necessary when homing. In Example 2 of the DH parameter explanation, the homing position is in a singularity (a small movement of most of the axes result in massive X movement and minimal Z movement). Setting the stepper positions by homing may be a necessity. A solution is to home and set the positions, then moving specific axes with G1 H2 to a position where the robots positions are outside the singularity, then proceed. Those G1 H2 moves can be placed into the homing file.

To avoid printing in a singularity, M208 can be set accordingly. Please see the chapter about M208 for details.

Singularities will be solved by adding Moore-Penrose inverse calculation in the next release.
# M669 configuration
M669 settings can and should be split into a main parameter line and separate lines to define the A properties to avoid a too long single line. The K parameter must be set in every line. The first M669 line should specify the kinematics type and the number of joints.

Example for first M669 line: M669 K14 A6

**K** needs to be defined. While beta, K14 is used.

**A** defines the Denavit-Hartenberg parameters for n joints, starting numbering by 1.

For the explanation how to set the parameters, please see the last chapter.

Atext defines the overall configuration and number of axes. R mean rotational, P is prismatic joint.
* ARRRRRR means 6 axis robot with rotational axes
* ARRP means serial scara with Z axis being prismatic (prismatic means linear movement)

An:a:alpha:theta:d:home:min:max

* n is the number of the axis, starting with 1
* a is the distance between Z and former Z axis. If alpha is 0, 90 or -90, it is the arm length
* alpha, which is the X axis rotation and is as high as the Z and former Z angle difference. Often 0, 180, 90 or -90 degrees
* theta rotation by Z axis, added to the variable theta angle (so the position of 0 degrees can be altered)
* d offset in Z direction
* home, min, max angles of theta if rotatioal axis. Home, min and max position in mm for a prismatic axis.

**Example**: M669 K14 ARRRRRR A1:200:0:-90:0:0:-90:90

axis1's parameters:
* 200 is arm length (Z axis distance)
* 0 alpha=0, so no rotation around the X axis
* -90 theta correction (e.g. axis2 0 degree will be vertical instead of horizontal)
* 0 d, displacement in Z axis direction is 0
* 0 theta angle set when homing the axis
* -90 minimun theta angle
* 90 maximum theta angle

**B** special functions

**Pn** defines 6th axis behaviour

P0 sixth axis is vertical and always in 0 degree position
P2 sixth axis is vertical and headed parallel to the x axis. Default

**Qn** quality of calculation

Q1 is fast but lowest, Q5 is slow but highest quality of calculation. The time needed to calculate depends on the processor speed. Slow and high quality means the algorithms takes more time to calculate exact results. Default is Q1. Quality can be changed anytime, e. g. to print specific object details with higher quality.

**R[diverse]** reporting mode

R reports statistics about the current configuration:

RS creates a report about all singularities and the types of them
RPA creates a report about a safe print area without singularities or near singularities
RT:j1:j2:j3:j4:j5:j6 reports DH on-the-fly: j1 to j6 are the variables (angles for rotational joints, mm for prismatic joints), resulting in a report of xyz and orientation and Jacobian matrix.

**Sn** Segments per second (because smooth XYZ motion is approximated by means of segmentation)

**Tn** Minimum segment length (mm) (because smooth XYZ motion is approximated by means of segmentation)

The higher S and lower T are, the better (more straight) straight line moves are, but at the cost of processing and time needed to calculate kinematics. The simulation B1:S will give information about the qualitity of the settings.

**Xn Yn Zn** are cartesian coordinates of the robot stand, the DH X0Y0Z0 origin.
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