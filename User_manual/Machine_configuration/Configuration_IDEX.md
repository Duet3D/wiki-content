---
title: Configuring RepRapFirmware for an IDEX printer
description: This page describes how to set up the configuration files for IDEX printers, the same firmware binary also supports Cartesian, Delta, CoreXY and other printers kinematics .
published: true
date: 2023-09-26T22:40:20.214Z
tags: 
editor: markdown
dateCreated: 2021-11-30T17:01:00.635Z
---

<!-- Migrated, not edited! -->

# Overview

**For a general guide to configuring RepRapFirmware, see [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian). Only the differences for IDEX printers are described here.**

RepRapFirmware is configured at run-time by means of files in the /sys folder of the on-board SD card. This means that you must have an SD card in the on-board socket to use RepRapFirmware. You do not have to recompile RepRapFirmware to configure it, so if you wish to update the firmware on your Duet or other compatible electronics board, you can download a ready-built binary.

**The easiest way to generate these files is using the [RepRapFirmware configuration tool](https://configurator.reprapfirmware.org/).**

RepRapFirmware 1.16 and later allow you to configure up to four X-carriages. The common case is two X-carriages on one set of rails, which home to opposite ends of the X axis. When one head is printing, the other is parked at or near its home position. This arrangement is sometimes referred to as IDEX.

For each additional X-carriage, you need to:

* Define an additional axis to represent the carriage. In the examples that follow, the second X carriage on an IDEX machine is defined as the U axis.
* Configure all the usual parameters for the additional axis/axes (i.e. motors used, motor current, steps/mm, accelerations, speed, jerk, axis limits, endstop configuration, and homing files)
* When defining tools on the second carriage, use the axis mapping facility to map the appropriate standard axis to the new axis (e.g. map the X axis to the U axis) or axes (e.g. map X to both X and U)
* Have free endstop inputs to home the additional axes
* Set up the tfree#.g files to home or park a carriage when you stop using it.

A machine with 2 independent X carriages needs 4 axis motors and 2 extruder motors, total 6. As Duet 2 and Duet 3 Mini 5+ mainboards have only 5 motor drivers, you will need to add a 6th stepper driver. The easiest way is to use a [Duet 3 Expansion Mini 2+](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_Mini_2+) with the Duet 3 Mini 5+, a [DueX2 or DueX5](/Duet3D_hardware/Duet_2_family/DueX2_and_DueX5) for Duet 2 WiFi/Ethernet, or a [Dual Stepper Driver Expansion](/Duet3D_hardware/Duet_2_family/Dual_Stepper_Driver_Expansion_Module) for Duet 2 Maestro. Alternatively, see [Connecting external stepper and servo motor drivers](/User_manual/Connecting_hardware/Motors_connecting_external)

# Creating new axes

Create a new axis by defining the motors it uses in a M584 command. Example for an IDEX machine:

```
M584 X0 Y1 Z2 U3 E4:5 ; create the U axis and assign stepper driver 3 to it
```

The M584 command should come before every other command that needs to refer to the new axes, because additional axis letters are not recognised in other G and M commands until the corresponding axes have been created. So put it at the beginning of the movement section of config.g.

Note: In RRF 2.x and earlier, if you create axis V then axis U will also be created even if you didn't ask for one. Similarly, if you create the W axis then U and V will also be created. But the motor assignments for the extra new axes will probably not be what you want. In RRF 3.x and later, only the axis requested is created; i.e. if you create the W axis, U and V will not be created.

# Configuring new axes

Your new axes should be configured in your M201, M203, M208, M350, M566, M574 and M906 commands in config.g exactly as for the other axes. For most of these commands, you just need to add the additional axis parameters. For example, if you were using M203 X15000 Y15000 Z100 in your single carriage machine, change it to M203 X15000 Y15000 U15000 Z100 for IDEX.

The M208 commands will be slightly different, because on an IDEX machine the movement ranges of the two carriages will be different, for example:

```
M208 X200 Y200 U250 Z200
M208 S1 X-50 Y0 U0 Z0
```

In this example, the X carriage moves along the X axis from -50 to 200 and the U carriage moves from 0 to 250. So there is a printable zone accessible to both carriages from 0 to 200.

Here is a sample Drives section of config.g for an IDEX machine:

```
; Drives
M569 P0 S1 ; Drive 0 goes forwards (change to S0 to reverse it)
M569 P1 S1 ; Drive 1 goes forwards
M569 P2 S1 ; Drive 2 goes forwards
M569 P3 S1 ; Drive 3 goes forwards
M569 P4 S1 ; Drive 4 goes forwards
M569 P5 S1 ; Drive 5 goes forwards
M584 X0 Y1 Z2 U3 E4:5 ; Create U axis for second X carriage before we try to configure it

M906 X800 Y800 U800 Z800 E1000 ; Set motor currents (mA)
M201 X800 Y800 U800 Z15 E1000 ; Accelerations (mm/s^2)
M203 X15000 Y15000 U15000 Z100 E3600 ; Maximum speeds (mm/min)
M566 X600 Y600 U600 Z30 E20 ; Maximum jerk speeds mm/minute
M208 X200 Y200 U250 Z200 ; Set axis maxima (adjust to suit your machine)
M208 X-50 Y0 U0 Z-0.2 S1 ; Set axis minimum (adjust to make X=0 and Y=0 the edge of the bed)
M92 X80 Y80 U80 Z2560 ; Set axis steps/mm
M92 E420:420 ; Set extruder steps per mm
```

For an IDEX machine, the X endstop will be at the low end and the U endstop will be at the high end. Remember to declare this in the M574 command. 
NOTE: in RRF 2.x and earlier, the U axis uses the E0 endstop input, and the V axis uses E1.

Example of the endstop congiration:

```
; Endstops

; RRF 3.x, Duet 3
M574 X1 S1 P"io1.in"                            ; configure switch-type (e.g. microswitch) endstop for low end on X via pin io1.in
M574 Y1 S1 P"io2.in"                            ; configure switch-type (e.g. microswitch) endstop for low end on Y via pin io2.in
M574 U2 S1 P"io3.in"                            ; configure switch-type (e.g. microswitch) endstop for high end on U via pin io3.in
```

```
; RRF 3.x, Duet 2
M574 X1 S1 P"xstop"                             ; configure switch-type (e.g. microswitch) endstop for low end on X via pin xstop
M574 Y1 S1 P"ystop"                             ; configure switch-type (e.g. microswitch) endstop for low end on Y via pin ystop
M574 U2 S1 P"e0stop"                            ; configure switch-type (e.g. microswitch) endstop for high end on U via pin e0stop 
```

```
; RRF 2.x, Duet 2 : 
M574 X1 Y1 Z0 U2 S1 ; Set endstop configuration (X and Y endstops at low end, U endstop at high end, active high, no Z endstop)
```

# Homing files

Your new axes need their own homing files. For an IDEX machine, you need to add homeu.g. File homex.g will home to the low end and homeu.g will home to the high end.

Your homeall.g file should home the new axes as well as X, Y, Z. On a Cartesian printer you will normally be able to home your additional axes simultaneously with X and Y.

If you home Z using a Z probe, then that probe should be mounted on the X carriage and you should ensure that the X carriage is over the bed when you probe using G30. One way of doing this is to deselect all tools prior to probing. Another is to use G1 H2 commands on the G1 commands, which disables axis mapping. You should also park the U carriage out of the way of where you want to probe.

Here are some sample files. (Use G1 S# commands instead of G1 H# commands in the following examples if you are using RRF 2.01 and earlier.)

homex.g:

```
; Home X at the low end of the axis
G91
G1 Z4 F200 H2
G1 X-240 F3000 H1
G1 X4 F600 H2
G1 X-10 H1
G1 Z-4 F200
G90
```

homeu.g:

```
; Home U at the high end of the axis
G91
G1 Z4 F200 H2
G1 U240 F3000 H1
G1 U-4 F600 H2
G1 U10 H1
G1 Z-4 F200
G90
```

homez.g:

```
; Home Z using the Z probe mounted on the X carriage
G91
G1 Z4 F200 H2 ; raise head 4mm to keep it clear of the bed
G1 U200 F2000 ; make sure the U carriage is out of the way
G90
G1 X100 Y100 F2000 H2 ; move to bed centre for probing
G30 ; probe the bed and set Z height
```

homeall.g:

```
; Home X, Y, U simultaneously 
G91
G1 Z4 F200 H2 ; raise head 4mm to keep it clear of the bed
G1 X-240 Y-240 U240 F3000 H1 ; coarse home X, Y and U
G1 X4 Y4 U-4 F600 H2 ; move 4mm away from the homing switches
G1 X-10 Y-10 U10 H1     ; fine home X, Y and U
G90
; Now home Z using the Z probe
G1 X100 Y100 F2000 H2 ; move to bed centre for probing
G30 ; probe the bed and set Z height
```

# Bed probing file

In normal setups, bed probing is always done using the X carriage - so the X carriage is the one that carries your Z probe. The X coordinates in G30 commands are never mapped.

You should take the following precautions in the bed.g file for an IDEX machine:

* Get the U carriage out of the way of the X carriage at the start, either by homing it or by moving it to near to its home position
* If you have any G1 commands in the bed.g file, use the S2 modifier on them so that any X coordinates in those commands do not get mapped.

# Configuring tools

## Creating a tool that uses just one carriage

A tool that uses the X carriage, and for example extruder drive 0, heater 1 and fan 1 can be configured as normal:

```
M563 P0 D0 H1 F1 ; tool 0 uses extruder 0 and heater 1 and fan 1
G10 P0 X0 Y0 Z0 S0 R0 ; set tool 0 offsets and temperatures
```

A tool that uses the U carriage for X movement need an X3 parameter in the M563 command:

```
M563 P1 D1 H2 X3 F2 ; tool 1 uses extruder 1 and heater 2 and maps X to U, use fan 2 as the print cooling fan
G10 P1 Y0 U0 Z0 S0 R0 ; set tool 1 offsets and temperatures
```

This tells the firmware that the X axis should be mapped to axis #3 which is the U axis. Note that the "X offset" of tool 1 is actually a U offset, because tool offsets are applied after axis mapping. Leave all offsets at 0 to start with.

The third fan channel is configured to this tool. This is because many slicers will send M106 Sxxx without any fan number. When this tool is selected any M106 commands will refer to fan 2.

You will need to adjust the X, Y and Z offsets of tool 1 so that both tools print at the same locations along the Y axis and at the same heights. To get both tools printing at the same place along the X axis, either adjust the homing positions of the U axes via the M208 commands, or adjust the G10 P1 U offset. See the Calibration section below.

## Creating a tool that uses more than one carriage to print multiple copies of an object

If you want to print using two or more carriages simultaneously, you will need to adjust the carriages so as to have equal nozzle heights, i.e. zero Z offset in the G10 commands. Otherwise you will only be able to get one of the nozzles at the perfect first layer height. You will also need to have a flat and level bed in at least the X direction, because bed compensation can only adjust the nozzle height for a single X position.

To print with both carriages at once you need to map X movement to both X and U, like this:

```
M563 P2 D0:1 H1:2 X0:3 F1:1 ; tool 2 uses both extruders and hot end heaters, maps X to both X and U, and uses both print cooling fans
G10 P2 X50 Y0 U-50 S0 R0    ; set tool offsets and temperatures for tool 2
```

Note that axes are mapped in the order XYZUVWABC, where X=0, Y=1, Z=2, U=3 etc, not by driver number, so X0:3 means 'map axis 0 (X) and 3 (U) to X'.

In this example we want to print one object using the X carriage and another identical object 100mm further along using the U carriage. So we specify X and U offsets of 50mm in opposite directions. However, tool offsets are given as the offset of the nozzle relative to the print head reference point, so the signs are opposite to what you might expect because tool offsets are subtracted from the required printing locations during printing. So we need to use an X offset of +50mm to force the X carriage to be 50mm lower along the X axis, and a U offset of -50mm to force the U carriage to print 50mm higher along the X axis.

To get the firmware to extrude on both extruders equally, we use the M567 and M568 commands, which were originally provided to support mixing hot ends:

```
M567 P2 E1:1 ; set mix ratio 100% on both extruders
M568 P2 S1 ; turn on mixing for tool 2
```

## Sample IDEX tool configuration

```
; Tool configuration section of config.g

; Create a tool that uses the low end (X) carriage
M563 P0 D0 H1 F0            ; tool 0 uses extruder 0, heater 1 and fan 0
G10 P0 X0 Y0 Z0 S0 R0       ; set tool 0 offsets and temperatures

; Create a tool that uses the high end (U) carriage
M563 P1 D1 H2 X3 F2         ; tool 1 uses extruder 1, heater 2, fan 2, and maps X to U
G10 P1 Y0 U0 Z0 S0 R0       ; set tool 1 offsets and temperatures

; Create a tool that prints 2 copies of the object using both carriages
M563 P2 D0:1 H1:2 X0:3 F0:2 ; tool 2 uses both extruders, hot end heaters and fans, and maps X to both X and U
G10 P2 X50 Y0 U-50 S0 R0 ; set tool offsets and temperatures
M567 P2 E1:1 ; set mix ratio 100% on both extruders
```

You can also create a 'mirror' mode, by using the same tool settings as the '2 copies', but with different X offsets, and setting M579 U-1 in the tool's tpost.g macro, and M579 U1 in the tfree.g tool change macros (see below). M579 changes the direction of the axis. See [this thread on the forum for details](https://forum.duet3d.com/topic/15066/u-axis-don-t-print).

# Setting up the tool change files

To do dual-colour or dual-material prints, you need to make sure that the unused carriage is always parked out of the way. Start by including a G28 or a G28 X U command in your slicer start gcode, to ensure that both tools are homed initially. Then use the tfree#.g files (where # is the tool number) to park a tool when you stop using it. To park a tool you could just home it, but you may prefer to move it to 1mm or so short of the homing position so that you can use a fast travel move to get there. To control oozing, you can use a lower standby temperature than active temperature, and retract a little filament.

Here are some sample tool change files for tools 0, 1 and 2 of an IDEX machine configured as above.

NOTE: these examples show how to do retraction and unretraction using the tool change files however depending on your results you may prefer to remove the retraction and unretraction and configure them in the slicer instead. Slicers will unretract just before starting to print, after moving the tool to the start point, which gives better ooze performance.

tfree0.g:

```
M83              ; relative extruder movement
G1 E-2 F3600     ; retract 2mm
M106 S0          ; turn off our print cooling fan
G91              ; relative axis movement
G1 Z3 F500       ; up 3mm
G90              ; absolute axis movement
G1 H2 X-48 F6000 ; park the X carriage at -48mm
```

tfree1.g:

```
M83              ; relative extruder movement
G1 E-2 F3600     ; retract 2mm
M106 S0          ; turn off our print cooling fan
G91              ; relative axis movement
G1 Z3 F500       ; up 3mm
G90              ; absolute axis movement
G1 H2 U248 F6000 ; park the U carriage at +248mm
```

tfree2.g:

```
M83          ; relative extruder movement
G1 E-2 F3600 ; retract 2mm
M106 S0      ; turn off our print cooling fan
G91          ; relative axis movement
G1 Z3 F500   ; up 3mm
G90          ; absolute axis movement
G28 X U      ; home the X and U carriages
```

tpost0.g:

```
M106 R2     ; restore print cooling fan speed
M116 P0     ; wait for tool 0 heaters to reach operating temperature
M83         ; relative extruder movement
G1 E2 F3600 ; extrude 2mm
```

tpost1.g:

```
M106 R2     ; restore print cooling fan speed
M116 P1     ; wait for tool 1 heaters to reach operating temperature
M83         ; relative extruder movement
G1 E2 F3600 ; extrude 2mm
```

tpost2.g:

```
M106 R2      ; restore print cooling fan speed
M116 P2      ; wait for tool 2 heaters to reach operating temperature
M83          ; relative extruder movement
M567 P2 E1:1 ; set tool mix ratio
M568 P2 S1   ; turn on mixing
G1 E2 F3600  ; extrude 2mm from both extruders
```

# Pause and resume files

When you pause a print, either or both heads may be printing, so you should park both of them in your pause.g file. Use the G1 command with the S2 modifier to do this. Here is an example pause.g file:

```
M83                   ; relative extrusion
G1 E-2 F3600          ; retract 2mm
G91                   ; relative movement
G1 Z2 F500            ; raise head 2mm
G90                   ; absolute movement
G1 H2 X-48 U248 F6000 ; park both heads
```

Your resume.g file needs to restore the position and undo any retraction:

```
G1 R1 X0 Y0 Z2 F5000 ; move to 2mm above the resume location
G1 R1 X0 Y0 Z0 F500  ; move to the resume location
M83                  ; relative extrusion
G1 E2 F3600          ; undo the retraction
```

# Calibration

To calibrate the printer so that both extruders print at the same point follow these principles:

1. Get the X axis working properly first, especially that it is printing on a level bed.
1. Get the U axis Z height to be identical to the X axis if you want to print two copies of the same file at the same time. This is less critical if you just want to print 1 object with both tools. If they are not identical you need to determine the exact difference between the two and set the T1 Z offset appropriately as shown in configuring tools above.

With both tools at the same Z height, or the difference accounted for in the G10 P1 Zn.n command, the heads need to be aligned in X and Y. Start by getting them in the right ballpark (~1mm) in the following manner:

1. Mark a dot on the printbed near the center (does not have to be exact).
1. Move T0 on the X axis so it is as near as possible over the marked dot, make a note of the X and Y co-ordinates, move the X axis out the way.
1. Move T1 on the U axis so it is as near as possible over the marked dot, make a note of the U and Y co-ordinates.
1. Subtract the U co-ordinates from the X co-ordinates and you have the rough offsets for U and Y. For the U value, increase or decrease the value of M208 Uxxx by the U offset, so that when the U axis is homed and then travels back to the spot it is above it for the same X co-ordinate as the X axis. For the Y co-ordinate add it to the G10 P1 Yx.x tool definition.
1. With the new values in effect (either reboot after updating config.g or enter them into the gcode console to test them temporarily) repeat the testing above the dot, both axis should now be above the dot with the same X and Y co-ordinate.

Now the tools are roughly aligned, fine alignment can be conducted in a number of ways. One method is to print a dual extruder print with lines, or triangles of varying spacing and then work out where they align:

![configure_idex_01.jpg](/manual/configuration/configure_idex_01.jpg =300x)

the two parts of this file are printed with the two tools and the line that most closely aligned is selected. If the line 3 left from the center is the closes, and the variation is 0.2mm then the U axis is 0.6mm too far to the left.

The process is repeated with the model rotated 90 degrees for the Y axis.

![configure_idex_02.jpg](/manual/configuration/configure_idex_02.jpg =300x)

The STL files and OpenScad document are available on the [Think3dPrint3d Github](https://github.com/T3P3/BigBox_Mods/tree/master/calibration%20objects) as IDEXcalibration_reg.stl and IDEXcalibration_var.stl. Print the 'reg' one with the X axis and the 'var' one with the U axis.

# Miscellaneous

* If you use a [Z probe](/User_manual/Connecting_hardware/Z_probe_connecting), put it on the X carriage so that the XY coordinates in bed.g correspond to positions of the X carriage.
* The motor for the parked carriage will be run at normal operating current, because motor idle detection is system-wide at present. Idle detection on a per-motor basis will be implemented in a future firmware release.