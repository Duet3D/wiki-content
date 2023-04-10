---
title: robot geometric algebra detailed
description: Semantics and syntax of geometric algebra, especially conformal one (CGA)
published: true
date: 2023-04-10T22:53:42.451Z
tags: robot
editor: markdown
dateCreated: 2023-04-07T08:20:25.411Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# Robot Geometric Algebra detailed

> work in progress, just started
{.is-info}

This page give detailed information about conformal geometric algebra (CGA). It is not necessary to use the firmware, but gives insight how the robot kinematics part of the RRF firmware is developed.

The syntax follows Gaalop syntax and the content of the Hildenbrand books.

# Geometric Algebra dimension

There are several different geometric dimension systems, declared as Gp,q[,r]:
- p are coordinate axes which square to 1
- q square to -1
- r squares to 0

For example,

- G3 uses three real axes e1, e2 and e3 with e1² = e2² = e3² = 1 unit values.
- G3,0,1 PGA is projective geometric algebra which is often used for (game) graphics
- G3,1 CRA compass ruler algebra is the CGA flavor for 2D and used in the Introduction book of Hildenbrand to explain GA
- G4,1 CGA see next section
- GAC is geometric algebra conic
- there are many other ones like G2, G3, G3,3, G9 etc.

The choosen dimension has influence on
- the capabilities
- how much memory is necessary to store the geometric objects

# CGA

Conformal geometric algebra, CGA, is placed in Euclidean 3D space, but uses for representation 5 dimensions. The 2 additional dimensions e0 and einf (e∞) are virtual ones with specific properties as explained next:

e1²=e2²=e3²=e+²=1
e-²=-1
e+ and e- are converted to e∞ (einf) and e0 to be used in CGA
e0 = 0.5(e- - e+), einf = 0.5(e- + e+)
e0 and einf are null vectors: e0²=einf²=0
The inner product e0 . einf = -1

The additional dimensions allow additional object types and affine orthogonal transformations.

G4,1 needs 32 (2^5, ordered by Pascal triangle) values for one variable, offering the capability of orthogonal transformations by using rotors (versors) including translates (reflect, rotate, dilate, translate). Not every object needs all values, so compressing is used.

Lengyel has a nice image on page 10 of https://terathon.com/gdc23_lengyel.pdf about the Pascal triangle.

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

The order of the single elements of each blade is not standardized, so differnt authors may use different orders.

The name pseudoscalar comes from the fact that it defines the volume 1 of a full dimension object.

# Dual

An object can be described by a combination of blades. It can be described by an alternative set of blades called dual. It is calculated by dual = object / I, and has the effect in CGA, that the array elements who describe it have dimension 5-n. Example: if an object uses 2-blades like a circle, the dual circle uses 3-blades. Point pairs use 3-blades, the dual 2-blades.

I is the pseudoscalar, the e1 ^ e2 ^ e3 ^ einf ^ e0 (e123inf0) blade.

Another example: a plane can be defined by a vector which defines the normal of the plane and the distance to the origin (pl = v + 5 * einf), stored in 1-blades, or as dual plane by using three points together with einf to store it in 4-blades (pl= * (p1 ^ p2 ^ p3 ^ einf)).

In Gaalop and elsewhere, the dual is marked by * in front of the object, e.g. dual = * object;

An object can be transformed into its dual by * and back again by another *

The choice which (IPNS or OPNS) is called standard form and which is the dual one is not standardizes and differs between authors.

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
|object|how calculated|Gaalop sample code|filled array elements|
|sphere|four points of the curvature|s = p1 ^ p2 ^ p3 ^ p4;|26-30|
|plane|three points on plane and einf|pl = p1 ^ p2 ^ p3 ^ einf;|26,28-30|
|circle|three points on circle|c = p1 ^ p2 ^ p3;|16-25|
|line|two points and einf|l = pl1 ^ pl2 ^ einf;|17,19,21,22,24,25|
|point pair|wedge of two points|pp = p1 ^ p2;|6-15|

In most cases the dual object is transformed into the normal form. The array elements are those of the normal form then.

A point has only an IPNS representation.

The objects with einf part are also called flat (e. g. plane) and those without einf are called round (e. g. sphere, circle).

Some authors describe additional objects like a hyperplane or different point types.

# intersections

In Euclidian 3D like CGA, intersection result in circles, lines or point pairs. An example is an intersection between two spheres: ci=s1^s2; which is a circle. Two circles intersect to a point pair pp=ci1^ci2;

# extract points from pair points

The pp pair point must be in dual mode (OPNS mode) *) . The formula is according to Dorst 14.13 and Hitzer Euclidean Geometric Objects ch 2.1. Chapter 9.2 of Hildenbrand/Fontijne/Perwass/Dorst's tutorial http://www.gaalop.de/dhilden_data/CLUScripts/eg04_tut03.pdf

*) dual mode in Hildenbrand book, normal mode in Dorst book. The pp must be stored with 2-blades array elements 6-15.

Example intersection of two spheres (which result in a circle) and a plane (which result in point pair), which is the use case to calculate PK2:

s1n=createPoint(1,2,3)-0.5 * 5 * 5 * einf;
s2n=createPoint(3,2,3)-0.5 * 5 * 5 * einf;
pln=e2+2 * einf;
s1=s1n/abs(s1n);
s2=s2n/abs(s2n);
pl=pln/abs(pln);
?pp= * (s1 ^ s2 ^ pl);
?x=-einf.pp;
?orig1=(pp-sqrt(pp * pp))/x;
?orig2=(pp+sqrt(pp * pp))/x;

It currently works with some object combinations, but not all. E. g. intersection line with plane doesn't work.

Tests decomposition:

|-|-|
|created by point-point|ok|
|sphere-sphere-plane|ok|
|line-plane|not yet|
|line-sphere|ok|
|more to come|

Author van Evert https://observablehq.com/@kelleyvanevert/conformal-3d-geometric-algebra suspect it has to do whether the objects are flat or round (i. e. whether they have an einf component).

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
