---
title: (Conformal) Geometric Algebra (GA, CGA)
description: explanation and how it's used in RRF, RobotViewer
published: true
date: 2023-03-08T09:24:47.986Z
tags: robot
editor: markdown
dateCreated: 2023-03-08T08:28:19.105Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

> work in programm (just started, please come back in a few days to see more content)
{.is-info}

# Geometric Algebra

Geometric Algebra (abbreviated by GA) offers descriptions and calculations of geometric with the help of algebra. It was developed in the 19th century, but was almost forgotten. Some key people were:
- Grassmann to develop what he called exterior algebra
- Hamilton to develop quaternions to describe rotations in 3D
- Clifford who joined the two theories (and died too young)
- several persons who used GA for quantum theory
- Hestenes and Sobczyk who rediscovered GA and set the name

GA offers
- coordinate free geometric descriptions and calculations
- e.g. normals, intersections, unions
- in all dimensions and mixing them
- proving trigonometric, geometric and other theorems with ease
- applicable not only for robotics, but also e. g. quantum theory, Maxwell's equations, cristallography, image processing

GA is like an onion, starting simple with option to solve complex problems with more advanced methods:
- vector and matrix calculations
- geometric product, joining inner and outer product
- projective geometric algebra (PGA)
- conformal geometric algebra (CGA), 5-dimensional

To optimize performance, simple problem can therefore solved by simple methods.

The current list of GA software for visualization or using in own developement:
- Galoop (sources Java with plugins for C++, CUDA etc). Offers good switch beteween dimension systems (including CGA), can use Maxima and is actively developed.
- GAViewer and Gaigen2 (sources Gaigen 1.0 C++ in GAViewer, C#/Java in Gaigen2). Has a nice GUI and supports CGA.
- there is a Matlab plugin called Clifford Multivector Toolbox
- Clical
- CLUCal seems to be inactive

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
- Dorst: Geometric Algebra for Computer Science, 1st revised ed. Includes CGA.
- Hildenbrand: Foundations fo Geometric Algebra Computing. Including CGA and Gaalop description. Basic GA knowledge is preferrable.
- Hestenes, Sobczyk: Clifford Algebras And Their Applications In Mathematical Physics. Book where GA was re-established after its deep sleep.

There are a lot of free pdf articles available. Additionally, the books from Dorst, Hildenbrand, Bayro-Corrochano, Vince, Hestenes, Sobczyk and many others are available. Main topics are directed to robotics, geometry, physics and other.
