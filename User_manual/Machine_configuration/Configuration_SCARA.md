---
title: Configuring RepRapFirmware for a Serial SCARA printer
description: This page describes how to set up the configuration files for Serial SCARA printers.
published: true
date: 2022-01-26T17:10:50.111Z
tags: 
editor: markdown
dateCreated: 2022-01-26T17:10:46.643Z
---

# Overview

**For a general guide to configuring RepRapFirmware, see [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian). Only the differences for Serial SCARA printers are described here.**

RepRapFirmware is configured at run-time by means of files in the /sys folder of the on-board SD card or attached Single Board Computer (SBC). You do not have to recompile RepRapFirmware to configure it, so if you wish to update the firmware on your Duet or other compatible electronics board, you can download a ready-built binary.

**The easiest way to generate these files is using the [RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/).** But this tool does not currently support Serial SCARA printers, so you will need to edit the generated config.g file by hand to change the kinematics type to Serial SCARA printers.

# SCARA description

Serial SCARA printers are supported in RepRapFirmware 1.19 and later.

A serial SCARA printer has two joints. The **proximal** joint is the one whose XY position is fixed, and the proximal arm rotates about the proximal joint. The **distal** joint is the one on the end of the proximal arm, and the distal arm is the arm between the distal joint and the print head.

The X axis of a SCARA printer running RepRapFirmware is the direction that the proximal arm points along when its angle is zero. You can choose where you consider the zero angle to be. It would generally be sensible to have it near the middle of its range of movement. When you have chosen the position that you consider to be zero angle, that determines what the minimum and maximum angles are, which you specify in the M669 P parameters (and either the minimum or the maximum one is the trigger point of the proximal joint homing switch).

The zero angle of the distal joint is defined as the angle when both arms are lined up. So this is chosen for you, and this in turn dictates the M669 D parameters.

# Movement section of config.g file

# Telling RepRapFirmware that your printer has SCARA geometry

To tell RepRapFirmware that your printer is a SCARA and to define its parameters, use the [M669](/User_manual/Reference/Gcodes/M669) command in config.g:

`M669 K4 Pnnn Dnnn Annn:mmm Bnnn:mmm Caaa:bbb:ccc Xxxx Yxxx**`

The parameters for serial SCARA are:

* **Knnn** Geometry class, 4 = SCARA
* **Pnnn** Proximal arm length
* **Dnnn** Distal arm length
* **Annn:mmm** Ground-to-proximal joint minimum and maximum angles
* **Bnnn:mmm** Proximal-to-distal joint minimum and maximum angles
* **Caaa:bbb:ccc** Crosstalk factors (optional, default all zero)
* **Rnnn** (optional, RRF 2.03 and later only) Minimum permitted printing radius from the proximal axis. If not specified, it will be calculated to be slightly larger than the distance between nozzle and proximal axis when the distal axis is homed.* **Snnn** Maximum segments per second (optional)
* **Tnnn** Minimum segment length (optional)
* **Xxxx and Yyyy** X and Y offsets from the location of the proximal joint.

All linear dimensions are in mm and all angles are in degrees. Fractional values can be used. The A and B parameters define not only the arm movement ranges but also the assumed positions when the corresponding homing switches are triggered.

The crosstalk factors define how much the motors interfere with each other. If they are all zero then it is assumed that the X motor affects only the angle between the proximal arm and the X axis, the Y motor affects only the angle between the distal arm and the proximal arm, and the Z motor affects only the nozzle height. If this is true for your printer, you can omit the C parameter. Otherwise, if the parameter is of the form *Caaa:bbb:ccc*, then:

**aaa** is the amount by which the X motor (whose primary function is to control the proximal arm) affects the angle between the distal arm and the proximal arm. For example, if movement of the proximal motor affects the proximal-to-distal joint angle in reverse (so that an X motor movement that causes one degree of proximal arm movement also changes the angle between the distal arm and the X axis by one degree in the opposite direction) then this crosstalk factor is 1.

**bbb** is the amount by which the X motor affects the Z height. A positive value indicates that anticlockwise rotation of the proximal arm increases the nozzle height above the print surface.

**ccc** is the amount by which the Y motor affects the Z height. A positive value indicates that anticlockwise rotation of the distal arm increases the nozzle height above the print surface.

If you specify **xxx** and **yyy** as zero in the M669 command or you leave out the X and Y parameters, then X0 Y0 will be the position of the axis of the proximal joint (which is an unreachable position in most SCARA architectures). You should specify **xxx** and **yyy** in the M669 command as the location you want for X0, Y0 relative to the proximal joint. In a typical case, the X offset will be positive by an amount somewhat smaller than the square printable X range, and the Y offset will be negative by about half the square printable Y range.

# Changes to other commands

In the [M92](/User_manual/Reference/Gcodes/M92) command, the X and Y parameters are the steps per degree for the proximal and distal arm motors respectively. The Z parameter is steps/mm as usual.

In the [M574](/User_manual/Reference/Gcodes/M574) command, for the X and Y parameters a low-end homing switch means that the homing switch is located at the position of minimum angle, i.e. the arm is rotated fully clockwise as seen from above. A high-end homing switch is one that triggers when the arm is rotated fully anticlockwise.

When you give a [G1 H1 or G1 H2](/User_manual/Reference/Gcodes/G1) (G1 S1 or G1 S2 in RRF 2.01 and earlier) command, the X and Y coordinates in that command must be specified in degrees.

# Example

Here is an extract from a sample config.g file for the Helios (Duet 2, RRF 3):

```
; Axis and motor configuration
M569 P0 S1 ; Drive 0 (X) goes forwards
M569 P1 S1 ; Drive 1 (Y) goes forwards
M569 P2 S1 ; Drive 2 (Z) goes forwards
M569 P3 S1 ; Drive 3 (E0) goes forwards
M584 X0 Y1 Z2 E3 ; drive mapping

M669 K4 P100 D100 A-90:90 B-135:135 C0:bb:cc ; set SCARA kinematics parameters [TODO what are bb and cc for the Helios?]

M203 X10000 Y10000 Z300 E3600 ; maximum speeds mm/minute
M906 X800 Y800 Y800 E800 ; set motor currents (mA)

; Endstops
M574 X1 S1 P"xstop" ; configure switch-type (e.g. microswitch) endstop for low end on X via pin xstop
M574 Y1 S1 P"ystop" ; configure switch-type (e.g. microswitch) endstop for low end on Y via pin ystop
M574 Z1 S2          ; configure Z-probe endstop for low end on Z
```

In RRF 3.0, if your endstop switches produce active low outputs instead of active high, invert the output with `!` in the [M574 P parameter](/User_manual/Reference/Gcodes/M574), eg `M574 X1 S1 P"!xstop"` .

Here is a sample config.g for an ldegraaf-style SCARA printer [MPSCARA](https://www.thingiverse.com/thing:2487048) (Duet 2, RRF 2.x):

```
; End stops
M574 X0 Y0 Z0 S1 ; proximal and distal homing switches trigger when the arm is fully clockwise and are active high

; Motor settings
M569 P0 S1 ; Drive 0 (X) goes forwards
M569 P1 S0 ; Drive 1 (Y) goes backwards according to https://www.thingiverse.com/thing:2487048 Configuration.h
M569 P2 S1 ; Drive 2 (Z) goes forwards

M906 X900 Y900 Z1050 ; set motor currents (mA)

; acceleration and speed
M201 X3000 Y3000 Z3000; maximum acceleration mm^2/second
M203 X18000 Y18000 Z360; maximum speeds mm/minute

; setup SCARA kinematics
; K4 for SCARA arms
; P, D from https://www.thingiverse.com/thing:2487048 Configuration.h
; A and B guesswork
; C from https://forum.duet3d.com/topic/9346/mpscara-g28-intermediate-position-outside-machine-limits
; X from https://www.thingiverse.com/thing:2487048 Configuration.h plus some slack
; Y guesswork

M669 K4 P98.41 D100.66 A-108:108 B0:140 C-1:0:0 X100 Y-100

; axis travel limits
M208 X63 Y200 Z160; if you want more X, reduce Y (don't forget to adjust Y offset in M669)

; steps per axis
; values from https://www.thingiverse.com/thing:2487048 Configuration.h
; for red (A4988?) drivers
M92 X28.6738351 Y28.6738351 Z2560;
```

# Homing files

For each of the proximal and distal joints, you should have a homing switch either at the maximum limit (maximum anticlockwise angle as seen from above), or at the minimum limit (maximum clockwise angle). In your [M574](/User_manual/Reference/Gcodes/M574) command in config.g, for the proximal point use X1 to indicate a switch at the minimum angle limit, or X2 to indicate a maximum angle switch. For the distal joint, use Y1 to indicate a switch at the minimum angle limit, or Y2 to indicate a maximum angle switch.

The joint angles at which the switches trigger must be as defined in the appropriate P and D parameters of the [M669](/User_manual/Reference/Gcodes/M669) command in config.g

You must provide file **homeall.g**. Optionally, you can also provide **homeproximal.g**, **homedistal.g** and **homez.g**. If it is not safe to home the proximal or distal joints or the Z axis independently, omit the corresponding homing files, then homeall.g will be used instead.

File **homeproximal.g** homes the proximal joint. You should use a `G1 H1 Xnnn` command (`G1 S1 Xnnn` in RRF 2.01 and earlier) to move the joint towards the limit switch until it is triggered.

File **homedistal.g** homes the distal joint. You should use a `G1 H1 Ynnn` command (`G1 S1 Ynnn` in RRF 2.01 and earlier) to move the joint towards the limit switch until it is triggered.

File **homez.g** is exactly the same as for a Cartesian printer, see the Homing Z section in [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian). You may choose to use either an endstop switch or a Z probe for homing.

File **homeall.g** should home all axes in an appropriate order.

Here are some sample homing files. These assume that the proximal arm homes to fully clockwise, the distal arm homes to fully anticlockwise, and Z homing is done using a Z probe.

```
** File homeproximal.g**
G91 ; relative movement
G1 H2 Z4 F100 ; ensure head is clear of the bed
G1 H1 X-200 F3000 ; move proximal joint clockwise up to 200 degrees until the endstop switch is triggered
G1 H2 X10 ; move proximal joint anticlockwise by 10 degrees
G1 H1 X-20 F600 ; move proximal joint slowly to the endstop switch again
G90 ; absolute movement
G1 H2 X0 F3000 ; move proximal joint to centre of range
G91 ; relative movement
G1 H2 Z-4 F100 ; restore original Z position
G90 ; absolute movement
```

```
** File homedistal.g**
G91 ; relative movement
G1 H2 Z4 F100 ; ensure head is clear of the bed
G1 H1 Y200 F3000 ; move distal joint clockwise up to 200 degrees until the endstop switch is triggered
G1 H2 Y-10 ; move distal joint anticlockwise by 10 degrees
G1 H1 Y20 F600 ; move distal joint slowly to the endstop switch again
G90 ; absolute movement
G1 H2 Y0 F3000 ; move distal joint to centre of range
G91 ; relative movement
G1 H2 Z-4 F100 ; restore original Z position
G90 ; absolute movement
```

```
** File homez.g**
G91 ; relative movement
G1 H2 Z4 F200 ; raise head 4mm to ensure it is above the Z probe trigger height
G90 ; back to absolute mode
G1 X100 Y100 F2000 ; put head over the centre of the bed, or wherever you want to probe
G30 ; lower head, stop when probe triggered and set Z to trigger height
```

```
** File homeall.g**
G91 ; relative movement
G1 H2 Z4 F100 ; ensure head is clear of the bed
** Home proximal joint**
G1 H1 X-200 F3000 ; move proximal joint clockwise up to 200 degrees until the endstop switch is triggered
G1 H2 X10 ; move proximal joint anticlockwise by 10 degrees
G1 H1 X-20 F600 ; move proximal joint slowly to the endstop switch again
G90 ; absolute movement
G1 H2 X0 F3000 ; move proximal joint to centre of range
** Home distal joint**
G91 ; relative movement
G1 H1 Y200 F3000 ; move distal joint clockwise up to 200 degrees until the endstop switch is triggered
G1 H2 Y-10 ; move distal joint anticlockwise by 10 degrees
G1 H1 Y20 F600 ; move distal joint slowly to the endstop switch again
G90 ; absolute movement
G1 H2 Y0 F3000 ; move distal joint to centre of range
** Home Z**
G1 X100 Y100 F2000 ; put head over the centre of the bed, or wherever you want to probe
G30 ; lower head, stop when probe triggered and set Z to trigger height
```

Here's an example homeall.g for the [MPSCARA](https://www.thingiverse.com/thing:2487048):

```
; home x
G91 ; relative movement
G1 H2 Z4 F100 ; ensure head is clear of the bed
G1 H1 X-200 F3000 ; move proximal joint clockwise up to 200 degrees until the endstop switch is triggered
G1 H2 X10 ; move proximal joint anticlockwise by 10 degrees
G1 H1 X-20 F600 ; move proximal joint slowly to the endstop switch again
M117 "homed proximal arm"

; home y
G1 H1 Y-200 F3000 ; move distal joint clockwise up to -200 degrees until the endstop switch is triggered
G1 H2 Y10 ; move distal joint anticlockwise by 10 degrees
G1 H1 Y-20 F600 ; move distal joint slowly to the endstop switch again
M117 "homed distal arm"

; home z
G1 H2 Z4 F200 ; raise head 4mm to ensure it is above the switch trigger height
G1 H1 Z-200 F100 ; move Z down until the switch triggers
G90 ; back to absolute mode
G1 H2 X-60 Y60; move into printable area
G0 X0 Y0; move to zero point
```

# Testing the motors individually

You can test the three motion motors individually by putting the printer in relative mode (G91 command) and then using the G1 command with H2 parameter. For example, sending `G1 H2 X10` should rotate the proximal arm 10 degrees anticlockwise, and sending `G1 H2 X-10` should rotate the proximal arm 10 degrees clockwise. If your mechanics has crosstalk, then of course the distal arm angle and Z height may also be affected, because crosstalk factors are not applied when the H2 modifier is used. Similarly for Y and Z.

If a motor moves the wrong way, you can reverse it by changing the corresponding [M569](/User_manual/Reference/Gcodes/M569) command in config.g. For example, if the X motor moves the wrong way, change `M569 P0 S1` to `M569 P0 S0`.

# Calibration

**Important!** From a firmware perspective, the true arms are the lines from the proximal joint to the distal joint, and from the distal joint to the nozzle. If you have joints and/or a nozzle offset to one side from the physical arm, then the physical arms will not coincide with the true arms. In the following, it is the angles of the true arms that you need to measure, so *arm* means the line of the true arm.

Initially you may not know the exact joint at which the proximal and distal homing switches trigger. Calibrate them as follows:

* Choose which direction you want for the X axis.
* When the proximal *arm* is pointing along the X axis, the proximal joint is at zero angle.
* When the distal *arm* is in line with the proximal *arm*, the distal joint is at zero angle
* Measure the approximate joint angles between the zero angle position and the position at which the homing switch triggers. Also measure how far the joint can rotate the other way from the zero angle position. Use those measurements in your M669 command A and B parameters.
* Run X homing (which is really proximal homing) and Y homing (which is really distal homing).
* Send command `G1 S2 X0 Y0` to command the printer to the zero angle position.
* The proximal *arm* should now point along the X axis. If this is not where you want it to be, adjust whichever A parameter (first or second) corresponds to the position of the proximal homing switch (fully clockwise or fully anticlockwise). Adding a positive value to it will rotate the X axis clockwise.
* The distal *arm* should be **exactly** in line with the proximal *arm*. If it isn't, adjust whichever B parameter (first or second) corresponds to the position of the distal homing switch (fully clockwise or fully anticlockwise). Adding a positive value to it will rotate the distal *arm* zero position clockwise relative to the proximal *arm*.