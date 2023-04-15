---
title: robot geometric algebra detailed
description: Semantics and syntax of geometric algebra, especially conformal one (CGA)
published: true
date: 2023-04-15T07:56:12.509Z
tags: robot
editor: markdown
dateCreated: 2023-04-07T08:20:25.411Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# Robot Geometric Algebra detailed

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
- G5,3 GAC is an algebra for conics (H10)
- G8,2 (?) Double CGA adds objects like quadrics, tori and cylinders (H11)
- G9,7 (?) Cubic CGA adds cubics (H12)
- there are many other ones like G2, G3, G3,3, G9 etc.

H10 = Hildenbrand The Power of ... chapter 10.

The choosen dimension has influence on
- the capabilities
- how much memory is necessary to store the geometric objects

# CGA

Conformal geometric algebra, CGA, is placed in Euclidean 3D space, but uses for representation 5 dimensions.

The e1, e2, e3 are the x, y, z coordinates. The 2 additional dimensions e0 and einf (e∞) are virtual ones to allow the extended capabilities of CGA. They are designed in a way, so the 3-dimensional euclidean information is included in CGA and can be extracted unchanged.

The coordinate properties are:

- e1²=e2²=e3²=e+²=1
- e-²=-1
- the bases e∞ (einf) and e0 are used instead of e+ and e-:
- e0 = 0.5(e- - e+), einf = e- + e+
- e0²=einf²=0 (null vectors)
- e0 . einf = -1 (inner product)

e+ and e- are the two additional coordinates (where the name G4,1 comes from), but e0 and einf are used to define the objects.

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

The name pseudoscalar comes from the fact that it defines the volume 1 of a full dimension object and is only one scalar value.

# Dual

An object can be described by a combination of blades. It can be described by an alternative set of blades called dual. It is calculated by dual = object / I, and has the effect in CGA, that the array elements who describe it have dimension 5-n. Example: if an object uses 2-blades like a circle, the dual circle uses 3-blades. Point pairs use 3-blades, the dual 2-blades. (Dorst names them other round)

I is the pseudoscalar, the e1 ^ e2 ^ e3 ^ einf ^ e0 (e123inf0) blade. / I means multiplying with the inverse of I.

Another example: a plane can be defined by a vector which defines the normal of the plane and the distance to the origin (pl = v + 5 * einf), stored in 1-blades, or as dual plane by using three points together with einf to store it in 4-blades (pl= * (p1 ^ p2 ^ p3 ^ einf)).

In Gaalop and elsewhere, the dual is marked by * in front of the object, e.g. dual = * object;

An object can be transformed into its dual by * and back again by another *

The choice which (IPNS or OPNS) is called standard form and which is the dual one is not standardizes and differs between authors.

# objects IPNS

IPNS means inner product null space, which means, that to check whether a point intersects with an object, this can be tested by inner product being 0: P.X=0 (p point, X object).

The first version of the point is the same for IPNS and OPNS.

|-|-|-|-|
|object|how calculated|Gaalop sample code|filled array elements|
|point|vector + 0.5 * norm² + e0|p=createPoint(1,2,3);|1-5, 1-blades|
|vector|coordinates e1, e2, e3|v=e1+2 * e2+e3|1-3 part of 1-blades|
|sphere|point - 0.5 * r * r * einf|s=createPoint(1,2,3)-0.5 * 3 * 3 * einf;|1-5 1-blades|
|plane|normal vector + distance * einf|plane=1 * e1+2 * e2+3 * e3+5 * einf;|0-4 scalar and 1-blades without e0|
|circle|intersection two spheres|z = s1 ^ s2|6-15 2-blades|
|line|intersection two planes|l = pl1 ^ pl2|6-8, 10, 11, 13 part of 2-blades|
|point pair|intersection three spheres|pp = s1 ^ s2 ^ s3|16-25 3-blades|
|(point)|intersection four spheres|p = s1 ^ s2 ^ s3 ^ s4||

In IPNS mode, the role of ^ is intersection.

A point is a sphere with 0 radius, so the properties are similar.

As example, a circle uses all 2-blades, array elements 6...15, wheres as dual representation (OPNS) it uses all 3-blades, array elements 16...25.

# objects OPNS

OPNS are the dual representations of IPNS, named by outer product null space. A test whether a point is part of the object can be done by P^X=0 (p the point, X the object).

|-|-|-|-|
|object|how calculated|Gaalop sample code|filled array elements|
|sphere|four points of the curvature|s = p1 ^ p2 ^ p3 ^ p4;|26-30 4-blades|
|plane|three points on plane and einf|pl = p1 ^ p2 ^ p3 ^ einf;|26,28-30 4-blades|
|circle|three points on circle|c = p1 ^ p2 ^ p3;|16-25 3-blades|
|line|two points and einf|l = pl1 ^ pl2 ^ einf;|17,19,21,22,24,25 part of the 3-blades|
|point pair|wedge of two points|pp = p1 ^ p2;|6-15 2-blades|
|(hyperplane)|four points and einf|pl = p1 ^ p2 ^ p3 ^ p4 ^ einf;||

In OPNS mode, the role of ^ is to combine the elements to build the object.

In most cases the dual object is transformed into the normal form. The array elements are those of the normal form then.

A point has only an IPNS representation.

The objects with einf part are also called flat (e. g. plane) and those without einf are called round (e. g. sphere, circle).

Some authors describe additional objects like a hyperplane or different point types.

# intersections

Calculation of intersections is one of CGA's strengths, because syntax is very simple.

∩ means meet.

There are two methods:
- A ∩ B = dual(B) . A
- dual(A ∩ B) = dual(B) ^ dual(A)

The dual object is meant to be the IPNS here. (Example: if a plane is created by using normal and distance, it is in dual mode already).

The first method (using the inner product) has preconditions (removing some parts from A), while the second (using wedge product) is without.

In my tests I currently have problems to find the intersection between line and plane and circle-circle, so I decided to test all possible combinations of objects. This will take a while, I'll document the results here.

Example of intersection sphere-sphere-plane, which results in point pair. Followed by an extraction of the point pair into single points:

s1n=createPoint(1,2,3)-0.5 * 5 * 5 * einf;
s2n=createPoint(3,2,3)-0.5 * 5 * 5 * einf;
pln=e2+2 * einf;
s1=s1n/abs(s1n);
s2=s2n/abs(s2n);
pl=pln/abs(pln);
?pp= * (s1 ^ s2 ^ pl);
?x=-einf.pp;
?p1=(pp-sqrt(pp * pp))/x;
?p2=(pp+sqrt(pp * pp))/x;

For this formula, pp must be in 2-blade mode, i. e. OPNS. Gaalop offers methods to extract the points:
p1=ExtractFirstPoint(pp);
p2=ExtractSecondPoint(pp);


# transformations

Conformal geometry algebra has its name conformal from the fact that transformations are angle preserving.

"Normal" rotations in linear algebra are done by matrix multiplications, as used in robotics to calculate the effect of angle changes e.g. by L=T2 * T1 .

A second method is possible, using reflections and multiple reflections by sandwitching, e.g. by L=Ro~R, which means an object is pre- and postprocessed with R and ~R. The R operator is called versor or spinor. One reflection is a reflection, but two reflections are a rotation. Versors can be combined. To avoid scaling effects, the versors should be normed.

A reflection by line or plane is possible, but in most cases for transformations bivectors (= 2-blades) are used (e. g. rotate at e1 ^ e2).

Overview of sandwitching transformations:

|-|-|-|
|reflection|one reflection|
|rotation|two reflections|
|translation|reflections at two parallel planes||
|motor|combined translation and rotation||
|inversion|

The rotors allow to implement interpolated motions easily. The quaternion slerp method is related to this algorithm.

This chapter will also describe how GA transformations can model screw motions (i. e. Chasles' theorem).

# angles and distances

The inner product is used for angle and distance calculations. It is similar to the matrix dot product, but valid for all dimensions.

# Denavit-Hartenberg - Screw - CGA

This section will probably be moved to configuration page when defined, and will describe how they are related and converted into each other (primarily to allow defintion by DH and then use the data in screw configuration).
