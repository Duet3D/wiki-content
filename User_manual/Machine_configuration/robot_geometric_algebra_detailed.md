---
title: robot geometric algebra detailed
description: Semantics and syntax of geometric algebra, especially conformal one (CGA)
published: true
date: 2023-04-08T07:00:12.194Z
tags: robot
editor: markdown
dateCreated: 2023-04-07T08:20:25.411Z
---

# Robot Geometric Algebra detailed

> work in progress, just started
{.is-info}


I thought it is necessary to add this detailed page, because CGA (conformal geometric algebra) is not standardized yet, so documentation in books is inconsistent
- I had problems to find the necessary information
- to document the algorithms used in RRF firmware and RobotViewer
- for users who are not interested in details to not be overwhelmed on the standard pages

The syntax follows Gaalop syntax and the content of the Hildenbrand books. Dorst is similar, but differs.

# Geometric Algebra dimension

There are several different GA dimension systems, declared as Gp,q[,r]:
- p are coordinate axes which square to 1
- q square to -1
- r squares to 0

For example the cartesian coordinate system is G3 with three real axes e1, e2 and e3 with e1² = e2² = e3² = 1 unit values.

As example, G4,1 is the conformal model (CGA) with 4 real number axes and one imaginary. G3,0,1 is projective geometric algebra (PGA) which is often used for (game) graphics. There are many other ones like G2, G3, G3,3, G9 etc.

The choosen dimension has influence on
- the capabilities
- how much memory is necessary to store the geometric objects

As example, G4,1 needs 32 (2^5, ordered by Pascal triangle) values for one variable, offering the capability of orthogonal transformations by using rotors (versors) including translates (reflect, rotate, dilate, translate). Not every object needs all values, so compressing is used.


# objects IPNS

IPNS means inner product null space.

|-|-|-|-|
|object|formula|Gaalop sample code|filled array elements|
|point|P=x + 0.5 * x² einf + e0|p=createPoint(1,2,3);|1-5, all 1-blades|a point is a sphere with 0 radius|
|vector|
|sphere|
|plane|
|circle|
|line|
|point pair|


# objects OPNS

# intersections

# angles and distances

# Denavit-Hartenberg - Screw - CGA
