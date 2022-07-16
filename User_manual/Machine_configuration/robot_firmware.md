---
title: Robot Firmware
description: details how the firmware is implemented
published: true
date: 2022-07-16T04:08:56.381Z
tags: robot
editor: markdown
dateCreated: 2022-06-18T05:20:44.359Z
---

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

# Forward kinematics

Forward kinematics calculates from actuators' (steppers) positions to cartesian coordinates. MotorStepsToCartesian() in RobotKinematics calls Robot2 methods where all methods are located. All calculations are done with double precision. 

The robot is a chain of joints/actucators and links. The end position and orientation can be calculated by matrix multiplications of translation and rotations. The result is a position of X, Y, Z cartesian coordinates and three axis vectors of X, Y, Z directions, which describe endpoint's orientation.
# Jacobian matrix and Inverse
Forward calculation can be gathered in a Jacobian matrix of 6 or 7 lines and number of columns equal to actuator count. Three lines represent X, Y, Z change by an actuator, the last three lines represent angular change by three axes X, Y, Z, or four lines if using quaternion values. For a 6 axis robot, the result is a 6x6 or 7x6 matrix. Often it is not possible to calculate the inverse, so the generalized inverse is calculated.

# Generalized inverse, Moore-Penrose
When a Jacobian matrix is not quadratic or or has reduced rank, the so-called generalized inverse must be calculated instead. Research has developed different methods for calculation. The Moore-Penrose inverse is calculated. The algorithms are based on the article "Singular Value Decomposition and Least Square Solutions" by G. H. Golub and C. Reinsch from 1970 http://people.duke.edu/~hpgavin/SystemID/References/Golub+Reinsch-NM-1970.pdf . The kinematics calculates all segments of a G0, G1, G2, G3 move in advance of the move and caches it, so it will not interrupt the print during a move.

# Orientation, Quaternions
I've described orientation in the rotation matrix in https://docs.duet3d.com/en/User_manual/Machine_configuration/Configuring_Robot_DH_parameters already.

For full orientation 6 axis robot the followign is valid:

To avoid limitations of Euler angles, quaternions are used for calculations.
Quaternions describe orientations with 4 parameters each (a rotation angle and a vector describing the rotation axis) and are geomatrically points on a 4 dimensional sphere. Interpolations to calculate segments are implemented by using Slerp with introduction see https://en.wikipedia.org/wiki/Slerp and implementation based on Shoemake https://dl.acm.org/doi/pdf/10.1145/325165.325242
Interpolation is unambigious and the orientation change has constant velocity, which is andvantageous for constant extrusion. Slerp is much used in 3D gaming.

For CNC 5 axis, 3D printers like Open5x:

CNC has a spindle with only one orientation in Z direction. X and Y directions are not important, so not all 6 DOFs are necessary. The kinematics are calculated for position and Z direction. The Z direction is described by two of ABC axes or vector based two of IJK letters. Internally a 6xn jacobian matrix is used for 3 position lines and 3 vector lines. Open5x is built like a CNC 5 axis, so it can print similar to CNC abrasive, if hotend orientation in XY plane needs not to be changed.

For robots with less than 5 axes:

Robots with only 3 axes have no control over the orientation of the endpoint. Calculations will ignore orientation.

5 axis robots, closed chain kinematics:

tbd

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

