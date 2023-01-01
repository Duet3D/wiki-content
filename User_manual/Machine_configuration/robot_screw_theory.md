---
title: Robot Screw Theory (Product of Exponentials)
description: Details of screw theory, configuration and examples
published: true
date: 2023-01-01T09:58:48.296Z
tags: robot
editor: markdown
dateCreated: 2023-01-01T09:48:16.157Z
---

# Screw Theory
> work in progress
{.is-info}


Screw Theory, also called Product of Exponentials, PoE, is an alternative to Denavit-Hartenberg, DH. DH is the most used method, but screw has some advantages:
* setup is easier, because less frames and axis definitions are necessary
* local singularities are avoided. There are no gimbal locks, polar kinematics 0,0 singularity and similar.
* inverse kinematics can be developed in closed form, i. e. as algorithms without iterations. All possible solutions (up to 8 for a 6 axis industrial robot) are calculated, so the best can be chosen.
* probably a speedup in calculation of inverse kinematics
