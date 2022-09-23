---
title: Robot Firmware
description: details how the firmware is implemented
published: true
date: 2022-09-23T08:16:36.975Z
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

For 6 axis robot and most other robot kinematics, forward kinematics is calculated by matrix multiplication of the axes, which contain translations and rotations. The result is a position and an orientation of the endpoint (hotend, CNC drill etc.). Internally, the full position and orientation information is used for calculations.

The Jacobian matrix is a calculation of the correlation between stepper position and cartesian coordinate/orientation, when single steppers are changed (the steppers are not changed in reality, but only mathematically, as if).

An inverse must be calculated to get the inverse kinematics: calculating from cartesian coordinates back to stepper positions. In inverse can only be calculated if the Jacobian is quadratic and not singular (lost rank). In most cases, a generalized inverse must be calculated to get a similar result. The method which is described in "Singular Value Decomposition and Least Square Solutions" by G. H. Golub and C. Reinsch from 1970 http://people.duke.edu/~hpgavin/SystemID/References/Golub+Reinsch-NM-1970.pdf is used.

The jacobian and inverse values are almost exact if using small steps, i. e. small segments. The default is 0.1 segment lengths.

Calculation of inverse kinematics by using Jacobian/Gen. Inverse is calculated in iterations to get the required precision. Settings a lower required precision result in lower needed iterations.

# G-Code to machine position

![robot_cam_to_machine.png](/manual/configuration/robot_cam_to_machine.png)

The CAM creates a G-Code file, which uses letters like G1 XYZAC or G1 XYZABCD with different meaning. XYZ are cartesian coordinates in mm, while AC (or BC, AB, ABCD) are degrees values.

**Note: IJK mode is avoided**, because it conflicts with G2/G3 IJ parameters. Instead, ABCD is used with ABC being the rotary information of a vector and D an angle, if used (zaxis mode uses ABC, full uses ABCD). Fanuc uses IJK sometimes, but the have the conflict with G2/G3 also, so they introduced special modes.

The firmware interpretes the letters in kinematics as input values. Kinematics can translate it at it's will, can combine, calculate with them, ignore them etc. For a meaningful interpretation, it needs to know what the CAM means by A letter e.g. The match is often done by convenience, but it is more safe to define the match explicitly. It is also possible that the match is not 1:1, but more motors used than letters in G-Code used.

Firmware kinematics than outputs its calculation results into machine positions and the main firmware positions the motors and prints or drills at the commanded motor positions.

# orientationType
The M669 B"orientationType" parameter specifies one of three possible orientation modes. It specifies whether and how the robot controls orientation of the endpoint.

![robot_coordinate_systems_small.jpg](/manual/configuration/robot_coordinate_systems_small.jpg)

If red is the direction of the X axis, green of Y axis and blue of the Z axis, with Z pointing down as is usual for 3D printers and CNC, the possibilities of orientation are from left to right:
* no orientation: only position is specified. An example is a cartesian printer
* Z axis orientation: only Z axis orientation is specified. The direction of X and Y axis is unspecified and can change and is out of control for the printer/CNC. Examples are normal 3D printers and CNC 3 axis.
* full orientation: all axes' orientations are under control. The axes vectors are orthonormal and righthanded, i. e. vector lengths are 1 each and all vertical on each other. A result is, that describing two vectors are sufficient to describe the orientation of all three vectors. angleaxis mode e.g. describes the Z axis by specifying 3 values for the Z axis vector and 1 value to specify the rotation around the Z axis, i. e. specifying the position of the X axis. quat(ernion) mode describes Z axis and rotation by 4 values also, but with a different method. A 0 degree angle means X axis direction and the rotation direction is specified by the direction of the Z axis vector.

# Rotation matrix
After calculation of forward inverse kinematics, the result is a 4x4 transformation matrix with information about position and orientation:

![robot_coordinates.png](/manual/configuration/robot_coordinates.png)

It contains the following information:
* X axis direction as XYZ vector in first column (red). (1 0 0) means the X axis is directed into X direction. (0 0 -1) would mean, the X axis directs to down (negative Z)
* Y axis (green) is the orientation of the Y axis
* Z axis (blue) is the orientation of the Z axis
* position XYZ (yellow) is the information about the tool tip's position

Some technical information about the transformation matrix:
* for every orientation vector, x² + y² + z² = 1
* each orientation vector is vertical to the other ones (orthonormal) and the axes are organized righthanded
* full can be uniquely translated into quaternions and reverse. Quaternions are used for storage efficiency, using 4 instead of 9 values. They can also be translated into angle-axis mode with 4 parameters, where axis is a Euler axis (not to be confused with Euler angles) and angle is the rotation around this axis.
* The four numbers (0 0 0 1) in the last line make sure that rotations and translations stay at their positions. They don't change.
* the transformation matrices are created by the Dn Denavit-Hartenberg parameters and multiplied to get forward kinematics

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

It is important to get the transformation matrix order for multiplication correct, i. e. the order of the Dn definitions, because matrix multiplication order is not commutative, i. e. A * B is different from B * A. In case of changing from world mode to workpiece mode, the transformation matrix must
* be inverted by setting the B"dnInvert" value this Dn-s
* change the Dn order to be reversed for the workpiece Dn-s

Example: D0 to D2 are BC settings for the rotary axes of CNC 5 axis BC. The workpiece is assembled on the C plate, then changing to workpiece mode means setting B"dnInvert=0:1:2" and reversing D0 to D2 values, i. e. using the original D2 values for D0 etc. For the other Dn values of the linear axes and tool Dn, the normal order is used, D3 to D6 in this example.

# Angle-Axis
Full orientation can be described by Euler axis and an angle around this axis.

Please see https://en.wikipedia.org/wiki/Euler%27s_rotation_theorem

# Quaternions
Quaternions are numbers of one real and three imaginary numbers, developed by Hamilton in 19th century, and can describe spatial rotations.
* the real number describes the rotation angle around an axis
* the three imaginary numbers describe the axis

A typical number looks like q0 + q1i + q2j + q3k, where ijk are imaginary numbers.

By using a calculation named Slerp, interpolation between two quaternions is unambigious and the orientation change has constant velocity, which is advantageous for constant extrusion, contrary to Euler angles. Slerp is often used in 3D gaming development.

Online translator to convert between rotation matrix and quaternion I use are https://www.andre-gaschler.com/rotationconverter/ and https://www.energid.com/resources/orientation-calculator (both show real number last for quaternions).

A nice introduction video to quaternions is https://www.youtube.com/watch?v=mHVwd8gYLnI

# Slerp
For segmentation of a rotation, simply dividing angles (e. g. Euler angles) is not the correct way, because the resulting segmented rotations have nonlinear velocity. The method called Slerp, which is based on quaternions, assures constant angle change and velocity of rotation.

![330px-slerp_factor_explanation.png](/manual/configuration/330px-slerp_factor_explanation.png)

(from Wikipedia https://en.wikipedia.org/wiki/Slerp).

The simple method would be to divide the line between p0 and p1 for segmentation (tendon). With slerp, the curve of the circle is divided instead.

Slerp is used in Kinematics for
* segmentation of a long move to avoid snapping into different work modes
* calculating jacobian matrix in full orientationType mode

Interpolations to calculate segments are implemented by using Slerp with introduction see https://en.wikipedia.org/wiki/Slerp and implementation based on Shoemake https://dl.acm.org/doi/pdf/10.1145/325165.325242
Firmware code follows the code of https://www.euclideanspace.com/maths/algebra/realNormedAlgebra/quaternions/slerp/index.htm


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

# Literature
An sublist of documents which were used to implement firmware algorithms. Documents of special topics will be created in the other specialized robot documents. I only list documents or links that I think are excellent.

* Wikipedia is always a great source. I use english and my own language page about a specific topic, because they often differ in details and understandability.

Rotation calculations:
* "Metrics for 3D Rotations: Comparison and Analysis" by Du Q. Hyunh. Helped me understand rotation matrix, quaternions and distances between
