---
title: Support for third-order motion control
description: This page describes the experimental support for third-order motion control, sometimes referred to as S-curve acceleration. This support is not currently available in standard builds of RepRapFirmware.
published: true
date: 2026-02-18T22:36:41.021Z
tags: 
editor: markdown
dateCreated: 2025-03-28T09:22:43.388Z
---

# What is third-order motion control?
## First order motion control
In a first order motion system, movement speed changes abruptly between moves. There is no continuous acceleration. In theory this requires infinite force to be applied to change the speed, since the acceleration is also infinite. In practice the change in speed occurs over a finite amount of time, so the movement is not quite as commanded. If the motion system is driven by stepper motors and the finite acceleration causes the actual to position differ from the commanded position by more than two full steps then the motor will skip steps, in multiples of 4 full steps.

First order motion control is practical when the speed changes are small and it is not critical that the actual position closely follows the commanded position.

## Second order motion control
In a second order motion system, changes in speed are performed by accelerating between the two speeds at a constant rate. If using open-loop stepper motors, the actual position will lag the commanded position slightly during acceleration because the amount of force developed by a stepper motors depends on the amount of lag. Similarly, when decelerating the actual position will lead the commanded position slightly. If the stepper motor(s) can supply sufficient force to achieve the required acceleration with less than two full steps of lag then no steps will be lost.

Second order motion control is practical in many applications. 3D printer firmwares (including RepRapFirmware) generally use second order motion control, except that if direction changes are to be made without coming to a complete stop then some degree of instantaneous speed change has to be accepted.

## Third order motion control
In a third order motion system, acceleration is not allowed to change abruptly. Instead it is only permitted to change gradually at a rate called the "jerk". Note, the term "jerk" is commonly used to refer to instantaneous speed change when talking about 3D printers, however the more established use of "jerk" is to mean rate of change of acceleration.

The potential advantages of third-order motion control are:
* Less tendency to excite high frequency resonances. However, in 3D printers the troublesome resonances are generally at lower frequencies, so input shaping is a better way to avoid exciting them.
* In order to apply extruder pressure advance, the extruder motion is modified by making the extrusion speed the commanded speed plus a component related to (typically proportional to) extruder acceleration. Since acceleration changes abruptly when using second order motion control, adding this extra component reduce the extrusion system to first order because it demands instantaneous changes in extrusion speed. If we switch to third order motion then we no longer need instantaneous changes to extrusion speed, making the extrusion second order after applying pressure advance.

The main disadvantages of third-order motion control are:
* It is much more computationally expensive, because the equations of motion change from being quadratic to being cubic. Depending on the situation it may be necessary to solve quadratic, cubic or quartic equations when planning motion, such that jerk is avoided at the boundaries between moves.
* This in turn makes it very difficult to compute step times as a function of distance, because there isn't time to solve a cubic equation on every step, or every few steps.

# Third order motion control in RepRapFirmware
## Phase stepping
RepRapFirmware 3.6.0 for Duet 3 6HC provides an alternative mechanism for stepper motion control called phase stepping. When this is enabled, instead of calculating the time at which each microstep is due (which involves solving a quadratic equation when using second-order motion control, and would require solving a cubic equation when using third-order motion control), at approximately fixed intervals it calculates the required position of the stepper motor and then commands the driver to apply phase currents that correspond to that position.

Calculating the required motor position only requires evaluation of a quadratic polynomial when using second order motion control, or a cubic polynomial for third order motion control. Therefore **the experimental third-order motion control support can only be enabled in firmware builds for the 6HC** and it can only be used when all axes and extruders that are driven by motors attached to the main board are configured to use phase stepping.

It is permitted to have additional axes and/or extruders driven from CAN-connected boards. However, third-order motion control is not currently supported by CAN-connected boards. Because of this, the motion commands sent to CAN-connected boards will be second-order approximations of the third-order motion used on the main board. Therefore they will not be perfectly synchronised with the main board during acceleration and deceleration. This may be acceptable for axes that are not greatly involved in movement with extrusion. For example, it is likely that the Z axis of a 3D printer can be driven from a CAN-connected board without significant issues.

Phase stepping is enabled using the [M970 command](/User_manual/Reference/Gcodes#m970-enabledisable-phase-stepping). Note, stall detection is not supported when using phase stepping; therefore if the machine uses stall-detect homing then the homing files should check which mode the axes being homes are in, disable phase stepping for those axes, perform the homing operation, then revert any axes to phase stepping that were using it. Object model field `move.axes[].phaseStep` can be used to establish whether an axis uses phase stepping.

## Extruder priority
For extruding moves, the main benefit of third order motion control is that pressure advance can be applied more accurately. Therefore, RepRapFirmware plans sequences of extruding moves so that the extrusion is third-order. The axis motion will be predominantly third-order except as follows:
* When the motion direction changes, unless movement comes to a standstill at the junction then it is inevitable that there some instantaneous speed changes will be commanded at the junction. Therefore the axis movement will be first-order at junctions.
* If the rate of extrusion per unit of distance travelled by the nozzle changes across moves, then the axis speeds will be adjusted so that the extrusion rate remains constant. If necessary, the speed at the junction will be reduced to avoid exceeding the axis instantaeous speed change limits.

For non-extruding moves the axis movement will be third-order as far as possible.

RepRapFirmware always comes to a stop between between extruding and non-extruding moves.

## Enabling third-order motion control
In firmware builds that support it the M201 command accepts an additional parameter `Tn.nn` where *n.nn* is a time in seconds called the acceleration time. This represent the time over which any axis or extruder may be accelerated from zero to its maximum acceleration as configured by M201. The jerk (i.e. maximum rate of change of acceleration) of an axis or extruder is calculated by dividing its maximum acceleration by the acceleration time.

If the acceleration time is set to zero (which is the default at startup) then second-order motion control is used. If it is set nonzero but there are any axes or extruders that use local drivers and are not configured to use phase stepping, then second-order motion control is used but a warning is generated whenever M201 is used. If it is set nonzero and all axes and extruders with local drivers use phase stepping, then third-order motion control is used.

The acceleration time parameter is common to all axes and extruders, therefore maximum jerk (i.e. rate of change of acceleration) cannot be set individually for each axis or extruder. It is available in the object model field `move.accelerationTime`.

Third-order motion is not yet supported on CAN-connected expansion boards (including main boards used as expansion boards). If third-order motion control is in use and a move involves CAN-connected motors, those motors will be commanded to execute second-order moves that have approximately the same profile as the third-order moves on the main board.

Object model field `move.usingSCurve` indicates whether or not third order motion control is in use.

## Input shaping and pressure advance
Input shaping and pressure advance can be configured and used as normal when using third-order motion control. Applying pressure advance to an extruder changes the motion of that extruder from third-order to second-order. Third-order motion remains third-order after input shaping is applied.

## Scanning Z probes
When G29 is used to generate a height map using a scanning Z probe, when calculating when to take readings RepRapFirmware assumes that second-order motion control is in use. If third-order motion control is used then the positions at which readings are taken during the acceleration and deceleration segments of the scanning move will be less accurate. Therefore, unless the acceleration and deceleration segments are very short, it is preferable to switch to second-order motion prior to running G29 using a scanning probe.