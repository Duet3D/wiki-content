---
title: robot Geometric Algebra, Conformal Geometric Algebra, GA, CGA
description: explanation and how it's used in RRF, RobotViewer
published: true
date: 2023-03-08T08:28:19.105Z
tags: robot
editor: markdown
dateCreated: 2023-03-08T08:28:19.105Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# robot Geometric Algebra

> work in programm (just started, please come back in a few days to see content)
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
- Galoop (sources Java with plugins for C++, CUDA etc)
- GAViewer and Gaigen2 (sources Gaigen 1.0 C++ in GAViewer, C#/Java in Gaigen2)
- there is a Matlab plugin called Clifford Multivector Toolbox
- Clical
- CLUCal seems to be inactive

Galoop offers good switch between dimension systems, GAViewer is visually nice. Both support CGA.


# Literature about Geometric Algebra

There are a lot of free pdf articles available. Additionally, the books from Dorst, Hildenbrand, Bayro-Corrochano, Vince, Hestenes, Sobczyk and many others are available. Main topics are directed to robotics, geometry, physics and other. Since 2000, the 5-dimensional CGA version of geometric algebra was detected for robotics and geometric tasks.
