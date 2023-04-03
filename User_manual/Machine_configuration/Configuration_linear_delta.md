---
title: Configuring RepRapFirmware for a Linear Delta printer
description: This page describes how to set up the configuration files for Delta printers, the same firmware binary also supports Cartesian, CoreXY and other printers kinematics .
published: true
date: 2022-06-01T20:25:20.114Z
tags: 
editor: markdown
dateCreated: 2021-11-30T16:48:42.130Z
---

# Overview

**For a general guide to configuring RepRapFirmware, see [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian). Only the differences for delta printers are described here.**

RepRapFirmware is configured at run-time by means of files in the /sys folder of the on-board SD card. This means that you must have an SD card in the on-board socket to use RepRapFirmware. You do not have to recompile RepRapFirmware to configure it, so if you wish to update the firmware on your Duet or other compatible electronics board, you can download a ready-built binary.

**The easiest way to generate these files is using the [RepRapFirmware configuration tool](https://configurator.reprapfirmware.org/).**

# General preferences section of config.g file

To tell RepRapFirmware that your printer is a delta and to define its parameters: 
* Add command [M665](/User_manual/Reference/Gcodes/M665) with L### R### H### B### X### Y### Z### parameters in your sys/config.g file on the on-board SD card, where ### represents a number. The L parameter is the diagonal rod length. The R parameter is your estimate of the delta radius. The H parameter is the height of the nozzle above the bed when the carriages are activating the endstop switches. The B parameter is the printable radius of the bed. X Y Z are the angular offsets of the X, Y and Z towers, and may be omitted or set to zero. All these values may include decimal portions.
* You can also use an [M666](/User_manual/Reference/Gcodes/M666) command to set the homing switch corrections.
* You also need to tell RepRapFirmware that the endstop switches are all at the high end using the [M574](/User_manual/Reference/Gcodes/M574) command.
* The motor idle current is 30% of normal by default. This may not be enough to hold the carriages of a delta up, so you may need to increase it using the I parameter in the [M906](/User_manual/Reference/Gcodes/M906) command.

## {.tabset}

### RepRapFirmware 3.x example

Add the following lines to your config.g:

```
; General preferences 
M665 R105.6 L215.0 B85 H235     ; set delta radius, diagonal rod length, printable radius and homed height
M666 X0 Y0 Z0                   ; put your endstop adjustments here

; Drives
M569 P0 S1                      ; Drive 0 (X) goes forwards
M569 P1 S1                      ; Drive 1 (Y) goes forwards
M569 P2 S1                      ; Drive 2 (Z) goes forwards
M569 P3 S1                      ; Drive 3 (E0) goes forwards
M569 P4 S1                      ; Drive 4 (E1) goes forwards
M203 X18000 Y18000 Z18000 E3600 ; maximum speeds mm/minute
M906 X800 Y800 Y800 E800 I60    ; set motor currents (mA) and increase motor idle current to 60%

; Endstops
M574 X2 S1 P"xstop"             ; active-high endstop for high end on X via pin xstop
M574 Y2 S1 P"ystop"             ; active-high endstop for high end on Y via pin ystop
M574 Z2 S1 P"zstop"             ; active-high endstop for high end on Z via pin zstop
```

If your endstop switches produce active low outputs instead of active high, invert the input by prefixing the pin name with '!', for example:

```
M574 X2 S1 P"!xstop"           ; active-low endstop for high end on X via pin xstop
```

### RepRapFirmware 2.x example

Here is an extract from a sample config.g file for the Mini Kossel, for RepRapFirmware 2.x:

```
; General preferences 
M665 R105.6 L215.0 B85 H235 ; set delta radius, diagonal rod length, printable radius and homed height
M666 X0 Y0 Z0 ; put your endstop adjustments here

; Drives
M569 P0 S1 ; Drive 0 (X) goes forwards
M569 P1 S1 ; Drive 1 (Y) goes forwards
M569 P2 S1 ; Drive 2 (Z) goes forwards
M569 P3 S1 ; Drive 3 (E0) goes forwards
M569 P4 S1 ; Drive 4 (E1) goes forwards
M203 X18000 Y18000 Z18000 E3600 ; maximum speeds mm/minute
M906 X800 Y800 Y800 E800 I60 ; set motor currents (mA) and increase motor idle current to 60%

; Endstops
M574 X2 Y2 Z2 S1 ; all endstops at high end, active high
```

If your endstop switches produce active low outputs instead of active high, replace S1 in the M574 command by S0.

## Notes on configuration

If your delta printer intentionally does not place the towers at the corners of an equilateral triangle, you can use additional X, Y and Z parameters in the M665 command to describe this. Consider a circle that passes through all three towers. The origin (i.e. bed centre assumed by the firmware) will be the centre of that circle. Label the towers X Y and Z moving around the circle in an anticlockwise direction as seen from above. Let X', Y' and Z' be the points on the circle where the towers would be if the towers were all 120 degrees apart and the Z tower in the +Y direction from the bed centre. Then the M665 X parameter is the angle from X' to X, the Y parameter is the angle from Y' to Y, and the Z parameter is the angle from Z' to Z, measured in degrees moving anticlockwise seen from above.

Example: a "square delta" has the towers at three of the four corners of a square. If Z is the middle tower and is at the rear left of the machine, then the M665 X Y Z parameters should be +15, +75 and +45 respectively.

The maximum usable XYZ speeds you can set in the M203 command depend on your tower steps/mm value. As a general guide, your maximum XYZ speeds in mm/minute multiplied by your tower steps/mm should be no more than 3.6 million when running firmware 1.09i-dc42 and later (older firmware versions have lower limits). If in doubt, try some long high-speed moves, then run command M122 and look at the MaxReps value in the output, which you should try to keep below 100. In practice, achieving the maximum speed that the firmware is capable of may require you to use 24V power instead of 12V power, in order that the driver chips can change the current in the stepper motors fast enough.

# Homing file

When configured for a delta printer, RepRapFirmware will always home all three towers by executing sys/homedelta.g when any G28 command is processed, ignoring any X, Y or Z parameters. Typical contents of homedelta.g would be:

```
G91 ; use relative positioning
G1 H1 X300 Y300 Z300 F2500 ; move all carriages up 300mm, stopping at the endstops
G1 H2 X-5 Y-5 Z-5 ; move all towers down 5mm
G1 H1 X8 Y8 Z8 F500 ; move towers slowly up 8mm, stopping at the endstops
G1 H2 X-5 Y-5 Z-5 F10000 ; move carriages down 5mm
G90 ; back to absolute positioning
```

In the above example, the X, Y and Z parameters in the first G1 H1 command have all been set to 300. This assumes that from any starting point, the maximum distance that any carriage has to move up to reach its homing switch is 300mm. **If your delta printer is larger than a Mini Kossel, you will need to increase these values**, otherwise the carriages may stop before they reach the homing switches.

During initial testing, you may wish to reduce the homing speed value “2500” in the same G1 command to a lower value, to give yourself more time to press the reset or power button if something goes wrong. You may also wish to reduce the motor currents during initial testing using the M906 command.

The above works because when the H1 or H2 parameter is used in a G0 or G1 command, the X, Y and Z values in the command are used as values for the individual towers instead of the head coordinates. In the case of H1, the homing switches (aka endstop switches) are activated too.

# Setting up the Z probe deploy and retract files

If your machine has a mechanical Z probe that needs to be deployed by moving the head in certain ways (e.g. standard Mini Kossel), then you need to set up macro files for deploying and retracting the probe. These are typically named sys/deployprobe.g and sys/retractprobe.g. Here is a sample deployprobe.g file:

```
M564 S0 ; don't apply limits
G1 X25 Y93 Z40 F10000 ; put probe arm next to belt
G1 X-5 F500 ; move probe arm slowly across belt
G1 X12 F1000 ; move probe back
G1 X0 Y0 F10000 ; move to somewhere sensible
M564 S1 ; apply limits again
```

Here is a sample retractprobe.g file:

```
M564 S0 ; don't apply limits
G1 Z40 F10000 ; raise head
G1 X-59 Y66 Z35 ; move over the post
G1 Z7 F500 ; push probe down on post
G1 Z35 F10000 ; raise head again
G1 X0 Y0 ; move to somewhere sensible
M564 S1 ; apply limits again
```

# Setting up the auto calibration file

RepRapFirmware supports a fast auto-calibration process. From a single set of bed probe readings, the firmware uses a mathematical model of the delta geometry to calculate the corrections needed so as to minimise the sum of the squares of the height errors at the probe points. It reports the RMS (root-mean-square) of the bed probe height error before probing, and the expected RMS error when the calibration has been done. On a typical delta printer, the auto calibration process takes about 30 seconds, this being the time taken to probe the bed.

The mathematical model of delta geometry used by RepRapFirmware assumes that the bed is flat, all diagonal rods have the same lengths, and all three towers are perpendicular to the bed. If these constraints are satisfied, then a single auto calibration cycle is sufficient. If not, you may need to run auto calibration two or three times before the corrections converge.

Auto calibration is performed by sending command G32 (which is the same command used to do auto bed compensation on a Cartesian printer). This command runs macro file **sys/bed.g**, so all the work is done there. Here is a sample bed.g file:

```
M561 ; clear any bed transform, otherwise homing may be at the wrong height
G28 ; home the printer

M401 ; deploy the Z probe
G30 P0 X0 Y0 Z-99999 ; dummy probe, needed only if your Z probe gives less accurate results the first time it is used after deployment

; Probe the bed and do 6-factor auto calibration
G30 P0 X-73.6 Y-42.5 Z-99999 ; X tower
G30 P1 X0 Y-85 Z-99999 ; between X and Y towers
G30 P2 X73.6 Y-42.5 Z-99999 ; Y tower
G30 P3 X73.6 Y20 Z-99999 ; between Y and Z towers
G30 P4 X0 Y67 Z-99999 ; Z tower
G30 P5 X-73.6 Y20 Z-99999 ; between Z and X towers
G30 P6 X-36.8 Y-21.25 Z-99999 ; half way to X tower
G30 P7 X36.8 Y-21.25 Z-99999 ; half way to Y tower
G30 P8 X0 Y42.5 Z-99999 ; half way to Z tower
G30 P9 X0 Y0 Z-99999 S6 ; centre, and auto-calibrate 6 factors

M402 ; retract the Z probe

G1 X0 Y0 Z150 F15000 ; get the head out of the way of the bed
```

The probing is commanded by the G30 commands. You can have up to 32 probe points, numbered P0 to P31. The probe positions and order are not critical, but you should cover at least the centre and the extremities of the bed, both next to towers and opposite the towers, and some points in between the centre and the periphery. The XY coordinates are where the nozzle will be, so if the Z probe is displaced from the nozzle then you must ensure that the probe is always over the bed, even if this means moving some of the probe points in from the edge.

This example uses ten points: six around the periphery, one at the centre, and three halfway between the centre and the periphery. For large delta printers, add at least 3 more probe points halfway to the periphery.

If your Z probe has different trigger heights at different XY positions, you can add an H parameter to each G30 command. This gives the value that must be added to the G31 Z parameter to get the actual trigger height at that point. Set the G31 Z parameter so that it is accurate when probing the centre of the bed, and use the H parameter to correct for different trigger heights at the other bed probe points. The H parameter is supported in firmware version 1.09e-dc42 and later.

The S parameter on the final G30 command defines what calculation is actually done, as follows:

**S=-1** Don't adjust anything, just print the height error at each probe point
**S=0** Equivalent to S=<number_of_points_probed>
**S=3** Adjust homing switch corrections only
**S=4** Adjust homing switch corrections and delta radius
**S=6** Adjust homing switch corrections, delta radius, and X and Y tower position offsets
**S=7** Adjust homing switch corrections, delta radius, X and Y tower position offsets, and diagonal rod length
**S=8** Adjust homing switch corrections, delta radius, X and Y tower position offsets, and bed tilt
**S=9** Adjust homing switch corrections, delta radius, X and Y tower position offsets, diagonal rod length, and bed tilt

The adjustments are made so as to minimise the sum of the squares of the height errors.

If your printer is accurately built, then 4-factor calibration may be sufficient. For normal use I recommend 6-factor calibration. If you choose 7-factor calibration, then you need to include probe points that are opposite the towers and far enough away so that the rods to the farthest tower are nearly horizontal, otherwise the diagonal rod length is not very well defined by the probing errors. To achieve this, you will need an oversized bed, and you may need to temporarily allow movement outside the normal printing radius using the M574 command.

If you wish, you can define several variants of bed.g and run them using the M98 command, or just 'print' them like any other gcode file. In particular, it is useful to have a variant that uses S-1 on the final G30 command, so that you can check the height errors multiple times without adjusting anything, to get an idea of how reproducible the Z probe height readings are.

To calculate suitable XY positions for probing and generate the bed.g file, use this [bed wizard from escher3d.com](http://escher3d.com/pages/wizards/wizardbed.php).

**Very Important!** By default, probing starts 3mm above where the printer thinks the bed is. This is sufficient when your printer is calibrated, however it may not be enough for first-time calibration if the endstops or the delta radius are a long way off. So **it is recommended to increase it to 20mm (or more) for first time calibration, by increasing the H parameter in the M558 command in the Z probe configuration section of config.g to H20**. See the Z probe section of [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian).

## Probing by pushing the bed with the hotend

Some probing systems, as FSR or the system used on the Fisher delta, needs to push the bed for probing, but the force to trigger the probe depends of the probing location.

This means that the printer mechanism deflect under load and there is a probing offset variation between each probe point. On the Fisher, this variation may be up to 0.15~0.2 mm.

The H parameter to the G30 command can be used to define this offset.

Example with 4 parameters and 7 points calibration:

```
G30 P0 X-60 Y-37.5 Z-99999     ; X tower
G30 P1 X64.95 Y-37.5 Z-99999   ; Y tower
G30 P2 X0 Y75 Z-99999          ; Z tower
G30 P3 X-32.48 Y-18.75 Z-99999 ; half way to X tower
G30 P4 X32.48 Y-18.75 Z-99999  ; half way to Y tower
G30 P5 X0 Y37.5 Z-99999        ; half way to Z tower
G30 P6 X4 Y0 Z-99999 S4        ; centre, and auto-calibrate
```

The offset defined in G31 Z parameter (set up in config file) will be added to the probe points offsets as defined above.

# Updating config.g

If the calibration errors are small (e.g. the M665 arm length and delta radius and the M666 endstop corrections are accurate to better than 0.5mm), a single execution of bed.g will calibrate your printer accurately. Larger errors my require 2 or 3 runs to calibrate out. So I suggest the following procedure:

1. Run auto calibration, then use the M665 and M666 commands with no parameters to see the adjustments made.
1. Repeat (1) until the M665 and M666 results converge.
1. Send M500 to save the M665 and M666 commands in config-override.g. (ensure there is M501 at the end of your config.g to load them again on reset/powerup
1. If desired copy the M665 and M666 commands from config-override.g into config.g, These then become the default starting conditions, even if you delete or overwrite config-override.g
1. Reset the controller to load the new config.g/config-override.g file (easiest way is to use the Emergency Stop button on the web interface).
1. Run auto calibration again and check that the height errors found by probing are small (less than 0.5mm). Then reduce the H parameter in the M558 command to a lower value (e.g. 3mm) for faster bed probing.

After this, a single auto calibration run should be sufficient. It is quick enough to be run at the beginning of every power-on or every print even to take into account small variations in printer geometry that could be caused by temperature differences for example.

# Testing the motors and endstops individually

You can test the three tower motors individually by putting the printer in relative mode (G91 command) and then using the G1 command with H2 parameter. For example, sending G1 H2 X10 should move the X carriage up 10mm, and sending G1 H2 X-10 should move the X carriage down 10mm. Similarly for Y and Z. If a motor moves the wrong way, you can reverse it by changing the corresponding M569 command in config.g. For example, if the X motor moves the wrong way, change M569 P0 S1 to M569 P0 S0.

When you have the motors moving the right way, test the endstops. See Connecting endstop switches, [Test endstop switches section](/User_manual/Connecting_hardware/Sensors_endstops#test-endstop-switches).

**It is essential that the endstop switch connected to the X endstop input is on the same tower as the motor connected to the X motor output that drives the carriage on the X tower**. A common mistake is to have endstop or motor connections swapped. So check this carefully. Make sure that the carriage that moves when you send a G1 H2 X command is on the same tower as the endstop that when triggered shows as X triggered in M119 or in the Machine Properties page of DWC.

After that you can check the combined effects of endstop switches and motors. Send G91 as before, but use H1 instead of H2 in the G1 commands. Each motor should stop early when you trigger the corresponding endstop.

# Adding additional towers to carry flying extruders

RRF 2.03 and later allow you to configure more than 3 towers (maximum 6 in total) on a delta machine. Typically, additional towers are used to carry extruders, allowing the Bowden tube to be shortened.

To use additional towers:

1. Use the M584 command to create additional axes, 1 for each tower. Example:
```
M584 X0 Y1 Z2 U4 E3
```
2. Specify the rod lengths to each tower individually in the M665 command. Example:
```
  M665 L360:360:360:250 R180.3 H470 B150.0
```
This specifies that the XYZ towers all have 360mm rods to the effector and there is a 4th tower with a 250mm "rod".  This 250mm value is the distance that RRF will maintain between the extruder output and filament entry point on the hot end  which is normally at the centre of the  effector), so roughly the length of the Bowden tube.

The M665 command must be later in config.g than the M584 command that creates the additional axes.

You must specify the XY offsets of the extruder outputs on additional towers relative to machine centre in the M669 command, for example:
```
M669 X150 Y100
```
The M669 command must come after the M665 command.

You must provide homing switches for the additional carriages, and you must home the additional towers simultaneously with the XYZ towers in homedelta.g. Adjust the height of the homing switches for additional towers to get the extruder at the correct height relative to the effector. In RepRapFirmware 2.04 and later you can use M666 corrections on additional towers to fine-adjust the height of the additional carriages..

As usual, RepRapFirmware 2.x will assign endstop inputs in the order that axes are created. For example, using the example M584 command given earlier, the U tower endstop must be connected to the E0 endstop input. If you are using RepRapFirmware 3 then you must declare the additional tower endstops explicitly using M574 commands.

You can  add parameter P3 to the M584 command to hide the additional axis during  normal use, but if you do that then you must un-hide it using M584 P4 at the start of homedelta.g so that you can command U axis movement.