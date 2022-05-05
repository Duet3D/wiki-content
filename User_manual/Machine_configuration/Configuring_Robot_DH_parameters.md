---
title: Robot Denavit-Hartenberg (DH) parameters
description: Description to describe robot parameters with examples.
published: true
date: 2022-05-05T07:22:05.145Z
tags: 
editor: markdown
dateCreated: 2022-03-03T13:41:15.633Z
---

There are different methods to describe a robot configuration. Following is described the most often used method by describing each joint with 4 parameters, called Denavit-Hartenberg parameters. To describe rotation and translation in all dimensions, 6 parameters are needed, so the DH parameters cannot describe all possibilities. For this reason, a different method to define all 6 parameters is implemented. The following explanation is valid for this enhancement as well, only adding Y translations and rotations in the middle.

A visual tool to check the current configuration is currently in development.
# Denavit-Hartenberg (DH) parameters
Denavit-Hartenberg (DH) parameters are often used in robotics to describe properties of a specific robot setup like axis orientations, arm lenghts and other properties.

Denavit-Hartenberg is shortened to DH in the following document. RPY is shortened for roll-pitch-yaw, J stands for Jacobian matrix, the 4 DH parameters are called theta, d, alpha and a. DOF stands for degrees of freedom.

The following images are based on [this Youtube video](https://www.youtube.com/watch?v=nwj0xR21ldo){target=_blank}
The following 6 axis robot is used to explain the DH parameters:
![robot_main.jpg](/manual/configuration/robot_main.jpg)

Please check Wikipedia for an introduction of DH. In brevity:
* coordination axes are used to describe where the robot actuators are placed
* the Z axis is the rotation axis for a rotational actuator and prismatic axis for a linear one
* the DH parameters describe 4 of the 6 degrees of freedom to transform one coordinate system to the next: Z translate and rotate, then X translate and rotate. Not all transformations are possible (Y translate and rotate).
* the DH parameters are defined in M669 A parameter.

# Euler Angles, Orientation
Robot's endpoints have an orientation which is described as follows:
* every rotation can be described by the combination of three rotation matrices by three main axes. The order of the rotation is important. There are 12 rotation combinations which make sense. They are called by the axis they are rotated by, e.g. ZYZ means rotation by Z axis first, then Y axis, then the newly created Z axis.
* the rotations of the robot system, using DH parameters, are rotations in the order of ZYX, where Y is not rotated, so effectively only a rotation by ZX. ZYX is an order which is also used in aviation and is called RPY (roll pitch yaw).
* Rotation calculations can be calculated back: from rotation matrix to three angles. The angles are called Euler angles. In robot configuration, calculations and statistics, those Euler angles, based on ZYX/RPY, are used.
* a vertical endpoint, i. e. Z pointing downward, X and Y being parallel to the original X, Y axes, have the Euler angles (X=180,Y=0,Z=0), i. e. a rotation by 180 degree of the X axis.

A typical industrial robot has axis 4 to 6 according to roll pitch yaw configuration, which originates from aviation:

![rollpitchyaw.png](/manual/configuration/rollpitchyaw.png)

(image from https://en.wikipedia.org/wiki/Aircraft_principal_axes)

Axis 4 is the roll axis, axis 5 is pitch, 6 is yaw. If axis5 is in a 0 degree position, axis 4 and 6 are parallel, which results in a singularity situation (also called gimbal lock).

Documentation sources are wiki and e.g. https://homes.cs.washington.edu/~todorov/courses/cseP590/05_Kinematics.pdf
# Coordinate system
Every robot joint is connected with its own coordinate system. The coordinate system XYZ is right hand based. It's easiest to positon Z to the direction of the axis, then X, then Y by the right hand rule. Although physical axes don't have direction, the coordinate system has, and it's important to be aware of the direction. The axis' direction defines which rotation is positive or negative degrees. From looking in front at the arrow, counterclockwise are positiv degrees.

The coordinate systems are numbered O0 to O6, from O0 being the starting, base coordinated system, to O6, which is attached to the end-effector. The coordinate systems are located at joints +1, i.e. O1 is attached to joint 2 etc. This will be explained in the examples.
# DH parameters
There are 4 DH parameters, defining Z rotation and displacment, X rotation and displacement. The missing Y rotation and displacement is solved by a combined Z and X rotations with displaments when needed, so defining all 6 DOF are possible for every joint.
# Example 1 joint 1
For the following examples, the DH parameter table from [this video](https://www.youtube.com/watch?v=nwj0xR21ldo){target=_blank} is used.

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
# Example 1 joint 2
Let's analyze DH for joint 2 of the above robot:

![robot_dh_ex1j2.jpg](/manual/configuration/robot_dh_ex1j2.jpg)
The following steps are processed:

* for joint 2, coordinate system 1 is at the position of joint 2
* the result of the DH transformations (theta, d, alpha and a) is visuable in coordinate system 2, drawed at the position of joint 3
* first theta, rotation around Z axis, by q2-pi/2, which means rotating the variable and additionally by -90 degrees. (pi/2 is 1/4 of the circumfence 2*pi in rad). Looked from rigth to the blue arrow, minus means clockwise, so X axis rotates from right to top and Y from down to right. Theta is always the axis where the variable rotation is around. Z must be correctly set from the joint before. The direction of the axis is important, here arrow is pointing to the back. Adding a fixed -90 degrees to the variable theta rotation angle results in the unusual fact that 0 degree means the arm 2 pointing vertical to the top instead of 0 degree being a horizontal arm 2. DH allows this flexibility, the resulting complete kinematics calculation does not change (hopefully...).
* d is displacement in Z direction by 0. The displacement is in respect to i-1 coordinate system, so no offset between axis 2 and axis 3 (arms 2 and 3 are in one line)
* alpha would be rotation around X axis, but is 0. Z2 is already in the direction of the next axis, so no rotation is needed.
* a is the distance between Zi-1 and Zi axis, perpedicular to both straights. If alpha is 0, 180, 90 or -90 angles, it is the arm length.
# Example 1 joint 3
Joint 3 has DH parameters theta=q3, d=0, alpha=-pi/2 (-90 degrees), a=0.

![robot_dh_ex1j3.jpg](/manual/configuration/robot_dh_ex1j3.jpg)
The following calculations are made to convert the coordinate system from O2 to O3 (the coordinate systems are abbreviated to Oi):

* theta is unchanged. Theta is the variable axis rotation, which is axis 3 and horizontal, direction to the back, so positive angles are counterclockwise and mean lowering the arm.
* d=0 means no displacement in Z direction (of old Z2)
* alpha=-90 degree means rotating clockwise by X axis looking from above to the arrow. This results in Z axis to rotate from back to right, and Y2 from right to front Y3.
* a=0 sets the arm length to next joint to 0. The length between joint 3 and joint 4 are added to joint 4 later (as d parameter, because arms direction is axis Z now), because joint 4 doesn't change the angle between both arms. This is not optimal, because later the joint 3 and 4 parameters could be changed later to account for buidings imperfections and the addition of arm lengths is an error then.

In reality, arm 2 and arm 3 are not at the same offset. But the construction of axis 1 to arm 2 in one direction and arm 3 back the same distance makes possible to set d=0 for joints 2 and 3. This is possible, because axis 2 and 3 are parallel to each other. Arm 3 is directly above axis 1 in respect to Z1, Z2 direction. But joint 3 has 70 offset (right of in the image) of axis 1 in respect to Y2, Z3 direction. Those combining of parameter values intend to ease calculations and parallel axes don't have d values, but I recommend so set the values, so the axes coordinates are where the axes are in reality. For two reasons:
* saving calcuation effect is no argument, as there are hundreds of trigonometric calculation and 4 more is really not important
* a later introduced method to calculate back from a measured endpoint to calculation of true axis and arm lenght values is only working, if the DH parameters are placed to axes where they are in reality

DH parameters do not allow to set joint 3 to the location of axis 4, because a translation in the direction of the y axis (Y2) would be needed, and DH parameters allow only rotations and translations around Z and X axes. To solve it, additional M669 B parameters were added to define all 6 parameters to define all possible axis translations and rotations.
# Example 1 joint 4
The DH parameters for joint 4 are theta q4, d=380, alpha 90 degrees and a = 0.

![robot_dh_ex1j4.jpg](/manual/configuration/robot_dh_ex1j4.jpg)
Joint 4 is the nice roll component in the middle of the image.

The calculation of the coordinate system is:

* starting from coordinate system 3, O3, with Z3 to the right, X3 to top, Y3 to the front. Placed at joint 4 (not in the image, the reason being d)
* theta q4 means the variable is to rotate around the Z axis later. This axis is called roll in the RPY (roll-pitch-yaw)
* d=380 displayment includes the length from joint 3 to joint 5, which is possible, because joint 4 is straight.
* alpha 90 means counterclockwise rotation around X axis viewed from above. This rotates Z3 to Z4 direction to behind now, and Y4 with direction to right now.
* a=0 means there is no displacement in X axis direction.
# Example 1 joint 5
Joint 5 is also called pitch and has DH parameters theta variable, d=0, alpha=-90 (- pi/2), a=0.

![robot_dh_ex1j5.jpg](/manual/configuration/robot_dh_ex1j5.jpg)
The coordination axes are calculated as follows:

* theta is the rotation around Z axis, where the Oi-1, i.e. Z4, is used, pointing to behind. The rotation is called pitch, coming from airplane roll-pitch-yaw terminology.
* d=0 means the Z axis has no displacement between axis 5 and 6
* alpha=-90 means rotation around X4 axis clockwise, i. e. resulting in Z axis rotating from behind Z4 to right Z5, and Y4 from right to front Y5.
* a=0, because there is no displacment in the X axis direction.
# Example 1 joint 6
The DH parameters are theta variable, d=65, alpha=0 and a =0.

![robot_dh_ex1j6.jpg](/manual/configuration/robot_dh_ex1j6.jpg)
The following changes are made

* theta is 0, so there is no coordinate system change between X, Y from i-1 to i (5 to 6) axes due to Z rotation. X5, Y5 and X6, Y6 stay at their orientations
* d=65 means displacement by 65 in Z direction, which is the displacement of the coordinate systems in the image, being the O6 a bit right from O5. (O stands for origin, the origin of the coordinate system).
* alpha=0 means no rotation around X axis, so no additional change for the direction of Y or Z axis.
* a=0 means there is no displacement between Z5 and Z6 (arm length).

If joint 6 is revolute, then Z is directed to the direction of the end effector. For additional guidelines for DH parameter settings, please see Siciliano et al Robotics Modelling..., chapter 2.8.2, pages 62 f.
# Example 2
The same robot in homing position with all theta angles being set to 0 with exception of 1 and 3. The DH parameters of example 1 are used for construction setup, but now additionally with a set of actuator/stepper (theta) angles.

![robot_dh_ex2a.jpg](/manual/configuration/robot_dh_ex2a.jpg)

![robot_dh_ex2b.jpg](/manual/configuration/robot_dh_ex2b.jpg)
I abbreviate explanation, as example 1 was detailed. The following remarks:

* theta of link1 being 90 and link3 being -90, the whole robot was turned left and link 3 pointing upward, because in the 0 position, the robot is snapped off.
* the image is rotated, the direction of the coordinated systems is changed (only in the image)
* as is shown later in the video (time 8:20), the homing position is in a singularity with two lost ranks (6 joints, 4 ranks). This position must be avoided for normal operation. One effect of being near or at singularity is, that some actuators have very high acceleration/deleceration rates. The inverse kinematics cannot be calculated from inverse Jacobian matrix. Instead, Moore-Penrose must be used to find a way out of the singularity, which means higher calculation time.
# Links and additional examples
A good page is
[https://automaticaddison.com/homogeneous-transformation-matrices-using-denavit-hartenberg/](https://automaticaddison.com/homogeneous-transformation-matrices-using-denavit-hartenberg/)
with some examples, the last one a 6 axis Fanuc LR Mate 200iD robot.

A nice video for visual explanation of DH parameters is https://www.youtube.com/watch?v=rA9tm0gTln8

A good introduction to kinematics and inverse kinematics is https://www.rosroboticslearning.com, Jacobian method 2 will be used for inverse kinematics.
