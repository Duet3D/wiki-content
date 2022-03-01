---
title: Configuration Five Bar Parallel Scara Printer
description: Explanation and setup of a five bar parallel scara printer.
published: true
date: 2022-03-01T06:35:57.337Z
tags: 
editor: markdown
dateCreated: 2022-03-01T06:21:20.414Z
---

# 5 Bar Parallel Scara Printer
This printer type uses two actuators to rotate the arms and one for Z movement. Parallel kinematics are told to have good precision.

A 5 Bar SCARA has 5 bars: one between the actuators (mostly implicit by the frame), and two arms on each side left and right.

# Construction and Naming
![5barparallelscara.jpg](/5barparallelscara.jpg)

* The names of the image are used in this documentation and in the firmware.

* The red dot is the hotend position, the yellow dots represent the actuators/steppers.

* At top are optional cantilevered arms with hotend positions at the end.

* The positions of the actuators are defined by M669 X and Y parameters.

* proximalL, distalL are the left arms in mm, proximalR and distalR the right ones. theta and psi left (L) and right (R) are degrees measured from the x axis. The optional cantilevered arm of distalL is of length cantL, for distalR it is cantR.

* constr is a constraint angle: if the angle is too flat, the SCARA cannot be moved (singularity). If it is too small, the x0,y0 position becomes undefined.

* x0,y0 is the hotend point, xL,yL and xR,yR the left and right hinges.
