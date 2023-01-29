---
title: Robot Screw Theory (Product of Exponentials)
description: Details of screw theory, configuration and examples
published: true
date: 2023-01-29T10:13:59.504Z
tags: robot
editor: markdown
dateCreated: 2023-01-01T09:48:16.157Z
---

# Screw Theory
> work in progress
{.is-info}

Screw theory will not be explained here, because Wikipedia, books and scientific articles can explain it much better than me (see the literature links below for recommendations). The following information provides what is needed to give an overview and configure the kinematics.

Screw Theory, also called Product of Exponentials (PoE), is an alternative to Denavit-Hartenberg (DH). DH is the most used method, but screw has some advantages:
* setup is easier, because less frames and axis definitions are necessary
* local singularities are avoided. There are no gimbal locks, polar kinematics 0,0 singularity and similar.
* inverse kinematics can be developed in closed form, i. e. as algorithms without iterations. All possible solutions (up to 8 for a 6 axis industrial robot) are calculated, so the best can be chosen.
* the closed form avoids iterating problems with big distances. The question whether a pose is reachable can be decided fast
* force/torque and trajectory planning are included in the theory with similar methods as for kinematics calculations
* probably a speedup in calculation of inverse kinematics

disadvantage:
* application of subproblems require special setups of the axes being intersecting or parallel. Some configurations cannot be calculated, but scientific research tries to solve those unsolvable problems.

# Comparison DH with Screw Theory PoE

Both approaches describe the properties of a robot, but differently:

|-|-|
|what|Denavit-Hartenberg, DH|Screw theory, PoE|
|joints|change of coordinate system|actuator axis orientation and a point on it|
|links|two DH parameters d, a|implicit through points and Gst0|
|actuator angles|through coordinate system|relative to Gst0|
|points|not explicit|points depend on setup|
|endpoint reference|not explicit|Gst0|

* so screw needs less axis descriptions, but additional reference points. The reference points depend on the setup: do axes intersect or are parallel e.g. (the more, the better)
* DH can be converted to Screw parameters by calculating forward kinematics
* Screw cannot be converted completely to DH, because Y rotations and translations are missing in DH
* both calculations are connected to transformation matrices by Rodrigues' formula

The power of screw theory shows in inverse kinematics and torque calculations to allow closed form calculations without iterations.

# Inverse kinematics

In most cases, inverse kinematics for robots are calculated by an iterative process, approaching a solution. Screw PoE uses a closed form calculation instead, calculating all possible solutions.

In the first release, the following configurations will be available as robotType templates, where only some basic settings like arm lenghts, angle and speed limits are necessary:
* robot 6 axis industrial robot with subtypes
* CNC 5 axis AC or BC type, Prusa (Pentarod, Open5x) and CoreXY with 5 axes
* 4 axis palletized robot
* perhaps 5 bar parallel scara, polar, serial scara, colinear tripteron
* serial scara
* linear gantry with 5 axis rotary (see Pardos-Gotor second example)

Stewart-Gough is postponed. It has a lot of degrees of freedom and is complex to solve.

# Paden-Kahan subproblems

It is used a process called subproblems: the whole chain is split into easier calculations by using reference points, where part of the chain can be elimininated from the formula. Paden-Kahan have developed a set of subproblem solutions for rotary joints. Other authors have added subproblems for prismatic joints or extensions.

The following closed form subproblems will be included and extended
* Paden-Kahan subproblems 1 to 5 with generalization of subproblem 2
* Pardos-Gotor subproblems 1 to 4
* extensions by others, e.g. to support parallel kinematics

Paden-Kahan subproblems:
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

PK denote Paden-Kahan subproblems, PG Pardos-Gotor ones. PK1...5 are algorithms for rotational axes, PG1...3 for prismatic analogue to PK1...3, and PG4 is a special case of PK2. Several authors  generalized PK2 (original PK2: two intersecting axes) to include parallel or skew axes.

The crossing was also defined by the "Pieper criterion". Nearly all kinematics which follow the Pieper criterion are solvable by closed form subproblems.

# C parameter
Configuration has three parts:

Direction of an axis and a point anywhere on the axis for revolute joints and a direction of the axis for prismatic/linear joints. Currently, two types of joints can be configured:
* revolute/rotational with 3 values of omega and 3 values of q. From omega and v, a v is calculated. omega are orthonormal axis values of the rotating axis. q is a point on the axis.
* translational/linear with omega being 3 values with 0 each, and v being 3 values of the direction of the axis

Endpoint position and orientation for reference angles, e.g. all angles being 0. This is called Gst(0) or HSt(0) or M. Gst(0) is a transformation matrix as described on the firmware page with a 3x3 rotation matrix and a 1x3 position vector, put together in a 4x4 matrix. It contains the orientation and position of the endpoint for given actuator angles (revolute joints) / positions (linear joints). Often all angles being 0 degrees or the home positions are taken as base for calculation of Gst(0).

Crossing points of axes and endpoint position. This can probably be calculated from the other paramters or from the DH parameters if they are provided, also.

The three parts are sufficient to calculate new endpoints with given actuator angles/positions and the inverse kinematics.

# Literature about screw theory, Paden-Kahan / canonical subproblems

With the exception of most of the books, all the following literature was available free, being Open Access, source code (e.g. on github) or as pre-printed versions. I especially recommend buying the smaller, less expensive Pardos-Gotor book.

books
* Jose Pardos-Gotor: Screw Theory for Robotics (about 200 pages). 4 additional subproblems called PG1...4, PG1...3 for translational (analogue to PK1...PK3), PG4 for parallel rotational axes
* Jose Pardos-Gotor: Screw Theory in Robotics (about 280 pages): I don't know its contents compared to the other one. It is very expensive.
* Murray/Li/Sastry book chapter 3.3.2 ff
* Lynch/Park Modern Robotics preprint 2017. Not about Paden-Kahan, but much about screw theory
* McCarthy: Introcution to Theoretical kinematics. Has some wasted pages, but is very affordable and provides good summaries about many topics including quaternions and closed chains.

articles
* 1 free + 2 parallel: Dimovski et al, Algorithmic approach to geometric...: one axis plus 2 parallel axes, without perpendicular requirement of first axis of Chen et al. PK2 modified
* 1 perpend + 2 parallel (1, 2/3, 4/5, 6): Hong et al - Algorithm and Application of Inverse Kinematics. Chen/Zhu/Zhang - Improved Inverse Kinematics Algorithm
* Elias, Wen: Canonical Subproblems..., 2022. Overview of additional subproblems, and description of 6 changed/new subproblems, e. g. extension to 3 or 4 intersecting cones. Offers a good overview of previous articles about subproblems
* PK2 extended disjoint: Yue-sheng, Ai-ping: Extension of the Second Paden-Kahan Sub-problem..., 2008. Extension of PK2 for disjoint axes.
* An, H.S.; Seo, T.W.; Lee, J.W. Generalized solution for a sub-problem of inverse kinematics based on product of exponential formula. Joining PK2 and PK3 into one subproblem.
* https://en.wikipedia.org/wiki/Paden%E2%80%93Kahan_subproblems with formulae PK1 to PK3 (draft for PK 4, 5)
* Bertold Bongardt - An analysis of the dual-complex unit circle with applications to line geometry

article, using dual quaternions
* Chen et al - Solution of an Inverse Kinematics Problem using Dual Quaternions

articles CNC 5 axis (for all with PPPRR)
* Jixiang Yang, Yusuf Altintas - Generalized kinematics of five-axis serial machines with non-singular tool path generation (2013)

dissertations
* Paden's dissertation (available as pdf),describing 4 subproblems

source code
* Pardos-Gotor Matlab code in https://github.com/DrPardosGotor/ScrewTheoryRobotics-KINEMATICS-Illustrated which also includes two pdf documents with a part of the book, especially the cases of ABB IRB 120 6 axis and the cobot 7 axis IIWAR 820
* Lynch, Park https://github.com/NxRLab/ModernRobotics
* Corke Robotics Toolbox https://petercorke.com/toolboxes/robotics-toolbox/
* Elias source for article with Matlab code: https://github.com/rpiRobotics/linear-subproblem-solutions
