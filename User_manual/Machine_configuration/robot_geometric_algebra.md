---
title: (Conformal) Geometric Algebra (GA, CGA)
description: explanation and how it's used in RRF, RobotViewer
published: true
date: 2023-04-29T22:02:49.126Z
tags: robot
editor: markdown
dateCreated: 2023-03-08T08:28:19.105Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# Geometric Algebra

Geometric Algebra (abbreviated GA, similar to: Clifford algebra, exterior algebra) offers descriptions and calculations of geometric with the help of algebra. It was developed in the 19th century, but was almost forgotten. Some key people were:
- Grassmann to develop what he called exterior algebra
- Hamilton to develop quaternions to describe rotations in 3D
- Clifford who joined the two theories (and died too young)
- Gibbs needed only 3-dimensional calculations and used cross products. This distracted from a dimensional-independent geometric algebra.
- Pauli, Dirac and others for quantum and spin theory
- Hestenes and Sobczyk who rediscovered GA and set the name

GA offers
- coordinate free geometric descriptions and calculations
- e. g. for calculation of intersections and unions
- in all dimensions and mixing them
- explain trigonometric, geometric and other theorems geometrically and easier to understand than the "traditional" approach
- applicable not only for robotics, but also e. g. quantum/spin theory, Maxwell's equations, mechanics incl. Newton's laws, cristallography, image processing

Several geometries are included in GA:
- vector model for rotations in 3D space
- homogeneous model in 4D space
- projective geometric algebra (PGA) in 4D space
- conformal geometric algebra (CGA), 5-dimensional
- GA can also use more than 5 dimensions for more objects like conics, cubic
- quaternions and complex numbers are part of it

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
- G5,3 GAC is an algebra for conics
- G8,2 Double CGA adds objects like quadrics, tori and cylinders (see Easter, Hitzer)
- G9,7 (?) Cubic CGA adds cubics
- there are many other ones like G2, G3, G3,3, G9 etc.

The choosen dimension has influence on
- the capabilities
- how much memory is necessary to store the geometric objects

# Conformal Geometric Algebra, CGA

Conformal means angle preserving of the transformations (reflection, rotation, translation etc). CGA uses additional information, compared to 3-dimensional storage. CGA is a 5-dimensional Minkowski G4.1 space, needing 32 parameters to describe all blades. CGA was patented in US 6,853,964, but expired in July 2022.

The additional dimensions allow additional object types and affine orthogonal transformations.

G4,1 needs 32 (2^5, ordered by Pascal triangle) values for one variable, offering the capability of orthogonal transformations by using rotors (versors) including translates (reflect, rotate, dilate, translate). Not every object needs all values, so compressing is used. The following table follows how Gaalop is organized:

|-|-|-|
|grade|blade|array index|
|0|1 (scalar)|0|
|1|e1, e2, e3, einf, e0|1...5|
|2|e12, e13, e1inf, e10, e23, e2inf, e20, e3inf, e30, einf0|6...15|
|3|e123, e12inf, e120, e13inf, e130, e1inf0, e23inf, e230, e2inf0, e3inf0|16...25|
|4|e123inf, e1230, e12inf0, e13inf0, e23inf0|26...30|
|5|e123inf0 (pseudoscalar)|31|

einf means e∞, 0 means e0, e12 means e1^e2

# Geometric Algebra and Screw Theory

Every rigid body motion can be represented by a rotation and translation in the direction of the rotation axis, according to Chasles' theorem. This combined motion is called screw. If used for velocity, it is called twist. If used for force/torque, it is called wrench.

Conformal Geometric algebra (CGA) can describe transformations (rotation, reflection, translation, dilation) by so-called rotors with the help of versors. Screw motions can be realised by combining the two rotors rotation and translation. A description can be found in chapter 13.5.2 of the Dorst book.


# Dual

An object can be described by a combination of blades. It can be described by an alternative set of blades called dual. It is calculated by dual = object / I (i. e. x* = x . I^-1^), and has the effect in CGA, that the array elements who describe it have dimension 5-n. Example: if an object uses 2-blades like a circle, the dual circle uses 3-blades. Point pairs use 3-blades, the dual 2-blades. (Dorst names them other round)

I is the pseudoscalar, the e1 ^ e2 ^ e3 ^ einf ^ e0 (e123inf0) blade. / I means multiplying with the inverse of I.

Another example: a plane can be defined by a vector which defines the normal of the plane and the distance to the origin (pl = v + 5 * einf), stored in 1-blades, or as dual plane by using three points together with einf to store it in 4-blades (pl= * (p1 ^ p2 ^ p3 ^ einf)).

In Gaalop and elsewhere, the dual is marked by * in front of the object, e.g. dual = * object;

An object can be transformed into its dual by * and back again by another * => that's wrong, it is rotated 180 degree when dualization two times. To revert, there is a special algorithm for "undualization" x^-*^ and the formula is (see dissertation Fontijne 2.25) A^-x^ = A . I

The choice which (IPNS or OPNS) is called standard form and which is the dual one is not standardizes and differs between authors.

# objects

The following objects can be stored in CGA. Storage needs between 3 and 10 values of the 32 values of CGA, so implementation has the task to optimize the sparse value usage (i. e. ignore the many 0 values).

Classification by grade:

|-|-|-|
|grade|flat|round|
|1 vector|point, vector|
|2 bivector|point pair|
|3 trivector|line|circle|
|4-vector|plane|sphere|
|5-vector|hyperplane|I, pseudoscalar|

A round object becomes flat by using one point from einf (a circle becomes a line, so a line is a circle with one point in infinity).

There are two methods to create the objects:
- IPNS: intersection of multiple objects
- OPNS: multiple points define the object

They are linked through dualization.

# objects IPNS

IPNS means inner product null space, which means, that to check whether a point intersects with an object (i. e. whether it is part of the object), this can be tested by inner product being 0: P.X=0 (p point, X object).

The first version of the point is the same for IPNS and OPNS.

|-|-|-|-|
|object|how calculated|Gaalop sample code|filled array elements|
|point|vector + 0.5 * norm² + e0|p=createPoint(1,2,3);|1-5, 1-blades|
|vector|coordinates e1, e2, e3|v=e1+2 * e2+e3|1-3 part of 1-blades|
|sphere|point - 0.5 * r * r * einf|s=createPoint(1,2,3)-0.5 * 3 * 3 * einf;|1-5 1-blades|
|plane|normal vector + distance * einf|plane=1 * e1+2 * e2+3 * e3+5 * einf;|1-4 1-blades without e0|
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
|plane|three points on plane and einf|pl = p1 ^ p2 ^ p3 ^ einf;|26,28-30 part of 4-blades|
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

Calculation of intersections is one of CGA's strengths, because syntax is often very simple, especially if the objects are of the same type like two spheres.

∩ means meet.

There are two methods:
- A ∩ B = dual(B) . A
- dual(A ∩ B) = dual(B) ^ dual(A)

The second method's dual is not necessarly the pseudoscalar. See Fortijne's thesis for detailed information.

# transformations

Conformal geometry algebra has its name conformal from the fact that transformations are angle preserving.

"Normal" rotations in linear algebra are done by matrix multiplications, as used in robotics to calculate the effect of angle changes e.g. by L=T2 * T1 .

A second method is possible, using reflections and multiple reflections by sandwitching, e.g. by L=Ro~R, which means an object is pre- and postprocessed with R and ~R. The R operator is called versor or spinor. One reflection is a reflection, but two reflections are a rotation. Versors can be combined. To avoid scaling effects, the versors should be normed.

A reflection by line or plane is possible, but in most cases for transformations bivectors (= 2-blades) are used (e. g. rotate at e1 ^ e2).

Overview of sandwitching transformations:

|-|-|-|
|reflection at line|one reflection|formula|
|reflection at plane|one reflection|formula|
|rotation|two reflections|formula|
|translation|reflections at two parallel planes|formula|
|motor|combined translation and rotation|formula|
|inversion|at sphere|formula|

The rotors allow to implement interpolated motions easily. The quaternion slerp method is related to this algorithm.

This chapter will also describe how GA transformations can model screw motions (i. e. Chasles' theorem).

# transformations and quaternions

Quaternions are rotors in 3D space.

A quaternion q = qw + qi * i + qj * j + qk * k;
qw² + qi² + qj² + qk² = 1
i * j * k = -1
etc.

Then the relation to GA is that the quaternions are the following blades and basis of the rotor bivectors:
i = e3e2
j = e1e3
k = e2e1

There are other relations from other authors, where the sign of the result is different.

# angles

> The following is from the memory and is to be verified
{.is-warning}

A vector can be created by subtracting points:
p1=createPoint(1,0,0);
p2=createPoint(2,0,0);
?v1=p2-p1;
The vector has xyz euclidean information and an einf component which contains information about weight and orientation in relation to the coordinate system.

The angle between two vectors a and b is:

Inner product i:
a . b = |a| |b| * cos(phi)
if the vectors are normalized: a . b = cos(phi)

Wedge (outer) product w:
(a ^ b)(a ^ b) = |a||b| sin(phi)
if the vectors are normalized: (a ^ b)(a ^ b) = sin(phi)

For the angle to be correct in 360 degree range and with correct orientation:
For normalized vectors:
atan2(sin,cos) = atan2(w,i) = atan2((a ^ b)(a ^ b), a . b)

Gaalop has no atan2 function, so atan(w/i) can be calculated instead and a 180 degree correction must be made for the cases sin... and cos... (tbd)

# distances

The inner product is the main tool to calculate distances. Depending on the object types, the meaning is different. For example, an inner product between a line and a plane measures the angle between them, while the inner product of other object types will measure different types of distances.

# Geometric Algebra Software

To verify code, Gaalop by Hildenbrand/Steinmetz is used. GAViewer by Dorst is also nice and fast, but uses a different syntax. Clifford Multivector Toolbox for Matlab is also available.

An overview of software is in Breuils et al - New Applications ... chapter 10.

Gaalop
- is actively developed
- creates code for C++, Python, CUDA and others
- creates visualization code, but could be nicer, like GAViewer
- offers a web version and two universities who offer web based online usage
- can be extended by Maxima to optimize the symbolic code of GA
- http://www.gaalop.de/ is open source and LGPL 3, Java based

Clifford Multivector Toolbox for Matlab (not tested yet)
- GPL license
- developed by Sangwine/Hitzer
- https://clifford-multivector-toolbox.sourceforge.io/

# Literature about Geometric Algebra

Recommended books from a personal view are:
- Dorst/Fontijne/Mann: Geometric Algebra for Computer Science, 1st revised ed. Uses GAViewer and Gaigen 2 software
- Hildenbrand: Foundations fo Geometric Algebra Computing. Uses Gaalop software
- Hildenbrand: The Power of Geometric Algebra Computing. More from Gaalop and extensions of CGA into cubic, conic, quantum.

There are a lot of free pdf articles and websites available. Additionally, the books from Dorst, Hildenbrand, Bayro-Corrochano, Vince, Hestenes, Sobczyk, MacDonald, Hitzer, Perwass and many others are available. Main topics are directed to robotics, geometry, physics and other. The newer books (after 2001) often include CGA.

A valuable source are thesis/dissertations, because they often have detailed explanations and they are often freely available. Some dissertations/thesis/master/articles:
- Fontijne: Efficient Implementation of Geometric Algebra, thesis with details about implementing and performance
- Perwass: Aspects of Geometric Algebra in Euclidean, Projective and Conformal Space
- Colapinto master thesis (2011) and dissertation (2016) and articles have nice looking images and good explanations. YT video https://www.youtube.com/watch?v=W4p-e-g37tg and C++ library Versor: https://github.com/wolftype/versor
- Hildenbrand/Fontijne/Perwass/Dorst: Geometric Algebra and its Application to Computer Graphics at http://www.gaalop.de/dhilden_data/CLUScripts/eg04_tut03.pdf
- Wareham dissertation: Computer Graphics using Conformal Geometric Algebra (2006)
