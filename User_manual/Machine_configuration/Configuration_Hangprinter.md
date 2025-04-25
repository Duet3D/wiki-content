---
title: Configuring RepRapFirmware for a Hangprinter
description: This page describes how to set up the configuration files for Hangprinters. The same firmware binary also supports Cartesian, Delta, CoreXY and other printers kinematics.
published: true
date: 2025-04-25T00:06:40.100Z
tags: 
editor: markdown
dateCreated: 2022-01-26T11:56:32.634Z
---

<!--[summary_image|377]-->

# Overview

**For a general guide to configuring RepRapFirmware, see [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian). Only the differences for Hangprinters are described here.**

RepRapFirmware is configured at run-time by means of files in the /sys folder of the on-board SD card or attached Single Board Computer (SBC). You do not have to recompile RepRapFirmware to configure it, so if you wish to update the firmware on your Duet or other compatible electronics board, you can download a ready-built binary.

**The easiest way to generate these files is using the [RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/).** But this tool does not currently support Hangprinters, so you will need to edit the generated config.g file by hand to change the kinematics type to Hangprinter.

Hangprinters are supported in RepRapFirmware 1.20 and later. The support in the standard binaries does not include spool buildup compensation or support for Mechaduino motors in torque mode. Torbjørn Ludvigsen has a fork of RRF that includes these features.

The Hangprinter architecture in its current form was designed by Torbjørn Ludvigsen, his [github repository](https://gitlab.com/tobben/hangprinter) has a large amount of information and is a great place to start to understand the Hangprinter in general.

In brief, a Hangprinter has four positioning motors (A, B, C and D) mounted either on a large effector or mounted at the anchor points. Each motor drives a spool which draws in or lets out 2 or 3 lines that pass through eyelets and then go to fixed anchor points. The D spool has 3 lines connected to anchor points in the ceiling. The A, B and C spools each have 2 lines connected to anchor points on the floor. The Hangprinter is under very active development so this may not match the current state of the art.

Additional info can be found at [Hangprinter.org](http://hangprinter.org/) or at the [Hangprinter RepRap Forum](https://reprap.org/forum/list.php?423), or [this thread in the Duet3D forum](https://forum.duet3d.com/topic/14215/).

# Movement section of config.g file

## Select Hangprinter kinematics

To tell RepRapFirmware that your printer is a Hangprinter and to define its parameters, use [M669](/User_manual/Reference/Gcodes/M669). Put command **M669 K6 Axx:yy:zz B xx:yy:zz Cxx:yy:xx Dzz Prr** in your sys/config.g file. The parameters are:

* **Knnn** Geometry class, 6 = Hangprinter
* **Axx:yy:zz** X, Y and Z coordinates of the A anchor
* **Bxx:yy:zz** X, Y and Z coordinates of the B anchor
* **Cxx:yy:zz** X, Y and Z coordinates of the C anchor
* **Dzzz** Z coordinate of the D anchor (the XY coordinates of the D anchor are 0,0)
* **Prr** Printable radius from the origin
* **Snnn** Segments per second when smooth XY motion is approximated by means of segmentation
* **Tnnn** Minimum segment length (mm) when smooth XY motion is approximated by means of segmentation

You will need to create an invisible U axis to handle the D spool motor with [M584](/User_manual/Reference/Gcodes/M584), like this:

```
M584 X0 Y1 Z2 E3 U4 P3
```

In this example, the ABCD spool motors are connected to the X Y Z and E1 motor connectors respectively (Duet 2), and the extruder motor is connected to the E0 motor connector. The P3 parameter restricts the number of visible axes to 3.

## Spool control

The [M666](/User_manual/Reference/Gcodes/M666) parameters are used to configure the buildup compensation for the Hangprinter. Each parameter adjusts how the firmware compensates for the line buildup on the spools during operation.

* **An** Anchor mode, default 1. Defines position of anchors, 0=None, 1=last-top, 2=all-top, 3-half-top. 
* **Qnn** Spool buildup factor, default 0.007. Determines how much the firmware compensates for line buildup on the spools.
* **Rnn:nn:nn:nn** Spool radii, default 75. Used for calculating the effective length of each line.
* **Unn:nn:nn:nn** Mechanical advantages on ABCD, default 2.
* **Onn:nn:nn:nn** Number of lines per spool, default 1.
* **Lnn:nn:nn:nn** Motor gear teeth of ABCD axes, default 20.
* **Hnn:nn:nn:nn** Spool gear teeth of ABCD axes, default 255.
* **Jnn:nn:nn:nn** Full steps per motor revolution for the ABCD motors, default 25.
* **Wnn** Weight of the mover in Kg, default 0. Impacts the force calculations during operation.
* **Snn** Line spring constant, default 20000. Defines the spring constant for the line, which is important for understanding how the line will behave under tension. Lower values gives more flex compensation.
* **Inn:nn:nn:nn** Minimum planned force in four directions in Newtons, default 0. This is a safety limit. Should affect only exceptional/wrong moves, for example moves outside of the reachable volume.
* **Xnn:nn:nn:nn** Maximum planned force in four directions in Newtons, default 70. This is a safety limit. Will affect moves close to the limits of the reachable volume.
* **Ynn:nn:nn:nn** Guy wire lengths, Needed for flex compenation. Guy wires go between spool and final line roller. If your spools are all mounted on the D-anchor, on the ceiling plate, then you're all good, and you don't need to configure M666 Y values explicitly. If your spools are not all on the D-anchor then you must measure guy wire lengths and set them here. If your spools are all mounted on their respective anchors, so that you have no guy wires, then you should configure zeroed guy wire lengths M666 Y0.0:0.0:0.0:0.0.
* **Tnn** Desired target force in Newtons, default 20. The flex compensation algorithm aims for at least this amount of force in the ABC line directions at all times. It can be thought of as a minimum pre-tension value. It's recommended to set it around 10 times higher than your W (mover weight in kg) value.
* **Cnn:nn:nn:nn** Torque constants, default 0. These are required for reading motor forces from ODrives. They are the same values as is configured in the ODrives themselves

## Changes to other commands

In the [M92](/User_manual/Reference/Gcodes/M92) command, the XYZU parameters are the steps per mm of line extension or retraction by the A, B, C and D spools.

The Z movement limits should be set in [M208](/User_manual/Reference/Gcodes/M208) commands as usual.

## Example

Here is an extract from a sample config.g file (Duet 2, RRF 3.x):

```
** Axis and motor configuration**
M569 P0 S1 ; drive 0 (X motor output aka A) goes forwards
M569 P1 S1 ; drive 1 (Y motor output aka B) goes forwards
M569 P2 S1 ; drive 2 (Z motor output aka C) goes forwards
M569 P3 S1 ; drive 3 (extruder) goes forwards
M569 P4 S1 ; drive 4 (E1 motor output aka U aka D) goes forwards
M584 X0 Y1 Z2 E3 U4 ; create U axis for the D motor, attached to the E1 motor connector

M669 K6 A0.0:-2163.0:-75.5 B-1841.0:741.0:-75.5 C1639.0:1404.0:-75.5 D3250.5 P1500 ; set Hangprinter kinematics parameters

M92 X101.86 Y101.86 Z101.85 U101.86 ; set steps/mm for each spool
M203 X6000 Y6000 Z6000 U6000 E3600 ; maximum linear speeds mm/minute
M906 X1000 Y1000 Y1000 U1000 E1000 ; set motor currents (mA)

M208 S0 Z1500 ; maximum height 1500mm
M208 S1 Z-5 ; minimum height -5mm
```

# Homing

Hangprinters do not have homing switches. To home your Hangprinter, we suggest the following:

* Use the [M913](/User_manual/Reference/Gcodes/M913) command to temporarily reduce the current to the ABC motors.
* Send a [G1 H2](/User_manual/Reference/Gcodes/G1) (G1 S2 in RRF 2.01 and earlier) movement command in the -U direction to energise the D motor and shorten the line to raise the effector off the floor, e.g. `G1 H2 U-100 F1000`.
* Run the ABC motors to tighten the lines. You may wish to use stall detection to stop the motors when the lines are tight, see [Stall detection and sensorless homing](/User_manual/Connecting_hardware/Sensors_stall_detection).
* Measure the approximate XYZ coordinates of the nozzle.
* Send [G92 Xxx Yyy Zzz](/User_manual/Reference/Gcodes/G92) to tell the firmware where the nozzle is, where xx, yy and zz are the coordinates of the nozzle.
* If your Hangprinter has a Z probe and you have set up a suitable bed.g file, press the Auto Calibration button in the web interface or PanelDue to run an auto calibration sequence. 3-factor auto calibration is sufficient to adjust the spool motor positions. Note: the less accurately you entered the initial coordinates of the nozzle, the higher the dive height (H parameter in the M558 command) will need to be, and the more iterations of auto calibration you will need to perform.

You may wish to create a homeall.g file that does some of these steps.

# Auto calibration

**Note:** the auto-calibration described below calibrates bed tilt or bed shape, *not* anchor positions and spool buildup.

You can use the following types of auto calibration:

* 3-factor: adjusts the spool zero positions. Useful as part of the homing sequence (see earlier).
* 6-factor: as 3-factor and also adjusts the B anchor Y coordinate and the C anchor X and Y coordinates
* 9-factor: as 3-factor and also adjusts the A, B and C anchor Z coordinates. [This may be too sensitive to line buildup to work well.]

You must use at least as many probe points as calibration factors you want to use. More probe points is better and will give you an indication of the height errors after calibration.

Auto calibration is performed by sending command [G32](/User_manual/Reference/Gcodes/G32) (which is the same command used to do auto bed compensation on a Cartesian printer). This command runs macro file **sys/bed.g**, so all the work is done there. Here is a sample bed.g file:

```
M561 ; clear any bed transform, otherwise homing may be at the wrong height
M401 ; deploy the Z probe
G30 P0 X0 Y0 Z-99999 ; dummy probe, needed only if your Z probe gives less accurate results the first time it is used after deployment

** Probe the bed and do 6-factor auto calibration**
G30 P0 X0 Y-1050 Z-99999 ; towards A anchor
G30 P1 X-460 Y-340 Z-99999 ; between A and B anchors
G30 P2 X-920 Y370 Z-99999 ; towards B anchor
G30 P3 X-50 Y535 Z-99999 ; between B and C anchors
G30 P4 X820 Y700 Z-99999 ; towards C anchor
G30 P5 X420 Y-175 Z-99999 ; between C and A anchors
G30 P6 X0 Y-525 Z-99999 ; halfway to A anchor
G30 P7 X-460 Y185 Z-99999 ; halfway to B anchor
G30 P8 X410 Y350 Z-99999 ; halfway to C anchor
G30 P9 X0 Y0 Z-99999 S6 ; centre, and auto-calibrate 6 factors

M402 ; retract the Z probe
G1 X0 Y0 Z150 F15000 ; get the head out of the way of the bed
```

The probing is commanded by the [G30](/User_manual/Reference/Gcodes/G30) commands. You can have up to 32 probe points, numbered P0 to P31. The probe positions and order are not critical, but you should cover at least the centre and the extremities of the bed, both close to anchors and opposite the anchors, and some points in between the centre and the periphery. The XY coordinates are where the nozzle will be, so if the Z probe is displaced from the nozzle then you must allow for this.

This example uses ten points: six around the periphery, one at the centre, and three halfway between the centre and the periphery.

If your Z probe has different trigger heights at different XY positions, you can add an H parameter to each G30 command. This gives the value that must be added to the G31 Z parameter to get the actual trigger height at that point. Set the G31 Z parameter so that it is accurate when probing the centre of the bed, and use the H parameter to correct for different trigger heights at the other bed probe points. The H parameter is supported in firmware version 1.09e-dc42 and later.

The S parameter on the final G30 command is the number of factors to calibrate.

# Testing the motors individually

You can test the four motion motors individually by putting the printer in relative mode ([G91](/User_manual/Reference/Gcodes/G91) command) and then using the G1 command with H2 parameter. For example, after sending G91, sending `G1 H2 X10` should increase the length of line between the A spool and the effector by 10mm; and sending G1 S2 U-20 should reduce the length of the line from the D spool by 20mm.

If a motor moves the wrong way, you can reverse it by changing the corresponding [M569](/User_manual/Reference/Gcodes/M569) command in config.g. For example, if the A spool motor moves the wrong way, change `M569 P0 S1` to `M569 P0 S0`.