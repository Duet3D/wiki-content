---
title: Configuring RepRapFirmware for a Polar printer
description: This page describes how to set up the configuration files for Polar printers.
published: true
date: 2022-01-26T12:44:43.105Z
tags: 
editor: markdown
dateCreated: 2022-01-26T12:44:43.105Z
---

<!--[summary_image|379]-->

# Overview

A Polar printer has a turntable (rotating bed) and an arm that moves along the radius of the bed. The radius motor should be connected to the X motor output of the Duet, and the turntable motor should be connected to the Y motor output.

**For a general guide to configuring RepRapFirmware, see [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian). Only the differences for polar printers are described here.**

RepRapFirmware is configured at run-time by means of files in the /sys folder of the on-board SD card or attached Single Board Computer (SBC). You do not have to recompile RepRapFirmware to configure it, so if you wish to update the firmware on your Duet or other compatible electronics board, you can download a ready-built binary.

**The easiest way to generate these files is using the [RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/).** But this tool does not currently support Polar printers, so you will need to edit the generated config.g file by hand to change the kinematics type to Polar.

Polar printers are supported in RepRapFirmware 1.20 and later.

# Movement section of config.g file

## Select Polar kinematics

To tell RepRapFirmware that your printer is Polar and to define its parameters, use [M669](/User_manual/Reference/Gcodes/M669). Put command **M669 K7 Raaa:bbb Hccc Fnnn Annn** in your sys/config.g file. The parameters are:

* **Knnn** Geometry class, 7 = Polar
* **Raaa:bbb** Minimum and maximum radius in mm. If only one value it given it will be used as the maximum radius, and the minimum radius will be assumed to be zero.
* **Hnnn** Radius in mm at which the homing switch is triggered during a homing move. If this parameter is not present, the homing switch is assumed to trigger at the minimum radius.
* **Fnnn** Maximum turntable speed in degrees per second
* **Annn** Maximum turntable acceleration in degrees per second per second
* **Snnn** Segments per second (because smooth XY motion is approximated by means of segmentation)
* **Tnnn** Minimum segment length (mm) (because smooth XY motion is approximated by means of segmentation)
* **Xnnn** X offset of bed origin from turntable centre (not yet implemented)
* **Ynnn** Y offset of bed origin from proximal joint (not yet implemented)

### Notes

The **A** and **F** parameters only apply to normal moves not to G1 H2 (individual motor) moves. The intention is that when printing well away from the centre, the normal X and Y limits set by [M201](/User_manual/Reference/Gcodes/M201) and [M203](/User_manual/Reference/Gcodes/M203) are sufficient. When printing at a small radius, movement may need to be slowed down to limit the turntable speed and acceleration.

There is currently no facility for offsetting the radius arm sideways from the centre of rotation of the turntable, or for moving the origin.

## Changes to other commands

In the [M92](/User_manual/Reference/Gcodes/M92) command, the Y parameter is the steps per degree of rotation of the turntable.

When you give a [G1 H1 or G1 H2](/User_manual/Reference/Gcodes/G1) command (G1 S1/G1 S2 in RRF 2.01 and earlier), the Y coordinate in that command must be specified in degrees.

## Example

Here is an extract from a sample config.g file (Duet 2, RRF 3):

```
; Axis and motor configuration
M569 P0 S1 ; Drive 0 (X) goes forwards
M569 P1 S1 ; Drive 1 (Y) goes forwards
M569 P2 S1 ; Drive 2 (Z) goes forwards
M569 P3 S1 ; Drive 3 (E0) goes forwards
M584 X0 Y1 Z2 E3

M669 K7 R0:150 H0.3 F30 A30 ; set Polar kinematics parameters

M203 X6000 Y6000 Z300 E3600 ; maximum linear speeds mm/minute
M906 X800 Y800 Y800 E800 ; set motor currents (mA)

; Endstops
M574 X1 S1 P"xstop" ; configure switch-type (e.g. microswitch) endstop for low end on X via pin xstop
M574 Y1 S1 P"ystop" ; configure switch-type (e.g. microswitch) endstop for low end on Y via pin ystop
M574 Z1 S2          ; configure Z-probe endstop for low end on Z 

```

If your endstop switches produce active low outputs instead of active high, invert the output with `!` in the [M574 P parameter](/User_manual/Reference/Gcodes/M574), eg `M574 X1 S1 P"!xstop"` .

# Homing files

You must provide homing files homeradius.g, homebed.g, homez.g and homeall.g.

**homeradius.g** should use a G1 H1 Xnnn command to move towards the radius at which the homing switch triggers. Then back off a little and repeat at slower speed.

**homebed.g** should move the turntable in an appropriate direction until the homing switch is triggered. If there is no homing switch for the turntable, then set the radius motor to a known position using a G1 H2 X command and then use G92 to define the X and Y positions. Note that if you want to support power down and resume functionality, you must provide a turntable homing switch so that the bed position can be restored.

**homez.g** is as for other types of printer, and **homeall.g** combines all the homing moves serially and/or concurrently as appropriate.

Here are some sample homing files. These assume that the radius homes to low radius, there is no turntable homing switch, and Z homing is done using a Z probe.

```
; File homeradius.g
G91 ; relative movement
G1 H2 Z4 F100 ; ensure head is clear of the bed
G1 H1 X-200 F3000 ; move radius towards minimum until the endstop switch is triggered
G1 H2 X5 ; move radius out by 5mm
G1 H1 X-20 F600 ; move radius slowly to the endstop switch again
G1 H2 Z-4 F100 ; restore original Z position
```

```
; File homebed.g
G91 ; relative movement
G1 H2 Z4 F100 ; ensure head is clear of the bed
G90 ; absolute movement
G1 H2 X50 ; move to 50mm radius
G92 X50 Y0 ; define current position as X50 Y0
G91 ; relative movement
G1 H2 Z-4 F100 ; restore original Z position
```

```
; File homez.g
G91 ; relative movement
G1 H2 Z4 F200 ; raise head 4mm to ensure it is above the Z probe trigger height
G90 ; back to absolute mode
G1 X0 Y0 ; put head over the centre of the bed, or wherever you want to probe
G30 ; lower head, stop when probe triggered and set Z to trigger height
```

```
; File homeall.g
G91 ; relative movement
G1 H2 Z4 F100 ; ensure head is clear of the bed
G1 H1 X-200 F3000 ; move radius towards minimum until the endstop switch is triggered
G1 H2 X5 ; move radius out by 5mm
G1 H1 X-20 F600 ; move radius slowly to the endstop switch again
; Home bed
G90 ; absolute movement
G1 H2 X50 ; move to 50mm from centre of bed 
G92 X50 Y0 ; define current position as X50 Y0
; Home Z
G1 X5 Y0 ; put head near the centre of the bed, or wherever you want to probe
G30 ; lower head, stop when probe triggered and set Z to trigger height
```

# Testing the motors individually

You can test the three motion motors individually by putting the printer in relative mode (G91 command) and then using the G1 command with H2 parameter. For example, sending `G1 H2 X10` should increase the radius by 10mm, and sending `G1 H2 Y-10` should rotate the turntable by 10 degrees clockwise.

If a motor moves the wrong way, you can reverse it by changing the corresponding [M569](/User_manual/Reference/Gcodes/M569) command in config.g. For example, if the radius motor moves the wrong way, change `M569 P0 S1` to `M569 P0 S0`.