---
title: (Conformal) Geometric Algebra (GA, CGA)
description: explanation and how it's used in RRF, RobotViewer
published: true
date: 2023-05-16T10:26:07.230Z
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

There are several different geometric dimension systems, declared as Gp,q[,r] (or R... or Cl...):
- p are coordinate axes which square to 1
- q square to -1
- r squares to 0

For example,

- G3 uses three real axes e1, e2 and e3 with e1² = e2² = e3² = 1 unit values.
- G3,0,1 PGA is projective geometric algebra which is often used for (game) graphics
- G4,1 CGA see next section
- there are other ones

The choosen dimension has influence on
- the capabilities, e. g. support of additional objects
- how much memory is necessary to store the geometric objects

# Conformal Geometric Algebra, CGA

|-|-|-|
|grade|blade|array index|
|0|1 (scalar)|0|
|1|e1, e2, e3, einf, eo|1...5|
|2|e12, e13, e1inf, e1o, e23, e2inf, e2o, e3inf, e3o, einfo|6...15|
|3|e123, e12inf, e12o, e13inf, e13o, e1info, e23inf, e23o, e2info, e3info|16...25|
|4|e123inf, e123o, e12info, e13info, e23info|26...30|
|5|e123info (pseudoscalar)|31|

Conformal geometric algebra (CGA) has the following properties:
- conformal means angle preserving for transformations like reflection, rotation, translation, dilation
- used patent US 6,853,964, which expired July 2022
- uses G4,1 Minkowski space, built from G3 and G1,1
- needs 32 parameters for a multivector variable
- the blades are organized according to Pascal triangle 1-5-10-10-5-1
- the additional dimensions allow additional object types and affine orthogonal transformations.

G4,1 is organized as e1, e2, e3, e+, e- and converted to e1, e2, e3, einf and eo according to this rules:
- einf means e∞ (infinity), o means eo (origin), e12 means e1^e2
- e1, e2, e3 and e+ square to 1 each, e- to -1 (hence the name G4,1). e+ and e- are converted to a different base system to be used:
- einf = e- - e+, eo = 0.5* (e- + e+), einf.e0=-1, eo²=einf²=0
- e+=eo-0.5* einf, e-=eo+0.5* einf, e+.e+=1, e-.e-=-1
- Some authors use two times 1/sqrt(2) instead of 0.5 as factor, the formulae are different then, but the logic is the same.

# Geometric Algebra and Screw Theory

Every rigid body motion can be represented by a rotation and translation in the direction of the rotation axis, according to Chasles' theorem. This combined motion is called screw. If used for velocity, it is called twist. If used for force/torque, it is called wrench.

Conformal Geometric algebra (CGA) can describe transformations (rotation, reflection, translation, dilation) by so-called rotors with the help of versors. Screw motions can be realised by combining the two rotors rotation and translation. A description can be found in chapter 13.5.2 of the Dorst book. Rotations can be calculated by the cos/sin algorithm easier, but for interpolation (to allow e.g. a smooth rotation with move) by exponentials and logarithms, converting to rotors in geometric algebra is necessary. Quaternion based slerp is such an interpolation, but only for 3D rotations. GA based interpolation allows interpolation of rotations and at the same time translations.

# Dual

An object can be described by a combination of blades. It can be described by an alternative set of blades called dual. It is calculated by dual = object / I (i. e. A^*^ = AI^-1^), and has the effect in CGA, that the array elements who describe it have dimension 5-n. Example: if an object uses 2-blades like a circle, the dual circle uses 3-blades. Point pairs use 3-blades, the dual 2-blades. (Dorst names them other round)

I is the pseudoscalar, the e1 ^ e2 ^ e3 ^ einf ^ eo (e123info) blade. / I means multiplying with the inverse of I.

Another example: a plane can be defined by a vector which defines the normal of the plane and the distance to the origin (pl = v + 5 * einf), stored in 1-blades, or as dual plane by using three points together with einf to store it in 4-blades (pl= * (p1 ^ p2 ^ p3 ^ einf)).

In Gaalop and elsewhere, the dual is marked by * in front of the object, e.g. dual = * object;

When an object is dualized two times, the result is negative. To get the correct result, i. e. the original object, there is a special algorithm for "undualization" with the formula (see dissertations Fontijne 2.25 / Colapinto 2.20) A^-*^ = AI

In most cases, the IPNS is called the dual form and the other the standard form.

# Normalize

Every time an object has an einf element (as single einf or as part of a blade), it can be scaled. To normalize it, the einf needs to be 1.0. Gaalop calculates by onor=o/abs(o) to normalize an object. E. g. if o is a vector, abs calculates the length of the vector.

Normalizing is important for angle and distance calculations in most cases.

# objects

Storage of objects need between 3 and 10 values of the 32 values of CGA.

There are two methods to create the objects:
- IPNS: intersection of multiple objects
- OPNS: multiple points define the object

IPNS and OPNS methods store the object data in different values of CGA. Both are connected by dualization and can be converted into the other.

A classification in literature is between flat (line, plane) and round (circle, sphere) objects. If the object contains einf elements, it is flat.

# objects IPNS

IPNS means inner product null space, which means, that to check whether a point intersects with an object (i. e. whether it is part of the object), this can be tested by inner product being 0: P.X=0 (p point, X object).

A point is created only with one method.

|-|-|-|-|
|object|how calculated|Gaalop sample code|0...31 array elements|
|point|vector + 0.5 * norm² * einf + eo|p=createPoint(1,2,3);|1-5, 1-blades|
|vector|coordinates e1, e2, e3|v=e1+2 * e2+e3|1-3 part of 1-blades|
|sphere|point - 0.5 * r * r * einf|s=createPoint(1,2,3)-0.5 * 3 * 3 * einf;|1-5 1-blades|
|plane|normal vector + distance * einf|plane=1 * e1+2 * e2+3 * e3+5 * einf;|1-4 1-blades without eo|
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
|object|how calculated|Gaalop sample code|0...31 array elements|
|sphere|four points of the curvature|s = p1 ^ p2 ^ p3 ^ p4;|26-30 4-blades|
|plane|three points on plane and einf|pl = p1 ^ p2 ^ p3 ^ einf;|26,28-30 part of 4-blades|
|circle|three points on circle|c = p1 ^ p2 ^ p3;|16-25 3-blades|
|line|two planes and einf|l = pl1 ^ pl2 ^ einf;|17,19,21,22,24,25 part of the 3-blades|
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

The second method's dual is not necessarly the pseudoscalar. See Fontijne's thesis for detailed information (chapters 2.8.4, 2.13.2).

The second method formulated by Colapinto (see his dissertation chapter 2.2.5 and 2.2.6):
v = (A^*^ ^ B^*^)^-*^
where -* is the undualization: A^-*^ = AI

Circle-circle intersection is difficult to calculate, it is easier to convert one of the circles to a sphere and calculate the intersection of circle-sphere.

A very good new article about intersections and distances (see literature link below) is from Bayro-Corrochano et al.

# transformations

Conformal geometry algebra has its name conformal from the fact that transformations are angle preserving.

"Normal" rotations in linear algebra are done by matrix multiplications, as used in robotics to calculate the effect of angle changes e.g. by L=T2 * T1 .

A second method is possible, using reflections and multiple reflections by sandwitching, e.g. by L=Ro~R, which means an object is pre- and postprocessed with R and ~R. The R operator is called versor or spinor. One reflection is a reflection, but two reflections are a rotation. Versors can be combined. To avoid scaling effects, the versors should be normed.

A reflection by line or plane is possible, but in most cases for transformations bivectors (= 2-blades) are used (e. g. rotate at e1 ^ e2).

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
- Hildenbrand: Foundations fo Geometric Algebra Computing. Uses Gaalop software, together with Steinmetz maintainer of Gaalop.
- Hildenbrand: The Power of Geometric Algebra Computing. More about Gaalop and extensions of CGA into cubic, conic, quantum.

There are a lot of free pdf articles and websites available. Additionally, the articles, books and webpages from Dorst, Hildenbrand, Bayro-Corrochano, Vince, Hestenes, Sobczyk, MacDonald, Hitzer, Perwass, Ian Bell, Lasenby (2 persons) and many others are available. Main topics are directed to robotics, geometry, physics, biology and other. The newer books (after 2001) often include CGA.

Poster CGA: http://projectivegeometricalgebra.org/confgeomalg.pdf
a similar one for PGA: http://projectivegeometricalgebra.org/projgeomalg.pdf

A valuable source are thesis/dissertations, because they often have detailed explanations and they are often freely available. Some dissertations/thesis/master/articles, most use CGA:
- Fontijne: Efficient Implementation of Geometric Algebra, thesis with details about implementing and performance. Together with Dorst developer of GAViewer and GAViewer.
- Perwass: Aspects of Geometric Algebra in Euclidean, Projective and Conformal Space
- Colapinto master thesis (2011) and dissertation (2016) and articles have nice looking images and good explanations. C++ library Versor: https://github.com/wolftype/versor
- Hildenbrand/Fontijne/Perwass/Dorst: Geometric Algebra and its Application to Computer Graphics at http://www.gaalop.de/dhilden_data/CLUScripts/eg04_tut03.pdf
- Wareham dissertation: Computer Graphics using Conformal Geometric Algebra (2006)
- Rosenhahn dissertation about pose estimation
- Zaplana dissertation Solving Robot Kinematic Problems. Uses CGA for inverse kinematics.

About the topic intersection and distances,
- Bayro-Corrochano et al - Computing in the Conformal Space Objects is a remarkable article
