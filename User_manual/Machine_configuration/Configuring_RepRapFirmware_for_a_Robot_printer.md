---
title: Configuring RepRapFirmware for a Robot printer
description: Currently for 4 to 6 axis robots
published: true
date: 2022-03-03T13:56:45.978Z
tags: 
editor: markdown
dateCreated: 2022-03-03T13:05:06.424Z
---

# Configuring a Robot printer
Following is a description how to setup a 6 axis robot for 3D printing. Removing the 4th and 3rd axis are optionally supported to allow setup of robots with less actuators. The RepRapFirmware firmware is in development, binaries for testing will be provided.
# Construction
![robot_main.jpg](/manual/configuration/robot_main.jpg)
The firmware can be configured to run a typical industrial 6 axis robot with 6 rotational axes, but other types with mixed rotational and prismatic axes as well, e. g. Stanford manipulator. Robots with more or less than 6 axes will be possible also.
# Denavit-Hartenberg parameters
To describe the robot setup like properties of the axes, arm lenghts and other properties, the Denavit-Hartenberg parameters are used to describe most of the properties.

The parameters and examples are described in a separate document:
[Robot Denavit-Hartenberg parameters](/User_manual/Machine_configuration/Configuring_Robot_DH_parameters)
# Singularities
Singularities are unreachable robot positions or positions where movement results are undefined. Approaching singularities is also probelmatic, because some angle velocities are approaching infinity. A good overview of singularities for 6 axis robots is https://www.mecademic.com/en/what-are-si...

Trying to move to a singularity or near a singularity will result in a forbidden movement and stop the printing process. A solution is to simulate the print first with B1:S (see below) and resolve errors before printing.

Corresponding to singularities is being aware of the workspace, the printable area. A typical 6 axis robot has a workspace which resembles a donut, with the cross-section of a top-down nautilus.
# M669 configuration
M669 settings can and should be split into a main parameter line and separate lines to define the A properties to avoid a too long single line. The K parameter must be set in every line. The first M669 line should specify the kinematics type and the number of joints.

Example for first M669 line: M669 K14 A6

**K** needs to be defined. While beta, K14 is used.

**A** defines the Denavit-Hartenberg parameters for n joints, starting numbering by 1.

For the explanation how to set the parameters, please see the last chapter.

An defines how many joints are used.

An:[1|0]:a:alpha:theta:d:home:min:max

n is the number of the axis, starting with 1
1 means the joint is rotational, 0 is prismatic
a is the distance between Z and former Z axis. If alpha is 0, 90 or -90, it is the arm length
alpha, which is the X axis rotation and is as high as the Z and former Z angle difference. Often 0, 180, 90 or -90 degrees
theta rotation by Z axis, added to the variable theta angle (so the position of 0 degrees can be altered)
d offset in Z direction
home, min, max angles of theta if rotatioal axis. Home, min and max position in mm for a prismatic axis.
Example: M669 K14 A1:1:200:0:-90:0:0:-90:90

axis1's parameters
1 is rotational
200 is arm length (Z axis distance)
0 alpha=0, so no rotation around the X axis
-90 theta correction (e.g. axis2 0 degree will be vertical instead of horizontal)
0 d, displacement in Z axis direction is 0
0 theta angle set when homing the axis
-90 minimun theta angle
90 maximum theta angle

**Bn[:S]** defines the calculation strategy

B1 means to calculate kinematics by Denavit-Hartenberg, inverse kinematics from calculating back pseudo jacobian (Moore-Penrose) with position controlled joints. (in first release only inverse jacobian and 6 joints)
S is an optional parameter for simulation mode, which analyses the object and tells whether all movements are feasible. S is meant as interactive command. tbd: it is unclear how a G-Code file can be parsed here. S may be discarded.

**Pn** defines 6th axis behaviour

P0 sixth axis is vertical and always in 0 degree position
P2 sixth axis is vertical and headed parallel to the x axis. Default
Qn (currently free parameter)

**R[diverse]** reporting mode

R reports statistics about the current configuration:

RS creates a report about all singularities and the types of them
RPA creates a report about a safe print area without singularities or near singularities
RT:j1:j2:j3:j4:j5:j6 reports DH on-the-fly: j1 to j6 are the variables (angles for rotational joints, mm for prismatic joints), resulting in a report of xyz and orientation and Jacobian matrix.

**Sn** Segments per second (because smooth XYZ motion is approximated by means of segmentation)

**Tn** Minimum segment length (mm) (because smooth XYZ motion is approximated by means of segmentation)

The higher S and lower T are, the better (more straight) straight line moves are, but at the cost of processing and time needed to calculate kinematics. The simulation B1:S will give information about the qualitity of the settings.

**Xn Yn Zn** are cartesian coordinates of the robot stand, the DH X0Y0Z0 origin.
# M208 configuration
M208 settings will be used to verify a secure area which is printable. I. e., kinematics checks that an object which is placed into this area is printable and not near singularities. M208 cannot be used for G1 H1 however, because stepper angles do not relate to M208 coordinates. The real print area is bigger than M208 limit and firmware allows to print inside the whole area.
# Homing
The homing angles are specified in the M669 A parameter and can be impemented e. g. by endstops between joint's axes or by reading absolute encoder positions. G1 H2 addressing specific axes (joints). Setting the position with G1 H1 is not possible, because M208 X, Y, Z values do not match any stepper positions. When an endstop is triggered, the homing position is set by firmware code to the A value. If necessary, the value can be changed later by G92, the 6 axes being XYZUVW. As value the stepper angle * microsteps * gear ratio must be taken. The current number can be reported by M122 in the count value. (todo: validate)

**Restricition**: currently, inverse kinematics is calculated by inverse Jacobian matrices, which means, singularities cannot be resolved. Some homing positions (like the mentioned video with all angles 0) have homing positions in a singularity, so movements cannot be calculated. A solution is, to home first, then rotate the steppers away from this position with G1 H2 commands into a non-singularity area, then start moving from there.
# Mesh compensation
For mesh compensation to work, the print head must be in a specific orientation in respect to the XY axis while measuring. This can be done by choosing the P2:0 mode of M669, which is orienting the hotend along the X axis. While printing, other P modes can be used, because the probe offset is not important anymore.
# Firmware details
To describe and for documentation for later maintenance, the details of the used firmware code is described in the following document. It is however not necessary to use the robot kinematics.
