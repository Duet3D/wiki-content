---
title: Robot Screw Theory (Product of Exponentials)
description: Details of screw theory, configuration and examples
published: true
date: 2023-04-14T08:53:14.243Z
tags: robot
editor: markdown
dateCreated: 2023-01-01T09:48:16.157Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# Screw Theory

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

# Description of Screw Setup

An axis is described by
- axis direction of the rotating/moving axis (in DH the Z axis), e. g. (0,0,1) to rotate about the Z axis (rotary axis) or move in Z axis direction (prismatic axis)
- an arbitray point on the axis
- type of axis

The endpoint position is described
- for a given set of angles (rotary) and positions (prismatic)
- endpoint as 3-value position and 9-value orientation

For parallel kinematics
- how it's organized
- how the axes movements are related

This information is sufficient to calculate new positions when changing angles/linear positions and to calculated inverse kinematics (i e. from endposition calculating back to the angles). This is possible, because the changed angles/positions are calculated from relative changes in respect to the reference angles.

Data model:

|-|-|-|-|
|element|count|data|example|
|axis|axisnr|direction vector, point on axis|0,0,1,0,0,352|
|axis type|1|type of axis and how parallel setup|RRRRRR (industrial robot)|
|endpoint|1|3 direction vectors, point of endpoint|1,0,0,0,1,0,0,0,1,500,0,712|
|endpoint angle reference|1|maxactuators * degrees or positions|0,0,0,0,0,0|

The direction vectors must be normalized (i. e. the sum of the squares is 1).

The norm which is needed for CGA points is added and cached and is not needed as input value.

# Paden-Kahan subproblems

Paden-Kahan is used to solve the inverse kinematics, i. e. for a given endpoint (position and orientation), the actuator angles or linear position shall be calculated. Often, there exists more than one solution, so all solutions are calculated.

For an introduction of the method, please see https://en.wikipedia.org/wiki/Paden%E2%80%93Kahan_subproblems

The actuator chain shall be solved by simplifying the exponential formula by
- taking out known actuator values
- handling together axes which are connected somehow, like intersection or being parallel
- finding points, vectors or distances in the chain which do not change by specific actuator changes

Paden developed an initial set of PK1 ... PKn (mainly 3) subproblems in his dissertation. It was extended for linear axes and other situations later.

I'll label Paden's subproblems as PK1...PK3 (4,5,6), Pardos-Gators' ones like he labeled it PG1..., and extensions as PK2a, PK2b etc. from several scientific articles.

The crossing was also defined by the "Pieper criterion". Nearly all kinematics which follow the Pieper criterion are solvable by closed form subproblems.

|-|
|label|property|simplification possible if...|solutions *)|
|PK1|one rotary axis|a point on the axis|1|
|PK2|two intersecting rotary axes|no|0...2 for the axis pair|
|PK3|rotary axis to given distance|distance fix|1|

*) number of angle solutions

The solutions are described on the geometric algebra detailed page, starting with PK2.

# C parameter
Configuration has three parts:

Direction of an axis and a point anywhere on the axis for revolute joints and a direction of the axis for prismatic/linear joints. Currently, two types of joints can be configured:
* revolute/rotational with 3 values of omega and 3 values of q. From omega and v, a v is calculated. omega are orthonormal axis values of the rotating axis. q is a point on the axis.
* translational/linear with omega being 3 values with 0 each, and v being 3 values of the direction of the axis

Endpoint position and orientation for reference angles, e.g. all angles being 0. This is called Gst(0) or HSt(0) or M. Gst(0) is a transformation matrix as described on the firmware page with a 3x3 rotation matrix and a 1x3 position vector, put together in a 4x4 matrix. It contains the orientation and position of the endpoint for given actuator angles (revolute joints) / positions (linear joints). Often all angles being 0 degrees or the home positions are taken as base for calculation of Gst(0).

Crossing points of axes and endpoint position. This can probably be calculated from the other paramters or from the DH parameters if they are provided, also. Due to rounding errors of the float numbers, intersections may not be detected, so there will be an explicit method in configuration to tell firmware where intersections are located. => tbd

The three parts are sufficient to calculate new endpoints with given actuator angles/positions and the inverse kinematics.

# Overview of mathematical methods

* Screw configuration or extended Denavit-Hartenberg is used for setup and calculating forward kinematics
* screw theory is used for setup and formula basis for Paden-Kahan based inverse kinematics
* geometric algebra (Clifford algebra, multivectors, GA) is used to deduce the algorithms for Paden-Kahan subproblems. See the two dedicated pages
* quaternions, dual quaternions (octonions, Cayley numbers) are used to calculate rotations and translations


# Literature about screw theory, Paden-Kahan / canonical subproblems

When searching for screw theory, searching for Chasles' theorem will give some findinds also. The theorem says that all rigid body displacements can be described by a rotation and translation (a screw).

With the exception of most of the books, all the following literature was available free, being Open Access, source code (e.g. on github) or as pre-printed versions. I especially recommend buying the smaller, less expensive Pardos-Gotor book.

books
* Jose Pardos-Gotor: Screw Theory for Robotics (about 200 pages). 4 additional subproblems called PG1...4, PG1...3 for translational (analogue to PK1...PK3), PG4 for parallel rotational axes
* Jose Pardos-Gotor: Screw Theory in Robotics (about 280 pages): I don't know its contents compared to the other one. It is very expensive.
* Murray/Li/Sastry book chapter 3.3.2 ff
* Lynch/Park Modern Robotics preprint 2017. Not about Paden-Kahan, but much about screw theory
* McCarthy: Introcution to Theoretical kinematics. Has some wasted pages, but is very affordable and provides good summaries about many topics including quaternions and closed chains.
* Peter Corke: Robotics, Vision and Control 2nd ed. 2nd ed is important, as in this edition screw theory was added as topic. Uses Matlab a lot, as Corke is an original developer of robotics toolbox and machine vision toolbox of Matlab. He offers two open source versions of the toolboxes.

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

