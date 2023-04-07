---
title: robot geometric algebra detailed
description: Semantics and syntax of geometric algebra, especially conformal one (CGA)
published: true
date: 2023-04-07T08:27:08.597Z
tags: robot
editor: markdown
dateCreated: 2023-04-07T08:20:25.411Z
---

# Robot Geometric Algebra detailed

> work in progress, just started
{.is-info}


I thought it is necessary to add this detailed page, because CGA (conformal geometric algebra) is not standardized yet, so documentation in books is inconsistent
- I had problems to find the necessary information
- to document the algorithms used in RRF firmware and RobotViewer
- for users who are not interested in details to not be overwhelmed on the standard pages

The syntax follows Gaalop syntax and the content of the Hildenbrand books. Dorst is similar, but differs.

# objects IPNS

IPNS means inner product null space.

|-|-|-|
|object|formula|Gaalop sample code|filled array elements|
|point|P=x + 0.5 * x² einf + e0|p=createPoint(1,2,3);|1-5, all 1-blades|
|vector|
|sphere|
|plane|
|circle|
|line|
|point pair|


