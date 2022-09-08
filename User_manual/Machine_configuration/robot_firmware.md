---
title: Robot Firmware
description: details how the firmware is implemented
published: true
date: 2022-09-08T17:45:11.307Z
tags: robot
editor: markdown
dateCreated: 2022-06-18T05:20:44.359Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

This document is about:
* how forward and inverse kinematics are calculated
* how positions and orientations are calculated
* orientation types and for which robot types they are applicable
* mathematical details how algorithms are implemented and on which knowledge they are based
* explanations of workpiece mode, workspace, singularities
* special topics like information about tool, speed control
* how to compile the firmware if necessary

The Denavit-Hartenberg parameters which are closely linked to the topics of this document are described in a separate document.

# Forward and inverse kinematics

The kinematics classes have two methods as core functionality: calculation of cartesian coordinates from stepper's position, called forward kinematics. And the opposite direction, called inverse kinematics.

![jacobian_geninverse.png](/manual/configuration/jacobian_geninverse.png)

For 6 axis robot and most other robot kinematics, forward kinematics is calculated by matrix multiplication of the axes, which contain translations and rotations. The result is a position and an orientation of the endpoint (hotend, CNC drill etc.). Internally, XYZ values are used for position and 4 parameters of quaternions are used for orientation.

The Jacobian matrix is a calculation of the correlation between stepper position and cartesian coordinate/orientation, when single steppers are changed (the steppers are not changed in reality, but only mathematically, as if).

An inverse must be calculated to get the inverse kinematics: calculating from cartesian coordinates back to stepper positions. In inverse can only be calculated if the Jacobian is quadratic and not singular (lost rank). In most cases, a generalized inverse must be calculated to get a similar result. The method which is described in "Singular Value Decomposition and Least Square Solutions" by G. H. Golub and C. Reinsch from 1970 http://people.duke.edu/~hpgavin/SystemID/References/Golub+Reinsch-NM-1970.pdf is used.

The jacobian and inverse values are almost exact if using small steps, i. e. small segments. The default is 0.1 segment lengths.

Calculation of inverse kinematics by using Jacobian/Gen. Inverse is calculated in iterations to get the required precision. Settings a lower required precision result in lower needed iterations.

# Transformation rotation matrix
Each object can be translated (displacement) in X, Y, Z and rotated around an arbitrary axis. Both can be described together in a 4x4 transformation matrix.

The left upper 3x3 part represents X axis (red), Y axis (green) and Z axis (blue) coordinates of the orientation change. The example below is the unchanged origin with red being (1 0 0)T, where the X axis is directing into X=1, Y=0 and Z=0. If the left column would be (0 0 1)T, the X axis would be directed upwards. The yellow box are the (X Y Z)T translate values. (T means transform of matrix)

The orientation numbers are orthonormal, i. e. for every vector x² + y² + z² = 1 and the three vectors are perpendicular to each other.

The four numbers (0 0 0 1) in the last line make sure that rotations and translations stay at their positions. They don't change.

![robot_coordinates.png](/manual/configuration/robot_coordinates.png)

The single transformation matrix is built from one Dn set of Denavit-Hartenberg parameters. Multiplying them in the correct order results in a matrix which is the complete transformation. This matrix is the endpoint position and orientation of the tool tip.
Example: in a robot with tool pointing down vertical, the third column, the Z axis, is (0 0 -1)T.

The 3x3 rotation submatrix has redundancy, because only 3 parameters are necessary to describe an orientation of one axis and 4 to describe the complete 3-axis coordinate system. Among others, the following storage methods are used, part of them are losing information:
* quaternions with 4 values to store vector (3 values) and rotation angle
* vector with 3 values to store Z axis direction in case the X and Y axis directions are not important. E.g. a CNC spindle constantly rotated, i. e. X and Y axis directions are changing, and Z direction stays (hopefully) vertical. On CNC 5 axis, the Z direction in respect to the surface changes, but X, Y still change fast.
* no direction storage

Quaternions and zaxis mode can be directly set with the B"orientationType=..." parameter, resulting in different calculations.

An alternative to quaternion storage to store all orientation information is to use Euler angles, which define three axis rotations around specific angles. There are 12 subtypes like ZYX (RPY) or ZYZ'. Euler angles have weaknesses compared to Quaternions (gimbal lock, nonlinear segmentation of rotations), so Quaternions are used.

# World coordinate or workpiece mode

This chapter is relevant if using
* CNC 5 axis
* 4 axis palletized with object on the robot plate

For forward and inverse kinematics calculations it is important to set world or workpiece mode. Default is world mode.
* workpiece mode (in gaming also called model space, object space) desribes transformations from the view of the workpiece
* world mode (in gaming called world space) describes transformations with world coordinates

When the workpiece is rotated or moved itself, calculating in workpiece mode for a part of the chain is necessary.

Examples for workpiece mode is CNC 5 axis with rotary axes on the table like Open5x and 4 axis palletized with the print object installed on the robot endpoint and the hotend stationary outside the robot. Example configurations are provided in the robot type documents about CNC 5 axis and 4 axis palletized.

Workpiece mode means that the transformation matrices need to be multiplied in back order. I was a bit suprised, that I could invert the ZYX rotation matrix (which is represented by one Dn definition) directly by inverting it. I expected that I have to change rotation/transformation order from ZYX to XYZ before inverting, but it is not the case, as A-1B-1 = (BA)-1, so X-1Y-1Z-1=(ZYX)-1, so it's sufficient to invert ZYX.

The inversion of a tranformation matrix looks like this:
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

# Quaternions
Quaternions are numbers of one real and three imaginary numbers, developed by Hamilton in 19th century, and can describe spatial rotations.
* the real number describes the rotation angle around an axis
* the three imaginary numbers describe the axis

Interpolation is unambigious and the orientation change has constant velocity, which is advantageous for constant extrusion, contrary to Euler angles. Slerp is often used in 3D gaming development.

Online translator to convert between rotation matrix and quaternion I use are https://www.andre-gaschler.com/rotationconverter/ and https://www.energid.com/resources/orientation-calculator (both show real number last for quaternions).

A nice introduction video to quaternions is https://www.youtube.com/watch?v=mHVwd8gYLnI

# Slerp
For segmentation of a rotation, simply dividing angles (e. g. Euler angles) is not the correct way, because the resulting segmented rotations have nonlinear velocity. The method called Slerp, which is based on quaternions, assures constant angle change and velocity of rotation.

![330px-slerp_factor_explanation.png](/manual/configuration/330px-slerp_factor_explanation.png)

(from Wikipedia https://en.wikipedia.org/wiki/Slerp).

The simple method would be to divide the line between p0 and p1 for segmentation (tendon). With slerp, the curve of the circle is divided instead.

Slerp is used in Kinematics for
* segmentation of a long move to avoid snapping into different work modes
* calculating jacobian matrix of the lower orientation rows

Interpolations to calculate segments are implemented by using Slerp with introduction see https://en.wikipedia.org/wiki/Slerp and implementation based on Shoemake https://dl.acm.org/doi/pdf/10.1145/325165.325242
Firmware code follows the code of https://www.euclideanspace.com/maths/algebra/realNormedAlgebra/quaternions/slerp/index.htm


# Z axis orientation
For robots where only Z axis orientation is needed, orientationType is set to zaxis and calculations of Jacobian and generalized inverse are based on positions and orientation of the Z axis only.

CNC 5 axis has a spindle with only one orientation in Z direction. Two rotational axes are used to change the angle of the spindle in respect to the workpiece surface. Letters AB, AC or BC are used: A is a rotational axis in the same direction like the X axis, B like Y, C like Z axis. The angle of the spindle in respect to the workpiece surface is described as tool vector IJK values. IJK values are coordinates in XYZ direction respectively, are values between -1 and +1 for IJ, between 0 and 1 for K and are I²+J²+K²=1 normalized.

G-Code can be described with AB, BC, AC code. The orientation is described by two angles. The calculation of the jacobian matrix is with 6 rows, 3 for position and 3 for IJK orientation of the Z axis. Segmentation is calculated by interpolation of the angles. Segmentation and how it's calculated is the task of the core RRF and not part of the kinematics code. Especially for CNC it is important to have high segmentation for better routing quality.

An alternative is to use G-Code with IJK tool vectors, which can be used with G0/G1.

There is a singularity, e.g. in AC mode for A at 0 degrees. This angle must be avoided, because at 0 degrees the C axis "wants" to rotate by 180 degrees instantly for specific movements, which is not possible (infinite velocity). In practice, the choosen solution is to A remain in the bigger degree range without crossing 0. Usually, there is a selection box in the CAM to choose angle preference positive/negative.

# no orientation
Every object has an orientation, but it is meant here that the orienation is not controlled by firmware, because the robot endpoint has an orientation which cannot be changed.

An example is a 4 axis palletized robot without 4th actuator. The orientation of the endpoint cannot be changed, only the position. By mechanical construction, the endpoint remains vertical. In reality, the X and Y axis axes change orientation with the result that probes change their offsets in respect to the hotend, so mesh compensation in the traditional manner cannot be made.

# Degrees of freedom, rank

Forward kinematics result in X, Y, Z and orientation. Together they result in 6 parameters, which correspond to 6 degrees of freedom (DOF). A 6 axis robot can create those 6 DOFs. Every configuration of less than 6 actuators is limited in the creation of the result.

Examples of reduced rank:
* a cartesian printer has 3 DOFs: control over X, Y, Z positions, but no control about the orientation of the endpoint. By mechanical means, the endpoint is always vertical.
* CNC 5 axis has a limited 6 DOFs: X, Y, Z positions and control over the orientation of the Z axis, but no control over the orientation of the X and Y axes. A CNC spindle doesn't need X, Y axis orientation control (they constantly change), so it's no problem.
* singularity situations of a 6 axis robot: in one of the singularity situations, position and orientation changes are limited. For singularity types, see singularity section.

# Workspace and Singularities
Workspace is the space where an object can be reached by the robot. Calculation is a combination of position and orientation. Positions near the edges should be avoided, because rotations of axes become critical and movement precision is reduced.

![robotworkspace.jpg](/manual/configuration/robotworkspace.jpg)
(image from https://www.mdpi.com/2218-6581/9/2/27/htm)

Singularities are unreachable robot positions or positions where movement results are undefined. Approaching singularities is also probelmatic, because some angle velocities are approaching infinity. See a good overview of [singularities for 6 axis robots here](https://www.mecademic.com/en/what-are-singularities-in-a-six-axis-robot-arm){target=_blank}.

Being in a singularity may be necessary when homing. In Example 2 of the DH parameter explanation, the homing position is in a singularity (a small movement of most of the axes result in massive X movement and minimal Z movement). Setting the stepper positions by homing may be a necessity. A solution is to home and set the positions, then moving specific axes with G1 H2 to a position where the robots positions are outside the singularity, then proceed. Those G1 H2 moves can be placed into the homing file.

To avoid printing in a singularity, M208 can be set accordingly. Please see the chapter about M208 for details.

Singularities are solved by calculating generalized inverses.

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
