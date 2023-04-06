---
title: (Conformal) Geometric Algebra (GA, CGA)
description: explanation and how it's used in RRF, RobotViewer
published: true
date: 2023-04-06T17:59:19.533Z
tags: robot
editor: markdown
dateCreated: 2023-03-08T08:28:19.105Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

> work in programm (just started, please come back in a few days to see more content)
{.is-info}

# Geometric Algebra

Geometric Algebra (abbreviated by GA, other names: Clifford algebra, exterior algebra) offers descriptions and calculations of geometric with the help of algebra. It was developed in the 19th century, but was almost forgotten. Some key people were:
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

GA is like an onion, starting simple with option to solve complex problems with more advanced methods:
- vector model for rotations in 3D space
- homogeneous model in 4D space for translations
- projective geometric algebra (PGA) in 4D space
- conformal geometric algebra (CGA), 5-dimensional
- even higher spaces like 6 and 9 dimensions

To optimize performance, simple problem can therefore solved by simple methods and use higher dimensions if needed.

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

# Geometric Algebra and Screw Theory

Every rigid body motion can be represented by a rotation and translation in the direction of the rotation axis, according to Chasles' theorem. This combined motion is called screw. If used for velocity, it is called twist. If used for force/torque, it is called wrench.

Conformal Geometric algebra (CGA) can describe transformations (rotation, reflection, translation, dilation) by so-called rotors with the help of versors. Screw motions can be realised by combining the two rotors rotation and translation. A description can be found in chapter 13.5.2 of the Dorst book.

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

# Conformal Geometric Algebra (CGA)

Conformal means angle preserving of the transformations (reflection, rotation, translation etc). CGA uses additional information, compared to 3-dimensional storage. CGA is a 5-dimensional Minkowski G4.1 space, needing 32 parameters to describe all blades. CGA was patented in US 6,853,964, but expired in July 2022.

The blades according to Pascal triangle, 2^5 = 32 blades, 1-5-10-10-5-1:
- 1 scalar value
- 5 1-blades, the e1, e2, e3, e0, einf axes
- 10 2-blades, the combinations e1e2 etc.
- 10 3-blades of e1e2e3 etc
- 5 4-blades, e. g. e1e2e3einf
- 1 pseudoscalar

Most can be ignored later for most object calculations, because for specific objects, most values are 0.

CGA is classified as G4,1, which means 4 coordinates square to 1 and one to -1.

# Geometric Algebra in Firmware

An old development saying is, to develop correctly with respect to design first, then analyze for bottlenecks and optimize later. In other words, don't optimize too early to avoid bad designs. In this spirit, I'm implementing all
* in Conformal Geometric Algebra, 5 dimensional
* check performance and memory usage
* strip down and simplify if this usage is too high for RRF

This approach will allow using the full functionality on a PC or in a future Duet with lot of memory.

# Literature about Geometric Algebra

Recommended books from a personal view are:
- Dorst/Fontijne/Mann: Geometric Algebra for Computer Science, 1st revised ed. Includes CGA. Including and using GAViewer and Gaigen2.
- Hildenbrand: Foundations fo Geometric Algebra Computing. Including CGA and using Gaalop. Basic GA knowledge is preferrable.
- Hildenbrand: The Power of Geometric Algebra Computing. More from Gaalop and extensions of CGA into cubic, conic, quantum.

There are a lot of free pdf articles and websites available. Additionally, the books from Dorst, Hildenbrand, Bayro-Corrochano, Vince, Hestenes, Sobczyk, MacDonald, Hitzer and many others are available. Main topics are directed to robotics, geometry, physics and other. The newer books often include CGA.
