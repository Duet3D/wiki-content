---
title: (Conformal) Geometric Algebra (GA, CGA)
description: explanation and how it's used in RRF, RobotViewer
published: true
date: 2023-04-17T09:29:49.770Z
tags: robot
editor: markdown
dateCreated: 2023-03-08T08:28:19.105Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

Following is content about
- geometric algebra in general
- knowledge about syntax and usage of Gaalop => this is moved to a dedicated page "geometric algebra detailed"
- application of it to Paden-Kahan subproblems

Gaalop seems to be the best starting point to develop solutions, so I'll use its syntax. The Hildenbrand books give additional information.

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
- homogeneous model in 4D space
- projective geometric algebra (PGA) in 4D space
- conformal geometric algebra (CGA), 5-dimensional
- even higher spaces like 6 and 9 dimensions, supporting cubic, conic, quantum theory topics

To optimize performance, simple problem can therefore solved by simple methods and use higher dimensions if needed.


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

# Paden-Kahan PK2 solution

Strategy:
- given: axes properties om1 (axis direction), om2, v1 (point on axis 1), v2, p (first point), k (second point)
- searched: angles between p and intersection point(s), angle between k and intersection point(s)
- get planes and sphere middle points from axis definitions and points
- calculate intersection line of planes. Special case parallel axes/planes, where the intersection solution is a plane, not a line
- calculate pointpair by intersection of this line with sphere
- double check by calculating other pointpair with intersection of line with other sphere
- the two pointpairs must match
- atan2(wedge,inner) of points with pointpair solutions
- store the 2 * 2 results (1 * 2 or 0 solutions are also possible)

This should already support parallel and skewing axes as extension to traditional PK2, which used only intersecting axes.

# Literature about Geometric Algebra

Recommended books from a personal view are:
- Dorst/Fontijne/Mann: Geometric Algebra for Computer Science, 1st revised ed. Includes CGA. Including and using GAViewer and Gaigen2.
- Hildenbrand: Foundations fo Geometric Algebra Computing. Including CGA and using Gaalop. Basic GA knowledge is preferrable.
- Hildenbrand: The Power of Geometric Algebra Computing. More from Gaalop and extensions of CGA into cubic, conic, quantum.

There are a lot of free pdf articles and websites available. Additionally, the books from Dorst, Hildenbrand, Bayro-Corrochano, Vince, Hestenes, Sobczyk, MacDonald, Hitzer, Perwass and many others are available. Main topics are directed to robotics, geometry, physics and other. The newer books often include CGA. A valuable source are thesis/dissertations, because they often have detailed explanations and they are freely available.

Some articles/thesis:
- Perwass: Aspects of Geometric Algebra in Euclidean, Projective and Conformal Space
- Colapinto dissertation and articles has nice looking images (and good information).
- Hildenbrand/Fontijne/Perwass/Dorst: Geometric Algebra and its Application to Computer Graphics at http://www.gaalop.de/dhilden_data/CLUScripts/eg04_tut03.pdf
