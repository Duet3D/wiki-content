---
title: Robot Firmware
description: details how the firmware is implemented
published: true
date: 2022-09-06T08:59:19.311Z
tags: robot
editor: markdown
dateCreated: 2022-06-18T05:20:44.359Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

Robot kinematics is based on different concepts and mathematical methods, which are described as follows. Denavit-Hartenberg has a separate dedicated document.

# Forward and inverse kinematics

The kinematics classes have two methods as core functionality: calculation of cartesian coordinates from stepper's position, called forward kinematics. And the opposite direction, called inverse kinematics.

![jacobian_geninverse.png](/manual/configuration/jacobian_geninverse.png)

For 6 axis robot and most other robot kinematics, forward kinematics is calculated by matrix multiplication of the axes, which contain translations and rotations. The result is a position and an orientation of the endpoint (hotend, CNC drill etc.). Internally, XYZ values are used for position and 4 parameters of quaternions are used for orientation.

The Jacobian matrix is a calculation of the correlation between stepper position and cartesian coordinate/orientation, when single steppers are changed (the steppers are not changed in reality, but only mathematically, as if).

An inverse must be calculated to get the inverse kinematics: calculating from cartesian coordinates back to stepper positions. In inverse can only be calculated if the Jacobian is quadratic and not singular (lost rank). In most cases, a generalized inverse must be calculated to get a similar result. The method which is described in "Singular Value Decomposition and Least Square Solutions" by G. H. Golub and C. Reinsch from 1970 http://people.duke.edu/~hpgavin/SystemID/References/Golub+Reinsch-NM-1970.pdf is used.

The jacobian and inverse values are almost exact if using small steps, i. e. small segments. The default is 0.1 segment lengths.

Calculation of inverse kinematics by using Jacobian/Gen. Inverse is calculated in iterations to get the required precision. Settings a lower required precision result in lower needed iterations.

# World coordinate or workpiece mode

This chapter is relevant if using
* CNC 5 axis
* 4 axis palletized with object on the robot plate

For forward and inverse kinematics calculations it is important to set world or workpiece mode. Default is world mode.

In world mode, the starting point is stationary and ends at the spindle or hotend endpoint. The to be printed or drilled object doesn't change position and orientation.

Setting to workpiece mode instead means that the DH parameters are transforming the coordinates from the view of the workpiece. The starting point is the workpiece and the endpoint is the tip of the drill or nozzle. This mode is important for kinematics where the printed or drilled object is rotated or moved.

Examples for workpiece mode is CNC 5 axis with rotary axes on the table like Open5x and 4 axis palletized with the print object installed on the robot endpoint and the hotend stationary outside the robot. Example configurations are provided in the robot type documents about CNC 5 axis and 4 axis palletized.

Workpiece mode means that the transformation matrices need to be multiplied in back order. I was a bit suprised, that I could invert the ZYX rotation matrix directly by inverting it. I expected that I have to change rotation/transformation order from ZYX to XYZ before inverting, but it is not the case, as A-1B-1 = (BA)-1, so X-1Y-1Z-1=(ZYX)-1, so it's sufficient to invert ZYX. Inversion of tranformation matrix looks like this:
![transfmatrixinvert_small.png](/manual/configuration/transfmatrixinvert_small.png)
T are transposes, R is the rotation matrix, t is the position matrix.

# Orientation types

Besides the position of the endpoint, orientation is also important. 3D printing mostly use vertical hotends without regard to rotation around the Z axis. But that does not have to be: a concrete printer needs a hotend which rotates into the direction of movement, e.g., to print nearly vertical walls. Non planar 3D printing need hotends with changing orientation.

The following different types of orientation description are used in internal robot kinematics code for diffferent tasks:

* three vectors in rotation matrix: orthonormal vectors, describing XYZ axis vectors of coordinate system. Used for position and orientation calculations.
* quaternions: four values, describing the vector where an object rotates around and the angle or rotation. Used internally to calculate rotation from source to target with slerp method. Abbreviation to efficiently store the three rotation vectors. Better than Euler angles, because no jerks for specific values.
* Euler angles: description of a rotation by rotating around three axes in a specific order. 12 Euler angles possible, named by the axes of rotation. E. g. ZYX to describe RPY (roll-pitch-yaw) rotation, ZYZ is also common. Euler angles have limits like lock situations (gimbal lock in ZYX), but they are well understood by people and often used.
* two angles for 5 axis CNC, Pentarod, Open5x and similar: two rotation axes are described by the two angles. A CNC spindle and in most cases 3D printer hotend don't care about Z axis rotation, so two angles are sufficient to describe the orientation. In G-Code, they are described by AB, BC or AC parameters in G1 commands. A are rotations around X axis, B around Y, C around Z.
* three values for 5 axis CNC: description of the Z axis orientation by using vector values instead of the angles. The advantage is that the description is independent of the machine implementation (can be used for AB, BC, AC unchanged). In G-Code, they are described by IJK parameters for G1 commands. It is used less often than the ABC method.
* no explicit orientation: often there is no description of orientation and the orientation is implicit through machine setup.

Of course, every object has a position and full orientation at any time, so describing it with less information means the object is not fully described. This is no problem, as long as the application doesn't need the information.

# Orientation 6 axis robot, Quaternions
I've described orientation in the rotation matrix in https://docs.duet3d.com/en/User_manual/Machine_configuration/Configuring_Robot_DH_parameters already.

To avoid limitations of Euler angles, quaternions are used for calculations. Quaternions are numbers of one real and three imaginary numbers, developed by Hamilton in 19th century, and can describe spatial rotations.

Interpolations to calculate segments are implemented by using Slerp with introduction see https://en.wikipedia.org/wiki/Slerp and implementation based on Shoemake https://dl.acm.org/doi/pdf/10.1145/325165.325242
Interpolation is unambigious and the orientation change has constant velocity, which is advantageous for constant extrusion, contrary to Euler angles. Slerp is often used in 3D gaming development.

Online translator to convert between rotation matrix and quaternion I use are https://www.andre-gaschler.com/rotationconverter/ and https://www.energid.com/resources/orientation-calculator (both show real number last for quaternions).

A nice introduction video to quaternions is https://www.youtube.com/watch?v=mHVwd8gYLnI

# Orientation 5 axis CNC, Open5x:

CNC has a spindle with only one orientation in Z direction. Two rotational axes are used to describe the orientation. Letters AB, AC or BC are used: A is a rotational axis in the same direction like the X axis, B like Y, C like Z axis.

G-Code can be described with AB, BC, AC code. The orientation is described by two angles. The calculation of the jacobian matrix is with 5 rows, 3 for position and 2 for the angles. Segmentation is calculated by interpolation of the angles.

An alternative is to use G-Code with IJK tool vectors, which can be used with G0/G1.

There is a singularity, e.g. in AC mode for A at 0 degrees. This angle must be avoided, because at 0 degrees the C axis "wants" to rotate by 180 degrees instantly for specific movements, which is not possible (infinite velocity). Often, the choosen solution is to A remain in the negative degree range.

# Orientation 4 axis palletized robot
Through parallelogram construction of the arms, the endpoint always stays in one horizontal plane, often with a vertically installed tool. Orientation is a rotation around the Z axis and one value is sufficient to describe the orientation with a Z direction indicator added. Orientation around the Z axis changes with every axis 1 rotation, and additionally by the 4th actuator, if it exists.

# Orientation for 2 or 3 axis robot:

Robots no control over the orientation of the endpoint. Calculations will ignore orientation.

# Degrees of freedom, rank

Forward kinematics result in X, Y, Z and orientation. Together they result in 6 parameters, which correspond to 6 degrees of freedom (DOF). A 6 axis robot can create those 6 DOFs. Every configuration of less than 6 actuators is limited in the creation of the result. A cartesian printer with three prismatic joints cannot change orientation of the endpoint, e.g., but has control over the X, Y, Z coordinates only, so it has 3 DOFs. A 4 or 5 axis CNC machine cannot use all 6 DOF and is restricted in the workspace (workspace in the sense of position and endpoint orientation).

# Workspace and Singularities
Workspace is the space where an object can be reached by the robot. Calculation is a combination of position and orientation. Positions near the edges should be avoided, because rotations of axes become critical and movement precision is reduced.

![robotworkspace.jpg](/manual/configuration/robotworkspace.jpg)
(image from https://www.mdpi.com/2218-6581/9/2/27/htm)

Singularities are unreachable robot positions or positions where movement results are undefined. Approaching singularities is also probelmatic, because some angle velocities are approaching infinity. See a good overview of [singularities for 6 axis robots here](https://www.mecademic.com/en/what-are-singularities-in-a-six-axis-robot-arm){target=_blank}.

Being in a singularity may be necessary when homing. In Example 2 of the DH parameter explanation, the homing position is in a singularity (a small movement of most of the axes result in massive X movement and minimal Z movement). Setting the stepper positions by homing may be a necessity. A solution is to home and set the positions, then moving specific axes with G1 H2 to a position where the robots positions are outside the singularity, then proceed. Those G1 H2 moves can be placed into the homing file.

To avoid printing in a singularity, M208 can be set accordingly. Please see the chapter about M208 for details.

Singularities will be solved by adding Moore-Penrose inverse calculation in the next release.

# Speed and Acceleration control
Near and at a singularity, the angular speed of a single or few actuators would grow to infinity and stop printing. The solution is to avoid singularities or to set those angular speed to 0 and accept some inaccuracy. The affected segments are corrected and the surrounding ones smoothing down to velocity values approaching 0 to avoid jerks. The M203 and M201 (and maybe M566) settings are used as upper angular velocity limits for each actuator.

# Tool offsets and orientation
Tool offsets are defined by G10 X, Y, Z offsets. There are no parameters for tool orientation in G10 (a proposal would be to add IJK)*), so a modification of the DH parameters directly is needed, if the tool itself is not vertical. When changing tools with a toolchanger, each tool could have it's own tilt values.

The X, Y, Z offset values are added to the tool's coordinate system. A positive Z offset with a coordinate system pointing down (coordinate system's Z axis is vector (0 0 -1), pointing down) will lower the distance between hotend and bed e.g.

The tool offset is added to the kinematics chain (the last Dn transformation matrix) before a forward kinematics is calculated. Every change of G10, a tool change eg., is immediately effective.

# Letters cartesian vs axis

Some Drive and G-Code Letters have different meanings, depending on context:
* cartesian coordinates of XYZUVW in mm position or distance
* orientation AB, BC, AC in degrees, IJK tool vector in radians
* drive letters like XYZUVWABC as reference for a specific actuator/stepper

In G-Code the two meanings are mixed unhappily, some examples:

* normal G1 X Y Z A B => X, Y, Z movements with A B rotations (5 axis CNC) or IJK for using vector tools
* G1 H2 X Y Z U V W => movements of single motors
* G31 X Y Z => X, Y, Z distances
* G10 X Y Z => offsets from reference
* M92, M203, M566, M201, M906 XYZUVW => stepper letter related
* M574 X, Y, Z => stepper letter related

# double vs float variables
Tests with forward, inverse kinematics and angle calculations resulted in differences of e-04 at worst between float and double precision variables for 6 axis robot calculations of positions and orientations. It seems acceptable to use float, because it needs less memory and can run on all newer Duet hardware with single and double precision Cortex chips (all M4F and M7 based with FPU).

# Firmware development and compilation
For installation and running robot kinematics, taking the binaries is the easiest solution. The following is only interesting if one wishes to compile or change something static inside the firmware code (e.g. using more than 6 axes).

For indidivual compilation of source, the guide https://github.com/Duet3D/RepRapFirmware/wiki/Building-RepRapFirmware should be followed. For the robot, the official Duet3D is based on the 3.4.0inputshaper branch, changed by:
* Kinematics.h and .cpp the variables robot and include RobotKinematics are added. robot is used instead of robot5axis to use K13
* Config/Pins.h set all other Kinematics SUPPORT_... to 0
* disabled delta code in ... (tbd where)
* all .h and .cpp files with names starting with Robot in folder src/Movement/Kinematics
