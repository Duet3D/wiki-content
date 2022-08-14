---
title: Robot types and their specifics
description: Supported robot types with description of properties and how to configure them, prototype recommendations
published: true
date: 2022-08-14T06:59:05.328Z
tags: robot
editor: markdown
dateCreated: 2022-08-14T06:59:05.328Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

> This wiki is new and under construction
{.is-info}


# Introduction
Robot kinematics supports different kinds of robots. Following is a description of some of them, categorized by number of joints/axes and whether they are serial/open chain or parallel/closed chain.

# 6 axis industrial robot
Configuration is made by defining the Denavit-Hartenberg parameters, see the wiki about DH.

Especially important is the identification of singularity areas, which must be avoided.

Care must be taken when the robot is powered off: the arms need protection against falling down with damaging result. The following possibilities avoid it
* using brakes at the joints
* weight balance of every joint, e.g. by using counterweights
* protection by using springs or dampers

# 5 axis CNC

5 axis CNC have subtypes head/head, head/table, table/table and AC, AB, BC.

# 4 axis palletized robot

The endpoint's horizontal orientation is ensured by mechanical parallelogram (often two connected parallelograms) based parallel arms, which are differently implemented.
