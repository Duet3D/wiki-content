---
title: (Conformal) Geometric Algebra (GA, CGA)
description: explanation and how it's used in RRF, RobotViewer
published: true
date: 2023-03-26T10:27:23.518Z
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

# Geometric Algebra Software

Learning GA needs good books or articles, but software for visualization and to learn the algorithms is also very valuable. Following an imcomplete list of software:

|-|-|-|-|
|name|developed, documented by|language, capabilities|
|Gaalop|Hildenbrand, Steinmetz|Java, several dimension systems, symbolic optimizer Maxima integrated|
|Maxima|GPL developers|symbolic algebra with optimizer|
|GAViewer|Dorst|C++ with Gaigen 1, Viewer|
|Gaigen 2|Dorst|C#, Java, library for different dimension systems|
|Clifford Multivector Toolbox| |Matlab plugin|
|Clical| | |
|CLUCal| |seems to be inactive. Gaalop is based on it|

Currently my favorite is Gaalop. The syntax of GAViewer and Gaalop is similar. Program handling and documentation of both is terrible, but imho it's important to follow how they calculate. Some tipps for Gaalop:
- start.bat doesn't work for me, I have to start by java -jar starter...jar directly. Jdk 14 and 19 both work.
- the visualizer runs only at the first call, then there are exceptions (so to visualize, I restart every time)
- often nullpointerexceptions, variables not usable and alike
- the C++ output is good


# Conformal Geometric Algebra (CGA)

Conformal means angle preserving of the transformations (reflection, rotation, translation etc). CGA uses additional information, compared to 3-dimensional storage. CGA is a 5-dimensional Minkowski G4.1 space, needing 32 parameters to describe all blades. CGA was patented in US 6,853,964, but fortunately expired in July 2022.

The blades according to Pascal triangle, 2^5 blades, 1-5-10-10-5-1, the first being scalar, then the single vectors, then bivectors, trivectors, 4-vectors, then all axes called pseudoscalar. Most can be ignored later for most object calculations.

# Geometric Algebra in Firmware

An old development saying is, to develop correctly with respect to design first, then analyze for bottlenecks and optimize later. In other words, don't optimize too early to avoid bad designs. In this spirit, I'm implementing all
* in Conformal Geometric Algebra, 5 dimensional
* check performance and memory usage
* strip down and simplify if this usage is too high for RRF

This approach will allow using the full functionality on a PC or in a future Duet with lot of memory.

# Literature about Geometric Algebra

Recommended introductory books from a personal view are:
- Dorst: Geometric Algebra for Computer Science, 1st revised ed. Includes CGA. Including and using GAViewer and Gaigen2.
- Hildenbrand: Foundations fo Geometric Algebra Computing. Including CGA and using Gaalop. Basic GA knowledge is preferrable.

There are a lot of free pdf articles and websites available. Additionally, the books from Dorst, Hildenbrand, Bayro-Corrochano, Vince, Hestenes, Sobczyk, MacDonald and many others are available. Main topics are directed to robotics, geometry, physics and other. The newer books often include CGA.
An example of a nice website is http://geocalc.clas.asu.edu/GA_Primer/GA_Primer/introduction-to-geometric/index.html by Hestenes.

For a beginner of GA, I recommend the Dorst book, use GAViewer and Gaalop and to accompany Dorst by looking up topics in the web which are unclear after reading it. 

Articles specifically about conformal geometric algebra:
- dissertation Gebken: https://www.informatik.uni-kiel.de/inf/Sommer/doc/extern/tr1001.pdf
