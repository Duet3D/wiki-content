---
title: Robot Screw Theory (Product of Exponentials)
description: Details of screw theory, configuration and examples
published: true
date: 2023-01-19T09:36:13.754Z
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
* serial scara
* linear gantry with 5 axis rotary (see Pardos-Gotor second example)

Stewart-Gough is postponed. It has a lot of degrees of freedom and is complex to solve.

# Paden-Kahan subproblems

Paden developed a method to divide the inverse kinematics problem into solvable parts called subproblems. It has the following properties
* the subproblems are solvable by algorithms, without iterations
* all possible solutions are found (up to 8 for a 6 axis robot)
* **basic procedure is to find points** where part of the complete PoE formula can be eliminated and the rest solved

Dividing into subproblems need the construction to be dividable by common points or distances, e.g:
* consecutive axes crossing at one point, e. g. spheric axes. For rotational (PK2) or translational (PG2) axes
* rotations (PK3) or translations (PG1, PG3) to a given distance
* part of the screw definition is chossing a point on the axis. This point can be choosen such that different axes share one point
* axes being parallel to each other (PG4)
* rotation about a single axis (PK1)

Simplifications are important, because they allow elimination of PoE elements to calculate angles.

|-|-|
|PK1|one axis, two points|simplifictation: point on axis|
|PK2|two crossing axes| |
|PK3|one axis, given distance|simplification: distance point on axis|
|PK4|like PK2, but distance to two points|?|
|PG1|like PK1 with linear axis|simplification: any point on planes|
|PG2|like PK2 with linear axes| |
|PG3, PK5|like PK3 with linear axes| |
|PG4|two parallel rotational axes| |
|PK2 Yue-sheng|like PK2 without crossing axes| |
|PK2 Chen|3 axis: 1 perpendicular to two parallel| |
|PK2 Dimovski|3 axis: 1 + two parallel| |


PK1... are Paden-Kahan subproblems, PG1... are Pardos-Gotor ones. Paden only defined subproblems for rotary axes, Pardos-Gotor added similar ones for linear axes and one additional for rotary ones.

The PK subproblems 1 to 5 are explained in Wikipedia of the link mentioned below.
There are tries to define subproblems without common points, like a generalized PK2. This will be implemented also, but will probably need more processing power (i. e. less performance).

The crossing was also defined by the "Pieper criterion". Nearly all kinematics which follow the Pieper criterion are solvable by closed form subproblems.

# C parameter
Configuration has three parts:

Direction of an axis and a point anywhere on the axis for revolute joints and a direction of the axis for prismatic/linear joints. Currently, two types of joints can be configured:
* revolute/rotational with 3 values of omega and 3 values of q. From omega and v, a v is calculated. omega are orthonormal axis values of the rotating axis. q is a point on the axis.
* translational/linear with omega being 3 values with 0 each, and v being 3 values of the direction of the axis

Endpoint position and orientation for reference angles, e.g. all angles being 0. This is called M. M is a transformation matrix as described on the firmware page with a 3x3 rotation matrix and a 1x3 position vector, put together in a 4x4 matrix. It contains the orientation and position of the endpoint for given actuator angles (revolute joints) / positions (linear joints).

Crossing points of axes and endpoint position. This can probably be calculated from the other paramters or from the DH parameters if they are provided, also.

The three parts are sufficient to calculate new endpoints with given actuator angles/positions and the inverse kinematics.

# Literature about Paden-Kahan / canonical subproblems

* Paden's dissertation (available as pdf),describing 4 subproblems
* Kahan didn't disclose his results
* Jose Pardos-Gotor: Screw Theory for Robotics. 4 additional subproblems called PG1...4, PG1...3 for translational (analogue to PK1...PK3), PG4 for parallel rotational axes
* Pardos-Gotor Matlab code in https://github.com/DrPardosGotor/ScrewTheoryRobotics-KINEMATICS-Illustrated which also includes two pdf documents with a part of the book, especially the cases of ABB IRB 120 6 axis and the cobot 7 axis IIWAR 820. The formulae are only in the book and in the Matlab code
* Yue-sheng, Ai-ping: Extension of the Second Paden-Kahan Sub-problem..., 2008. Extension of PK2 for disjoint axes.
* Elias, Wen: Canonical Subproblems..., 2022. Overview of additional subproblems, and description of 6 changed/new subproblems, e. g. extension to 3 or 4 intersecting cones. Offers a good overview of previous articles about subproblems and Matlab code in https://github.com/rpiRobotics/linear-subproblem-solutions
* https://en.wikipedia.org/wiki/Paden%E2%80%93Kahan_subproblems with formulae PK1 to PK3 (draft for PK 4, 5)
* An, H.S.; Seo, T.W.; Lee, J.W. Generalized solution for a sub-problem of inverse kinematics based on product of exponential formula. Joining PK2 and PK3 into one subproblem.
* Murray/Li/Sastry book chapter 3.3.2 ff
* Dimovski et al, Algorithmic approach to geometric...: one axis plus 2 parallel axes, without perpendicular requirement of first axis of Chen et al. PK2 modified
