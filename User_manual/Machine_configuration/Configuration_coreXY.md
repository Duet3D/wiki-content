---
title: Configuring RepRapFirmware for CoreXY Printer
description: This page describes how to set up the configuration files for CoreXY printers, the same firmware binary also supports Cartesian, Delta and other printers kinematics .
published: true
date: 2022-05-04T12:33:03.061Z
tags: 
editor: markdown
dateCreated: 2021-11-30T16:28:00.743Z
---


This page describes how to set up the configuration files for CoreXY printers, the same firmware binary also supports Cartesian, Delta and other printers kinematics .

<!--[summary_image|373]-->

# Overview

**For a general guide to configuring RepRapFirmware, see [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian). Only the differences for CoreXY printers are described here.**

RepRapFirmware is configured at run-time by means of files in the /sys folder of the on-board SD card. This means that you must have an SD card in the on-board socket to use RepRapFirmware. You do not have to recompile RepRapFirmware to configure it, so if you wish to update the firmware on your Duet or other compatible electronics board, you can download a ready-built binary.

**The easiest way to generate these files is using the [RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/Start).**

# Main configuration file

## General preferences section

### {.tabset}

#### For Firmware 2.03 and later

To switch the firmware to CoreXY mode, add this command to the General preferences section:

```
M669 K1 ; switch to CoreXY mode
```
This gives a movement matrix of:
```
M669
Kinematics is CoreXY, no segmentation, matrix:
1.00 1.00 0
1.00 -1.00 0
0 0 1.00
```

See here for specifics: [M669 Set kinematics type and kinematics parameters](/User_manual/Reference/Gcodes/M669)

By default, the firmware assumes that the paired axes (e.g. X and Y for a CoreXY machine) move equal amounts when just one motor turns. This is not always the case, for example a CoreXZ machine typically uses additional pulleys so that the Z axis moves only 1/3 of the amount of the X axis. To allow for this, you can use X, Y and Z parameters to specify that the motor movements should be multiplied by the given factor when moving the specified axis. So the typical CoreXZ machine should be configured like this:

```
M669 K2 Z3:0:-3 ; switch to CoreXZ mode and multiply motor movements by 3 for the Z axis
```
This gives a movement matrix of:
```
M669
Kinematics is CoreXZ, no segmentation, modified matrix:
1.00 0 1.00
0 1.00 0
3.00 0 -3.00
```

#### RepRapFirmware earlier than 2.03

To switch the firmware to CoreXY mode, add this command to the General preferences section:

```
M667 S1 ; switch to CoreXY mode
```

Use S1 for CoreXY, S2 for CoreXZ, and S3 for CoreYZ.

See here for specifics: [M667 Select CoreXY or related mode](/User_manual/Reference/Gcodes/M667)

By default, the firmware assumes that the paired axes (e.g. X and Y for a CoreXY machine) move equal amounts when just one motor turns. This is not always the case, for example a CoreXZ machine typically uses additional pulleys so that the Z axis moves only 1/3 of the amount of the X axis. To allow for this, you can use X, Y and Z parameters to specify that the motor movements should be multiplied by the given factor when moving the specified axis. So the typical CoreXZ machine should be configured like this:

```
M667 S2 Z3 ; switch to CoreXZ mode and multiply motor movements by 3 for the Z axis
```

## Testing motor movement

For a CoreXY or H-Bot machine, RepRapFirmware assumes that the motor connected to the X motor output moves the head in the +X and +Y directions when it runs forwards, and that the Y motor moves the head in +X and -Y directions when it runs forwards. *Note: firmware 1.18 and earlier assumes that the motor connected to the Y motor output moves the head in the -X and +Y directions when it runs forwards*. So you can start with these M569 commands in config.g:

```
M569 P0 S0 ; X motor runs forwards
M569 P1 S0 ; Y motor runs forwards
```

Test for the correct X motor movement by sending these commands from the console:

```
G91
G1 H2 X10 F3000
```

If the head moves diagonally in the +X and +Y directions, all is well. If it moves in the -X and -Y directions, change the S parameter to S1 in the M569 P0 command. If it moves towards +X and -Y, or towards -X and +Y, then either turn the power off and swap the X and Y motor connections, or use M584 in config.g to swap the X and Y motor drivers over.

When you have the X motor moving correctly, test the Y motor by sending from the console:

```
G91
G1 H2 Y10 F3000
```

If the head moves diagonally in the +X and -Y directions, all is well. If it moves in the -X and +Y directions, change the S parameter in the M569 P1 command to S1. *Different rules applies to firmware 1.18 and earlier*.

**Important:** make sure that you have chosen a right-hand axis system. That is, looking down on the printer the +Y direction should be 90 degrees anticlockwise from the +X direction. If instead it is 90 degrees clockwise, you have a left-hand axis system, which will give you mirror-image prints.

# Homing files

## homeall.g file

The method of setting up simultaneous homing is different for CoreXY printers. On a Cartesian printer, if you use the G1 command with H1 parameter to perform a homing move on several axes simultaneously, each axis will continue moving until either the specified movement amount has been completed or the homing switch for that axis has been triggered. On a CoreXY printer, the whole move will be terminated when the first homing switch is triggered. Therefore, to do simultaneous XY homing, your homeall.g file needs to first home X and Y together, then home them separately. For example, if your printer has homing switches at the low ends of the X and Y axes, use this pattern:

```
G91                     ; relative mode
G1 H1 X-240 Y-240 F3000 ; coarse home X or Y
G1 H1 X-240             ; coarse home X
G1 H1 Y-240             ; coarse home Y
G1 X4 Y4 F600           ; move away from the endstops
G1 H1 X-10              ; fine home X
G1 H1 Y-10              ; fine home Y
; Z homing section follows
...
```

# Commissioning

During commissioning, you can test the X and Y motors independently by using the H2 modifier on the G1 command, like this:

```
G91        ; relative mode
G1 H2 X10  ; move the X motor forward 10mm
G1 H2 X-10 ; move the X motor back 10mm
G1 H2 Y10  ; move the Y motor forward 10mm
G1 H2 Y-10 ; move the Y motor back 10mm
G90        ; back to absolute mode
```