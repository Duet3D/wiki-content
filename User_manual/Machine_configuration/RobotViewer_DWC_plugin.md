---
title: RobotViewer
description: DWC plugin to create and visualize robot configurations
published: true
date: 2022-06-02T05:27:25.375Z
tags: robot
editor: markdown
dateCreated: 2022-06-02T04:43:21.762Z
---

# Introduction
It's a bit confusing sometimes to configure a robot configuration. The main reason is that every actuator has 6 degrees of freedom (6 DOF) and the description of this position is done by describing coordinate systems of every actuator. The traditional method to describe is to use 4 parameters, using Denavit-Hartenberg DH parameters. Robot kinematics is enhanced for full 6 parameter usage and RobotViewer allows 6 DOF coordinate systems.

RobotViewer allows to start from scratch, from a template, from an existing robot.g or by a guided procedure (button desig new).

The designed configuration are G-Code lines which can directly be used in config.g or included with macro command from config.g, importing robot.g. The G10 tool configuration line may need modification or removal if using a tool changer or multiple extruders.

**work in progress, please watch for changes the next days**
