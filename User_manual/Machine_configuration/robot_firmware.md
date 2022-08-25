---
title: Robot Firmware
description: details how the firmware is implemented
published: true
date: 2022-08-25T07:27:36.601Z
tags: robot
editor: markdown
dateCreated: 2022-06-18T05:20:44.359Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# Robot firmware
The following description gives detailed information about how the firmware for robot kinematics is implemented, for the following reasons
* for the curious to know what's behind the curtain, giving confidence over what the printer does
* for other developers who want to help remove bugs or enhance the kinematics
* explain design decisions
* know the reasons for inherent limitations like printing near or at singularities

I am sure that a deep understanding of "what's under the hood" gives better results in using the firmware and hardware, for example to balance parameter settings for precision versus performance.

I try to not repeat information I provided in the other robot articles (especially about DH) and will give cross links if useful.

# Configuration setup

The robot configurations are defined by M669 settings and described in the main article. They are not finished yet, the following is missing yet
* support closed chain setups like parallel scara, delta, parallel arms, stewart
* multiple chain support with one base like Open5x, rotating both the endpoint and object, or as another example a robot hand with fingers
* maybe support other actuator types than prismatic and rotational

The parameters are read in the Config() method, stored in variables and reported by using the object model.

# Methods of calculation
Forward and inverse kinematics can be calculated with different methods. The first two are used in robot kinematics where reasonable:

* jacobian - generalized inverse - iterative
* direct
* neuronal network learning
* others like using evolutionary, random, lookup methods

The first is discussed below in detail.

The direct method needs to know some core configuration in advance and can only be used for this configuration. It is the fastest and most precise forward and inverse kinematics method. Some of the implemented robot types are implemented in this manner.
The following kinematics are implemented with the direct method:
* tbd a list

# Jacobian - Generalized Inverse

This method is used if direction calculation is not used:

![jacobian_geninverse.png](/manual/configuration/jacobian_geninverse.png)

The kinematics classes have two methods as core functionality: calculation of cartesian coordinates from stepper's position, called forward kinematics. And the opposite direction, called inverse kinematics.

For 6 axis robot and most other robot kinematics, forward kinematics is calculated by matrix multiplication of the axes, which contain translations and rotations. The result is a position and an orientation of the endpoint (hotend, CNC drill etc.). Internally, XYZ values are used for position and 4 parameters of quaternions are used for orientation.

The Jacobian matrix is a calculation of the correlation between stepper position and cartesian coordinate/orientation, when single steppers are changed (the steppers are not changed in reality, but only mathematically, as if).

An Inverse must be calculated to get the inverse kinematics: calculating from cartesian coordinates back to stepper positions. In inverse can only be calculated if the Jacobian is quadratic and not singular (lost rank). In most cases, a generalized inverse must be calculated to get a similar result.

For calculation of the generalized inverse, several methods exist. Here, the method described in "Singular Value Decomposition and Least Square Solutions" by G. H. Golub and C. Reinsch from 1970 http://people.duke.edu/~hpgavin/SystemID/References/Golub+Reinsch-NM-1970.pdf is used.

The jacobian and inverse values are almost exact if using small steps, i. e. small segments. Tests result in 0.5 mm long segments is a good candidate to calculate long distances (e.g. calculate LimitPosition to check whether the goal is reachable). For the move itself, the shortest segments are best, because rotational axes produce curves instead of straight lines, so a balance between precision and performance cost must be found. The default is 0.1 segment lengths.

Calculation of inverse kinematics by using Jacobian/Gen. Inverse is calculated in iterations to get the required precision. Settings a lower required precision result in lower needed iterations.

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

To avoid limitations of Euler angles, quaternions are used for calculations.
Quaternions describe orientations with 4 parameters each (a rotation angle and a vector describing the rotation axis) and are geomatrically points on a 4 dimensional sphere. Interpolations to calculate segments are implemented by using Slerp with introduction see https://en.wikipedia.org/wiki/Slerp and implementation based on Shoemake https://dl.acm.org/doi/pdf/10.1145/325165.325242
Interpolation is unambigious and the orientation change has constant velocity, which is andvantageous for constant extrusion. Slerp is much used in 3D gaming.

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

# Singularities
Singularities are areas which can be reached be the robot arms, but the movement at this position is not possible or not determined. The reasons are different and the implemented solutions are different also.
Types of singularities and their implemented solutions:
* reduced rank: if e. g. two actuators are moving with their axes being parallel, the rank is reduced. The robot cannot move to all 6 DOF anymore. The implemented solution is to calculate the generalized inverse (also called pseudo-invers, Moore-Penrose inverse), which approximates the exact solution by using least square methods. An example of this singularity is gimbal lock, which is known from aviation and can occur at a 6 axis robot with axis 5 at 0 degrees. Axis 4 and 6 are parallel then.

# Speed and Acceleration control
Near and at a singularity, the angular speed of a single or few actuators would grow to infinity and stop printing. The solution is to avoid singularities or to set those angular speed to 0 and accept some inaccuracy. The affected segments are corrected and the surrounding ones smoothing down to velocity values approaching 0 to avoid jerks. The M203 and M201 (and maybe M566) settings are used as upper angular velocity limits for each actuator.

# Tool offsets and orientation
Currently only X, Y, Z position information from G10 is used, later the orientation can be set by using U, V, W (or A, B, C) also. I'm aware that there are different possibilites in CNC to define tool endpoints, so this part will be enhanced.

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
