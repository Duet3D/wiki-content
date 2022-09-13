---
title: Robot industrial 6 axis
description: 
published: true
date: 2022-09-13T11:49:58.256Z
tags: robot
editor: markdown
dateCreated: 2022-09-13T11:49:01.371Z
---

# Industrial 6 axis robot
following will be a description of the specifics



Configuration is made by defining the Denavit-Hartenberg parameters, see the wiki about DH.

Especially important is the identification of singularity areas, which must be avoided.

Care must be taken when the robot is powered off: the arms need protection against falling down with damaging result. The following possibilities avoid it
* using brakes at the joints
* weight balance of every joint, e.g. by using counterweights, so the force downward is below the detent torque and friction of the steppers/gears (servos don't have much detent torque and cannot be used for this method)
* protection by using springs or dampers

