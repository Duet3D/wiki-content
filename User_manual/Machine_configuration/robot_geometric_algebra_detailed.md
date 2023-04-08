---
title: robot geometric algebra detailed
description: Semantics and syntax of geometric algebra, especially conformal one (CGA)
published: true
date: 2023-04-08T07:31:00.653Z
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

There are several different geometric dimension systems, declared as Gp,q[,r]:
- p are coordinate axes which square to 1
- q square to -1
- r squares to 0

For example,

- G3 uses three real axes e1, e2 and e3 with e1² = e2² = e3² = 1 unit values.
- CGA G4,1 uses e1²=e2²=e3²=e+²=1 and e-²=-1. e+ and e- are converted to e∞ (einf) and e0 to be used in CGA.
- G3,0,1 is projective geometric algebra (PGA) which is often used for (game) graphics
- there are many other ones like G2, G3, G3,3, G9 etc.

The choosen dimension has influence on
- the capabilities
- how much memory is necessary to store the geometric objects

As example, G4,1 needs 32 (2^5, ordered by Pascal triangle) values for one variable, offering the capability of orthogonal transformations by using rotors (versors) including translates (reflect, rotate, dilate, translate). Not every object needs all values, so compressing is used.

CGA uses the following blades and they are used in the array as follows. This follows how Gaalop is organized, so the code of it can be used by C++, Python etc. directly:

|-|-|-|
|grade|blade|array index|
|0|1 (scalar)|0|
|1|e1, e2, e3, einf, e0|1...5|
|2|e12, e13, e1inf, e10, e23, e2inf, e20, e3inf, e30, einf0|6...15|
|3|e123, e12inf, e120, e13inf, e130, e1inf0, e23inf, e230, e2inf0, e3inf0|16...25|
|4|e123inf, e1230, e12inf0, e13inf0, e23inf0|26...30|
|5|e123inf0 (pseudoscalar)|31|

einf means e∞, 0 means e0, e12 means e1^e2

# objects IPNS

IPNS means inner product null space.

|-|-|-|-|
|object|formula|Gaalop sample code|filled array elements|
|point|P=x + 0.5 * x² einf + e0|p=createPoint(1,2,3);|1-5, all 1-blades|
|vector|
|sphere|s=p-0.5 * r * r * einf|s=createPoint(1,2,3)-0.5 * 3 * 3 * einf;|all 1-blades|
|plane|
|circle|
|line|
|point pair|

As example, a circle uses all 2-blades, array elements 6...15, wheres as dual representation (OPNS) it uses all 3-blades, array elements 16...25.

# objects OPNS

# intersections

# angles and distances

# Denavit-Hartenberg - Screw - CGA
