---
title: Third-order motion profile support
description: This page describes the experimental support for third-order motion profiles, sometimes referred to as S-curve acceleration. This support is not currently available in standard builds of RepRapFirmware.
published: true
date: 2025-03-28T09:58:34.106Z
tags: 
editor: markdown
dateCreated: 2025-03-28T09:22:43.388Z
---

# What is third-order motion control?
## First order motion control
In a first order motion system, movement speed changes abruptly between moves. There is no continuous acceleration. In theory this required infinte force to be applied to change the speed, since the acceleration is also infinite. In practice the change in speed occurs over a finite amount of time, so the movement is not quite as commanded. If the motion system is driven by stepper motors and the finite acceleraiton causes the actual position differ from the commanded position by more than one full step then the motor will skip steps.

First order motion control is practical when the speeds are low and it is not critical that the actual position closely follows the commanded position.

## Second order motion control
In a second order motion system, changes in speed are performed by accelerating between the two speeds at a constant rate. If using open-loop stepper motors, the actual position will lag the commanded position slightly during acceleration because the amount of force developed by a stepper motors depends on the amoubnt of lag. Similary, when decelerating the actual osition will lead the commanded position slightly. If the stepper motor(s) can supply sufficient force to achoeve the required acceleration with less than one full step of lag then no steps will be lost.

Second order motion control is practical in many applications. 3D printer firmwares (including RepRapFirmware) generally use second order motion control, except that if direction changes are to be made without coming to a complete stop then some degree of instantaneous speed change has to be accepted.

## Third order motion control
In a third order motion system, acceleration is not allowed to change abruptly. Instead it is only permitted to change gradually at a rate called the "jerk". Note, the term "jerk" is commonly used to refer to instantaneous speed change when talking about 3D printers, however the more establisged use of "jerk" is to mean rate of change of acceleration.

The potential advantages of third-order motion control are:
* Less tendency to excite high frequency resonances. However, in 3D printers the troublesome resonances are generally at lower frequencies, so input shaping is a much better way to reduce exciting them.
* In order to apply extruder pressure advance, the extruder motion is modified by making the extrusion speed the commanded speed plus a component related to (typically proportional to) extruder acceleration. Since acceleration changes abruptly when using second order motion control, adding this extra component reduce the extrusion system to first order because it demands instantaneous changes in extrusion speed. If we switch to third order motion then we no longer need instantaneous changes t extrusion speed, making the extrusion second order.

The main disadvantages of third-order motion control are:
* It is much more computationally expensive, because the equations of motion change from being quadratic to being cubic;
* This in turn makes it very difficult to compute step times as a function of distance, because there isn't time to solve a cubic equation on every step, or every few steps.
# Third order motion control in RepRapFirmware