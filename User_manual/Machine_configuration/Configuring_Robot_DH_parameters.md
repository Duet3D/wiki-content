---
title: Robot Denavit-Hartenberg (DH) parameters
description: Description to describe robot parameters with examples.
published: true
date: 2022-03-03T14:11:52.981Z
tags: 
editor: markdown
dateCreated: 2022-03-03T13:41:15.633Z
---

# Denavit-Hartenberg parameters
Denavit-Hartenberg (DH) are often used in robotics to describe the configuration of a specific robot like axis orientations, arm lenghts and other properties.
# Introduction
Denavit-Hartenberg is shortened to DH in the following document. RPY is shortened for roll-pitch-yaw, J stands for Jacobian matrix, the 4 DH parameters are called theta, d, alpha and a. DOF stands for degrees of freedom.

The following images are based on video https://www.youtube.com/watch?v=nwj0xR21..., which is the license "Creative Commons Attribution licence (reuse allowed)." The following 6 axis robot is used to explain the DH parameters:
![robot_main.jpg](/manual/configuration/robot_main.jpg)

To configure the properties of the 6 axes, the Denavit-Hartenberg (DH) parametes are used, see https://en.wikipedia.org/wiki/Denavit%E2... and https://en.wikipedia.org/wiki/Robotics_c... The german wiki explains the matrices differently, please also check https://de.wikipedia.org/wiki/Denavit-Ha... . The Denavit-Harternberg parameter are explained in a separate chapter at the end of the documentation.

A good introduction to kinematics and inverse kinematics is https://www.rosroboticslearning.com, Jacobian method 2 will be used for inverse kinematics.
# Denavit-Hartenberg (DH)
Denavit-Hartenberg (DH) is an often used method to define robot axes by 4 parameters. There are new methods to define all 6 parameters, but DH is used because it is the most used method. If it proves to be insufficient, an alternative method may be implemented additionally. DH doesn't allow parameters for rotations by the Y axis, this may be too restrictive. The underlying matrix calculations support all 6 parameters (XYZ and rotations by XYZ).

The kinematics are calculated by vector based matrix calculations, where the coordinate system is changed by the joint parameters. The classic DH convention is used where Oi-1 is at Joint i (not Craig naming)

![robot_dh_main.jpg](/manual/configuration/robot_dh_main.jpg)
(image is from the wiki about DH)

The common labels theta, d, a and alpha are used in documentation and firmware. For each joint (axis), a coordinate system is defined, where the 4 parameters are applied and a matrix calculated. The total kinematics is calculated by matrix multiplication. The result is the cartesian coordinate of the endeffector and its orientation.

For a 6 axis robot with 6 rotational joints theta is variable and the other 3 DH parameters are fixed.

For prismatic joints, d is variable. Theta/a/alpha are set.

The angles are positive (right hand rule)

if axis vertical, direction to top: counterclockwise when watched from top
if axis horizontal with direction to front: counterclockwise when watched from front
Searching for a commercial robot (e. g. Kuka KR5) which resembles to robot which is planned, together with "DH, Denavit-Hartenberg, pdf" keywords result in good scientific article examples. If example values are given, they can be validated by calling B1:T, because a pitfall can be if Craig numbering of the coordinate system is used.

For the usage of the DH see M669 A parameter.
# Coordinate system
Every robot joint is connected with its own coordinate system. The coordinate system XYZ is right hand based. It's easiest to positon Z to the direction of the axis, then X, then Y by the right hand rule. Although physical axes don't have direction, the coordinate system has, and it's important to be aware of the direction. The axis' direction defines which rotation is positive or negative degrees. From looking in front at the arrow, counterclockwise are positiv degrees.

The coordinate systems are numbered O0 to O6, from O0 being the starting, base coordinated system, to O6, which is attached to the end-effector. The coordinate systems are located at joints +1, i.e. O1 is attached to joint 2 etc. This will be explained in the examples.
# DH parameters
There are 4 DH parameters, defining Z rotation and displacment, X rotation and displacement. The missing Y rotation and displacement is solved by a combined Z and X rotations with displaments when needed, so defining all 6 DOF are possible for every joint.
# Positions and orientations
The end effector's position is expressed as XYZ coordinate and is summed up by the DH parameters and tool's xyz offsets.

Orientation means how the end effector is directed in respect to the base coordination system. The DH parameters are calculated by Z and X rotations and transformations. The resulting orientation is based on Euler ZYX angles. Euler angles express an orientation by three angles. The order of angles (ZYZ and several others) is important, here we use ZYX (called RPY roll-pitch-yaw or Tait-Bryan angles, also used for describing airplane behaviour).

Example: if the end effector shall be directed vertically to the bottom e.g., like is preferred for a 3D printing hotend, the Z axis vector must be directed vertically to vector, the Euler angles are (0,0,-90).

Following are robot examples, explaining DH parameters and some specifics like singularities.
# Example 1 joint 1
For the following examples, the DH parameter table from the video https://www.youtube.com/watch?v=nwj0xR21... are used.
![robot_main.jpg](/manual/configuration/robot_main.jpg)

The example 1 is from the video, in position where all angles are 0, time 0:29 of video. Axis 1 is rotated left 90 degrees and axis 3 is rotated by 90 degree, so from arm the robot arm are horizontal.

![robot_dh_ex1j1_2.jpg](/manual/configuration/robot_dh_ex1j1_2.jpg)

![robot_dh_ex1j1_3.jpg](/manual/configuration/robot_dh_ex1j1_3.jpg)
The calculation of the DH parameters for joint 1 is:

* joint 1 is where the coordination system 0 is drawn, this is a bit confusing, but correct (named classic DH naming).
* at the beginning, Z points to top, X to right, Y right hand rule *) to the back
* theta rotation around Z axis is made, which is by vertical, by 0 degrees, so no change
* d is 352 mm direction Z to top, so coordinate originin of coordination system 1 is now 352 higher than coord system 0
* alpha -90 means rotating around X axis clockwise looking at the arrow, which means Z is changed from top to horizontal, pointing to back. X is unchanged, Y is right hand rule (pointing downwards now)
* a 70 is the distance between the Z axes for a perpendicular line between the two Z axis straights and 70 in X direction, so axis 2 is placed 70 to the right of axis 1. It is not visible, but it is the arm 1 length between joint 1 and joint 2.
* it doesn't matter whether rotation theta or displacement d is made first (or alpha or a first) (commutative), but the order of Z (theta, d) first, then X (alpha, a) is important (not commutative).

*) it is recommended to do warm-up exercises before trying. Take right hand, thumb to the X direction, middle finger which is 90 degree snapped off to the direction of Z, then index finger which is straight points to the Y direction. (BTW a minute ago I got wrong results with the rule - then I became aware that I used the left hand....)

![robot_dh_ex1j1_4handrule.jpg](/manual/configuration/robot_dh_ex1j1_4handrule.jpg)