---
title: Macros
description: A work in progress page for useful gcode macros.
published: true
date: 2025-01-17T15:01:28.015Z
tags: 
editor: markdown
dateCreated: 2021-12-03T14:50:28.135Z
---

# Introduction

RepRapFirmware allows for gcode sequences, called macros, to be stored on the SD card. these macros can then be called by using [M98](/User_manual/Reference/Gcodes/M98), for example:

`M98 P"mymacro.g"`

Macros can be called from any source of gcode: the console, from a gcode printing off the SD card, from the PanelDue and from another user or system macro. [Duet Web Control](/User_manual/Reference/Duet_Web_Control_Manual) makes it easy to upload, edit and create new macros.

Macros also allow for naming gcodes with easier to remember names. For example you can create a Macro for "Allow Cold Extrude" to call [M302 P1](/User_manual/Reference/Gcodes/M302).

Note: if you try to execute a G- or M-command that RRF does not implement, it will execute a system macro of that name if it exists. For example, if you send G40 then it will execute /sys/G40.g if it exists; and if you send M48 then it will execute /sys/M48.g if it exists.

The macro folder can divided into sub directories to more easily organise your macros by type.

![macros_01.png](/manual/configuration/macros_01.png =400x)

For more information see [Setting up macro files for common tasks](/User_manual/Tuning/Macros_tasks).

System files are also effectively macros, for example [homing files](/User_manual/Machine_configuration/Configuration_cartesian#homing-files) are a sequence of gcodes to run the specific homing sequence for a specific printer type. See here for information on using [tool change macros](/User_manual/Tuning/Tool_changing).

The [RepRap wiki](https://reprap.org/wiki/RepRap_Firmware_macros#Useful_user_macros) also contains a page of useful macros.

## RepRapFirmware configuration tool

[RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/) will produce appropriate system macros for most basic printer types. These are generally a good start point.

## Repository

There are a couple of github repository for macros; see [here](https://github.com/Duet3D/GcodeMacros) or [here](https://github.com/Duet3D/RRF-machine-config-files/tree/master/DBotCoreXY/x0r-wifi-RRF3/macros). They are a work in progress. Please submit pull requests or comment on the forum if you have example macros to share.

# Printer management

## Start and end gcodes

Macros can be used to complement printer "start gcode" and "end gcode", for example if you have different printers, with different start and ends sequences, but you want to use the same sliced gcode, you could use:
```
M98 P"startprint.g"
```

## Filament management

see [Filaments](/User_manual/Reference/DWC_filaments), specific load and unload macros can be defined by filament type.

# Calibration and Tuning

Calibration and tuning printer settings often requires repeating the same steps repeatedly, changing one variable at a time. Macros can help automate this.

As set of simple short cuts for tuning various settings like acceleration, jerk, retraction, pressure advance, etc, see [this forum thread](https://forum.duet3d.com/topic/6181/) (from forum user [Phaedrux](https://forum.duet3d.com/user/phaedrux)).

## Example: Measuring the Z axis for a Z max endstop and saving the result

Here is an example macro that uses the M500 function to measure and save the length of the Z axis using the Z max endstop.

```
; 0:/macros/Bed Leveling/0_Set Zmax height.g
; Automates measuring the Zmax height
;
M291 P"This will set Z0 and calibrate Zmax height" R"Proceed?" S3
M291 P"Heating and Homing all axis. Remove filament." T5
M190 S60 ; Set bed temp to 60 and wait
M109 S215 ; Set nozzle temp to 215 and wait
G28 ; Home all
G90 ; absolute positioning
G1 X155 Y140 F6000 ; move probe to center
M291 P"Print head will now raise until the Z Max endstop is triggered" S3
M913 Z50 ; drop the motor current of the z axis motor
G1 H3 Z300 F400 ; Z up until triggered. set M208 Z max
M500 ; save m208 value for z axis to config override
M291 P"ZMax homing will now be tested, starting with homing Zmin" S3
M291 P"Homing to Zmin" T5
G1 Z100 F6000 ; move z down a bit quickly
M558 F600 A1 ; set single fast probe settings
G30 ; probe the bed at center
M558 F120 A10 ; set slower multi probe
G30 ; probe the bed at center
M291 P"Ready to test Zmax homing?" R"Proceed?" S3
M291 P"Homing to Zmax" T5
G28 Z ; home to zmax
M291 P"Verify that Z max height is correct by moving to Z0" S3
G1 Z0 F400 ; move the Z axis to Z0
M291 "The nozzle should now be just touching the bed." S3
M913 Z100 ; return z axis motor current to normal
M140 S0 ; turn bed heater off
M104 S0 ; turn hotend heater off
```

# Other Macros

## Sounds

It is possible to use macros to make the PanelDue beeper play simple tunes by using the [M300 (play beep)](/User_manual/Reference/Gcodes/M300) gcode and the [G4 (dwell)](/User_manual/Reference/Gcodes/G4) gcodes. See examples in [this forum thread](https://forum.duet3d.com/topic/15722/).

# System Macros

RepRapFirmware uses gcode files to script many user configurable printer actions. The use of these system macros and opposed to hard coding the functionality is one the reasons RepRapFirmware is so flexible. It is recommended to start with a configuration from the [RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/) or a known good configuration as a start point.

*Note, all these macros are in /sys/ on the SD card unless otherwise stated*

## Homing

Homing macros depend on the type of printer you are using. See:

* [Cartesian printer homing](/User_manual/Machine_configuration/Configuration_cartesian).
* [Delta printer homing](/User_manual/Machine_configuration/Configuration_linear_delta).
* [Peculiarities of CoreXY homing](/User_manual/Machine_configuration/Configuration_coreXY)
* [IDEX homing](/User_manual/Machine_configuration/Configuration_IDEX)
<!--* [SCARA homing **UPDATE LINK**]()
* [Polar homing **UPDATE LINK**]()-->

## Bed probing

### Deploying and retracting the probe

If you have a z probe that must be deployed before probing and retracted afterwards use **deployprobe.g** and **retractprobe.g** to call the appropriate deploy and retract commands. These are called by [M401 - Deploy z probe](/User_manual/Reference/Gcodes/M401), [M402 - Retract z probe](/User_manual/Reference/Gcodes/M402).

If you are running RepRapFirmware 3.1 or later and you have more than one Z probe, then use **deployprobe0.g** and **retractprobe0.g** for probe 0, **deployprobe1.g** and **retractprobe1.g** for probe 1 and so on.

***Note: If you do not have a Z probe that needs to deploy or retract you must not have these files in the /sys/ directory***

### Delta calibration, or true bed levelling using multiple Z motors

**bed.g** is run in response to the G32 command. See [delta calibration](/User_manual/Tuning/Delta_calibration) and [Bed levelling using multiple independent Z motors](/User_manual/Connecting_hardware/Z_probe_auto_levelling).

### Mesh bed compensation

In RepRapFirmware 3.2 and later, **mesh.g** is run in response to a [G29](/User_manual/Reference/Gcodes/G29) command with no parameters. If file mesh.g is not found then it behaves like G29 S0 instead.

## Errors and Events

With RRF 3.4, a new 'Events' system allows for more granular control of errors. See the [Events](/User_manual/RepRapFirmware/Events){target=_blank} page for more details. The following macros can be created for control of specific events.
* **heater-fault.g** 
* **filament-error.g** 
* **driver-error.g** 
* **driver-stall.g** 
* **driver-warning.g** 
* **expansion-reconnect.g**
* **expansion-timeout.g**

## Tool change

There are normally three specified tool change macros (any of which can contain no commands if desired) that execute in this order:

* Actions to do with the old tool before it is released - macro name: **tfreeN.g** where N is the tool number;
* Actions to do with the new tool before it is selected - macro name: **tpreN.g** where N is the tool number;
* Actions to do with the new tool after it is selected - macro name: **tpostN.g** where N is the tool number.

See [Selecting a tool](/User_manual/Reference/Gcodes/T) and [Multiple tools and tool change macros](/User_manual/Tuning/Tool_changing) for more details.

## Start, pause, stop and power fail

* **start.g** is run when you start a print from SD card. Runs immediately before any slicer created gcode.
* **pause.g** is run when you pause a print.
* **resume.g** is run when you resume a print.
* **cancel.g** is optional, but if present is run when you cancel a paused print.
* **stop.g** is optional, but if present is run when a print finishes normally with an M0 command at the end of the print job. (from RRF 3.5b1, When a print file completes normally then file stop.g is run automatically even if the print file did not end with a M0 command)
* **sleep.g** If [M1](/User_manual/Reference/Gcodes/M1)) is issued the firmware finishes any moves left in its buffer, then shuts down, the macro file sleep.g is run before all heaters and drives are turned off.
* **filament-change.g** is run if a [M600](/User_manual/Reference/Gcodes/M600) command is encountered when printing from SD card. if that file is not found then **pause.g** is run instead.

The Duet can be [configured to detect a power failure](/User_manual/Tuning/Resume) and store the current state once the power returns if a user wants to resume the print **resurrect-prologue.g** is run that would contain user commands to home the printer etc before resuming.

## Triggers

[M581](/User_manual/Reference/Gcodes/M581) can be used to setup external triggers (switches etc) that run the actions defined in **sys/trigger#.g** macros. These can be used for things like Emergency Stop buttons, door switches, using endstop switches as limit switches, turning an external PSU on and off, etc.

See [User manual: Connecting an Emergency Stop](https://docs.duet3d.com/en/User_manual/Connecting_hardware/IO_E_stop) and [User manual: Using triggers to control the Duet](/User_manual/Tuning/Triggers) for examples of usage.

## daemon.g

From RRF3.1.0 and later the file /sys/daemon.g can be used to execute regular tasks. The firmware looks for the file, if the file exists it executes it and once the end of file is reached it waits. If the file is not found it waits and then looks for it again. In RRF 3.3 The wait time was increased form 1 second to 10 seconds. If you want a shorter update time then put a while loop inside the daemon.g with `G4 S1` in it for 1 second repeats.

This can be used in combination with [GCode Meta Commands](/User_manual/Reference/Gcode_meta_commands) to check the object model to look for a particular condition, and then take an action.

Caution must be taken not to start a loop that takes a long time to complete, without having a `G4 P500` or similar command to hand control back to the main process every half a second or so.

You can't directly edit a daemon.g file that is running on a Duet. To edit, right click on daemon.g in the SD card /sys folder and rename it to something else. This will stop the daemon.g file from running, and allow editing. Once the file has been edited, rename it back to daemon.g.

## runonce.g

File runonce.g is supported from RRF3.1.0 and later. If this file is present at startup, it is run after running config.g and activating the network, and then deleted.