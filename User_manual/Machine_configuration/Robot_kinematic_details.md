---
title: Robot kinematic details
description: Description of how the kinematic calculation works.
published: true
date: 2022-04-21T08:59:00.863Z
tags: 
editor: markdown
dateCreated: 2022-04-21T06:39:54.238Z
---

# Robot kinematic details
This page will describe how robot kinematics is implemented. Here will be placed explanation of special problems and solutions also.

Most functionality is gathered in two files: Movement\Kinematics\RobotKinematics.h and .cpp, which is C++ code and inherits its public methods from Kinematics.h and .cpp. Currently, the base is the final 3.4.0inputshaper branch of Duet3D on github.

# Forward kinematics
The public method is MotorStepsToCartesian(), which takes the stepper angles and calculates cartesian coordinates and endpoint orientation.

motorPos is an integer calculated from stepper angle, microsteps and gear ratio. It is transformed to angle degrees. getForward() calculates the chained transformations.

Every rotation and translation by the Denavit-Hartenberg (DH) parameters can be expressed by a matrix multiplication. getMatrix() loads the parameters and calculates the RTTR matrix, which means rotation by X, transform by X, then transform by Z, rotate by Z. The matrices are chained by mulitplying them. Then the tool offset and optional its orientation is chained as another tranformation/rotation matrix. Very small values of the result are set to 0.

The resulting values are the X, Y, Z, rotation by X, rotation by Y, rotation by Z values, rotations being in degrees. The rotation values are Euler angles of type ZYX. ZYX corresponds to the opposite order of the RTTR rotation/transform order (get angle rotation by Z first, Y is always 0, then by X).

# Jacobian matrix and inverse
It is an important information to know which effect each stepper's angle change has for the resulting X/Y/Z/rotX/rotY/rotZ. The connection between angles and result can be expressed by a matrix, the Jacobian matrix. It has 6 rows and n columns, n being the count of the actuators. The upper three rows are the change of X, Y, Z, the lower three have effect on the change of orientation of the endpoint.

Calculating the inverse of the Jacobian matrix allows to calculate the opposite direction, starting from cartesian coordinates and orientation, getting the angles.

If the Jacobian matrix is quadratic and the robot is not in or near a singularity, getting the inverse is easily calculated. Being in a singulartiy means loosing ranks. If the inverse cannot be calculated, a generalized inverse must be calculated. This generalized inverse calculates an approximation to an inverse, based on a least square approach. The most common approach is the Moore-Penrose algorithm, which uses singular value decomposition (SVD). The more axes, the more complex and time consuming is the calculation, which is basically calculation of eigenvalues and eigenvectors.
