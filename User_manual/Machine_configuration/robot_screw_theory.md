---
title: Robot Screw Theory (Product of Exponentials)
description: Details of screw theory, configuration and examples
published: true
date: 2023-01-05T15:08:00.217Z
tags: robot
editor: markdown
dateCreated: 2023-01-01T09:48:16.157Z
---

# Screw Theory
> work in progress
{.is-info}


Screw Theory, also called Product of Exponentials, PoE, is an alternative to Denavit-Hartenberg, DH. DH is the most used method, but screw has some advantages:
* setup is easier, because less frames and axis definitions are necessary
* local singularities are avoided. There are no gimbal locks, polar kinematics 0,0 singularity and similar.
* inverse kinematics can be developed in closed form, i. e. as algorithms without iterations. All possible solutions (up to 8 for a 6 axis industrial robot) are calculated, so the best can be chosen.
* the closed form avoids iterating problems with big distances. The question whether a pose is reachable can be decided fast
* force/torque and trajectory planning are included in the theory with similar methods as for kinematics calculations
* probably a speedup in calculation of inverse kinematics

# Inverse kinematics

In most cases, inverse kinematics for robots are calculated by an iterative process, approaching a solution. Robot kinematics uses a closed form calculation instead, calculating all possible solutions. It is used the method of Paden-Kahan to define solvable subproblems. The iterative process is included as fallback solution.

The following closed form subproblems will be included and extended
* Paden-Kahan subproblems 1 to 6 with generalization of subproblem 2
* Pardos-Gotor subproblems 1 to 4
* extensions by others, e.g. to support parallel kinematics

In the first release, the following configurations will be available as robotType templates, where only some basic settings like arm lenghts, angle and speed limits are necessary:
* robot 6 axis industrial robot
* CNC 5 axis AC or BC type, Prusa (Pentarod, Open5x) and CoreXY with 5 axes
* 4 axis palletized robot
* perhaps 5 bar parallel scara, polar, serial scara, colinear tripteron

Stewart-Gough is postponed. It has a lot of degrees of freedom and is complex to solve.

# Paden-Kahan subproblems

Inverse kinematics can be divided into sub-calculations to find all exact solutions. This is called Paden-Kahan subproblems or canonical inverse kinematics.

A 6 axis industrial robot has e. g. up to 8 possible solutions. Knowing all solutions allow to choose the best trajectory (or force/torque or whatever the criteria are) solution.

To allow Paden-Kahan, the mechanical configuration can support and ease the application of the subproblems. The following constructions will ease it:
* consecutive axes crossing at one point, e. g. spheric axes. For rotational (PK2) or translational (PG2) axes
* rotations (PK3) or translations (PG1, PG3) to a given distance
* part of the screw definition is chossing a point on the axis. This point can be choosen such that different axes share one point
* axes being parallel to each other (PG4)
* rotation about a single axis (PK1)

PK1... are Paden-Kahan subproblems, PG1... are Pardos-Gotor ones.
The PK subproblems 1 to 5 are explained in Wikipedia of the link mentioned below.

# Pieper criterion

The close form inverse kinematics are easier to solve, if consecutive axes cross or are parallel to each other. This is especially true for axes 4 to 6 of the 6 axis industrial robot, resulting in a spherical joint. It is also advantageous, if axes 1 to 3 are on one line (same X Y positions).

# C parameter
Configuration has two parts:

Direction of an axis and a point anywhere on the axis for revolute joints and a direction of the axis for prismatic/linear joints. Currently, two types of joints can be configured:
* revolute/rotational with 3 values of omega and 3 values of q. From omega and v, a v is calculated. omega are orthonormal axis values of the rotating axis. q is a point on the axis.
* translational/linear with omega being 3 values with 0 each, and v being 3 values of the direction of the axis

Endpoint position and orientation for reference angles, e.g. all angles being 0. This is called M. M is a transformation matrix as described on the firmware page with a 3x3 rotation matrix and a 1x3 position vector, put together in a 4x4 matrix. It contains the orientation and position of the endpoint for given actuator angles (revolute joints) / positions (linear joints).

The two parts are sufficient to calculate new endpoints with given actuator angles/positions.

# Literature about canonical subproblems

* Paden's dissertation (available as pdf),describing 4 subproblems
* Jose Pardos-Gotor: Screw Theory for Robotics. 4 additional subproblems called PG1...4
* Yue-sheng, Ai-ping: Extension of the Second Paden-Kahan Sub-problem..., 2008. Extension of PK2 for disjoint axes.
* Elias, Wen: Canonical Subproblems..., 2022. Overview of additional subproblems, and description of 6 changed/new subproblems, e. g. extension to 3 or 4 intersecting cones.
* https://en.wikipedia.org/wiki/Paden%E2%80%93Kahan_subproblems
* An, H.S.; Seo, T.W.; Lee, J.W. Generalized solution for a sub-problem of inverse kinematics based on product of exponential formula

# Literature about screw theory

Following are personal recommendations:
* Lynch, Park
* Murray, Li, Sastry
* Jose Pardos-Gotor: Screw Theory for Robotics
