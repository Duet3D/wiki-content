---
title: Robot Firmware
description: details how the firmware is implemented
published: true
date: 2022-06-18T05:20:44.359Z
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

**This document is under construction, please come back to see updates.**

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

# Generalized inverse, Moore-Penrose

# Degrees of freedom, rank

Forward kinematics result in X, Y, Z and orientation, which is described by three coordination axes. Together they result in 6 parameters, which correspond to 6 degrees of freedom (DOF). A 6 axis robot can create those 6 DOFs. Every configuration of less than 6 actuators is limited in the creation of the result. A cartesian printer with three prismatic joints cannot change orientation of the endpoint, e.g., but has control over the X, Y, Z coordinates only, so it has 3 DOFs.

# Singularities

# Speed and Acceleration control

# Tool offsets and orientation

