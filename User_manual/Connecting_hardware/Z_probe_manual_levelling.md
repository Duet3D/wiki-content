---
title: Using the Manual Bed Levelling Assistant
description: 
published: true
date: 2021-12-15T22:43:45.908Z
tags: 
editor: markdown
dateCreated: 2021-10-28T15:01:54.156Z
---

# Introduction

To help level the bed the firmware provides the Manual Bed Levelling Assistant (MBLA) and Auto Bed Levelling (ABL).

The Manual Bed Levelling Assistant helps you to level the bed of a Cartesian, CoreXY[UV] or SCARA printer using the manual adjusting screws on the bed itself, when you don't have multiple independently-driven Z axis motors to do it for you, i.e. Auto Bed Levelling (ABL).

MBLA relies on your printer having adjustment screws of some sort that you can turn by hand to level the bed. Like Mesh Bed Compensation (MBC), it probes the bed but the result is information on how much to turn each adjustment screw to level the bed. It normally takes several iterations to get the bed truly level.

Mesh Bed Compensation (MBC) is intended to compensate for irregularities in the surface of the bed. As a result it also compensates for a bed that is not level BUT it is better to level the bed as best as you can and then rely on MBC to deal with bed surface irregularities.

ABL relies on your printer having multiple Z axis steppers that the firmware can use to level the bed. Again, like MBC and MBLA, it probes the bed but the result is stepper movement that works to level the bed. Sometimes it can take more than one iteration to get the best result.

MBC and MBLA/ABL work together to improve the performance of your printer.

This guide is specific to Manual Bed Levelling Assistant. Other guides for MBC and ABL can be found here:

[Setting up automatic probing of the print bed](/User_manual/Connecting_hardware/Z_probe_auto_probing)
[Bed levelling using multiple independent Z motors](/User_manual/Connecting_hardware/Z_probe_auto_levelling)
[Mesh bed compensation](/User_manual/Connecting_hardware/Z_probe_mesh_bed)

# Prerequisites

* This feature is implemented in firmware version 1.19 and later.
* You must have a bed or gantry that is levelled by three or four adjusting screws. Three screws is best, although on a very large bed four screws may be needed.
* Your Z probe must be correctly defined and calibrated in the [M558](/User_manual/Reference/Gcodes/M558) and [G31](/User_manual/Reference/Gcodes/G31) commands in config.g. If you have no Z probe then use `M558 P0` to indicate you will manually adjust the nozzle down to the bed (using the paper feeler test or similar).
* You must use the [M671](/User_manual/Reference/Gcodes/M671) command to define the X and Y coordinates of the adjusting screws. These coordinates will usually be outside the normal printable area defined by [M208](/User_manual/Reference/Gcodes/M208). The M671 command must come after any M667 or M669 command in config.g.
* You must set up a bed.g file in the usual way with at least as many probe points as adjusting screws. As a minimum, you should have one probe point close to each adjusting screw. You can have more probe points if you wish. For example, with a square bed you might choose to probe close to each corner even if you have just 3 bed levelling screws.
* The value of the S parameter on the final [G30](/User_manual/Reference/Gcodes/G30) command in bed.g must equal the number of adjusting screws.
* This facility does not work on delta printers. On a delta printer you can have auto calibration correct for bed tilt instead.

# How it works

A valid [M671](/User_manual/Reference/Gcodes/M671) command enables the bed levelling assistant. When you run [G32](/User_manual/Reference/Gcodes/G32) to perform bed probing, the final [G30](/User_manual/Reference/Gcodes/G30) command (the one with the S parameter) in bed.g will cause the assistant to run. It uses a least squares algorithm that minimises the sum of the height errors at the probe points. The amount by which each screw should be adjusted is reported. The adjustment requested for the first screw is always zero.

You can run [G32](/User_manual/Reference/Gcodes/G32) again to repeat the process.

The process typically causes a small shift in the Z=0 position. To correct this, if you are using the Z probe to do Z homing, you can just re-home Z after making the adjustments. Otherwise, do a single [G30](/User_manual/Reference/Gcodes/G30) probe at the centre of the bed to set the Z=0 position.

# Example

File config.g:

```
...
M671 X-15:100:215 Y190:-10:190 P0.5 ; adjusting screws at rear left (-15,190), front middle (100,-10) and rear right (215,190), thread pitch 0.5mm
...
```

File bed.g:

```
G28 ; home
M401 ; deploy Z probe
G30 P0 X20 Y190 Z-99999 ; probe near an adjusting screw
G30 P1 X180 Y190 Z-99999 ; probe near an adjusting screw
G30 P2 X100 Y10 Z-99999 S3 ; probe near an adjusting screw and report adjustments needed
M402 ; retract probe
```