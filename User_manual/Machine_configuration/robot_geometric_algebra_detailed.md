---
title: robot geometric algebra detailed
description: Semantics and syntax of geometric algebra, especially conformal one (CGA)
published: true
date: 2023-04-10T08:13:20.587Z
tags: robot
editor: markdown
dateCreated: 2023-04-07T08:20:25.411Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# Robot Geometric Algebra detailed

> work in progress, just started
{.is-info}

This page give detailed information about conformal geometric algebra (CGA). It is not necessary to use the firmware, but gives insight how the robot kinematics part of the RRF firmware is developed.

The syntax follows Gaalop syntax and the content of the Hildenbrand books. I recommend buying the Hildenbrand and Dorst books, because I can give a summary below, but details and extented samples are in the books. Mathematical books are very expensive unfortunately, I would buy the Dorst or Hildenbrand Foundations book if I am forced to buy just one. Dorst is very good, but syntax differs between Dorst/GAViewer/Gaigen and Hildenbrand/Gaalop.

# Geometric Algebra dimension

There are several different geometric dimension systems, declared as Gp,q[,r]:
- p are coordinate axes which square to 1
- q square to -1
- r squares to 0

For example,

- G3 uses three real axes e1, e2 and e3 with e1² = e2² = e3² = 1 unit values.
- CGA G4,1 uses e1²=e2²=e3²=e+²=1 and e-²=-1. e+ and e- are converted to e∞ (einf) and e0 to be used in CGA: e0 = 0.5(e- - e+), einf = 0.5(e- + e+). e0 and einf are null vectors: e0²=einf²=0. Inner product e0 . einf = -1.
- G3,0,1 is projective geometric algebra (PGA) which is often used for (game) graphics
- there are many other ones like G2, G3, G3,3, G9 etc.

The choosen dimension has influence on
- the capabilities
- how much memory is necessary to store the geometric objects

As example, G4,1 needs 32 (2^5, ordered by Pascal triangle) values for one variable, offering the capability of orthogonal transformations by using rotors (versors) including translates (reflect, rotate, dilate, translate). Not every object needs all values, so compressing is used. Lengyel has a nice image on page 10 of https://terathon.com/gdc23_lengyel.pdf

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

IPNS means inner product null space, which means, that to check whether a point intersects with an object, this can be tested by inner product being 0: P.X=0 (p point, X object).

|-|-|-|-|
|object|how calculated|Gaalop sample code|filled array elements|
|point|vector + 0.5 * norm² + e0|p=createPoint(1,2,3);|1-5, all 1-blades|
|vector|coordinates e1, e2, e3|v=e1+2 * e2+e3|1-3|
|sphere|point - 0.5 * r * r * einf|s=createPoint(1,2,3)-0.5 * 3 * 3 * einf;|all 1-blades|
|plane|normal vector + distance * einf|plane=1 * e1+2 * e2+3 * e3+5 * einf;|0-4|
|circle|intersection two spheres|z = s1 ^ s2|6-15|
|line|intersection two planes|l = pl1 ^ pl2|6-8, 10, 11, 13|
|point pair|intersection three spheres|pp = s1 ^ s2 ^ s3|16-25|

A point is a sphere with 0 radius, so the properties are similar.

As example, a circle uses all 2-blades, array elements 6...15, wheres as dual representation (OPNS) it uses all 3-blades, array elements 16...25.

# objects OPNS

OPNS are the dual representations of IPNS, named by outer product null space. A test whether a point is part of the object can be done by P^X=0 (p the point, X the object).

|-|-|-|-|
|object|formula|Gaalop sample code|filled array elements|
|vector|
|sphere|
|plane|
|circle|
|line|
|point pair|

A point has only an IPNS representation.

# intersections

In Euclidian 3D like CGA, intersection result in circles, lines or point pairs. An example is an intersection between two spheres: ci=s1^s2; which is a circle. Two circles intersect to a point pair pp=ci1^ci2;

# angles and distances

# transformations

Conformal geometry algebra has its name conformal from the fact that transformations are angle preserving.

"Normal" rotations in linear algebra are done by matrix multiplications, as used in robotics to calculate the effect of angle changes e.g. by L=T2 * T1 .

A second method is possible, using reflections and multiple reflections by sandwitching, e.g. by L=Ro~R, which means an object is pre- and postprocessed with R and ~R. The R operator is called versor or spinor. One reflection is a reflection, but two reflections are a rotation. Versors can be combined. To avoid scaling effects, the versors should be normed.

Overview of sandwitching transformations:

|-|-|-|
|reflection|one reflection|
|rotation|two reflections|
|translation|reflections at two parallel planes||
|motor|combined translation and rotation||
|inversion|

The rotors allow to implement interpolated motions easily. The quaternion slerp method is related to this algorithm.

# Denavit-Hartenberg - Screw - CGA
