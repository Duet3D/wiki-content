---
title: Test homing behaviour
description: Verify that your printer touches each endstop when homing.
published: true
date: 2024-08-27T16:25:05.326Z
tags: 
editor: markdown
dateCreated: 2021-10-26T10:47:29.043Z
---

# Prerequisites

* You have tested the endstops with the procedure [Testing Endstop swithces](/User_manual/Connecting_hardware/Sensors_endstops#test-endstop-switches)
* If you are using the Z probe to home Z, you have tested and calibrated the Z probe
* For Delta Printers, ensure a "homedelta.g" file exists.  This should have been built by the reprap online configuration tool and included in the zip package.  If the file is not present, see the 'Homing file' section of [Configuring RepRapFirmware for a Linear Delta printer](/User_manual/Machine_configuration/Configuration_linear_delta#homing-file)
* Be ready to power off, if the printer is about to do something like "crash" the nozzle into the bed.  Position yourself with a finger on the power switch, or similar. 
* If you are at all in doubt, temporarily reduce the motor currents before you test homing. You can do this either by editing the M906 command in config.g, or by sending the M913 command from the console. For example if you send M913 X50 Y50 Z50 then the motors will run at 50% current until you restart the Duet or send another M913 command.

# Issuing the home command(s)

All of the following are equivalent:

* In the Web Interface, click the Home All button on the Machine Control page to home everything, or (if provided) Home X, Home Y or Home Z to home a single axis.
* In the gcode console, issue command G28 to home everything, or G28 X to home just X, and so on.
* On a PanelDue, press the Home icon on the Control page, or (if provided) Home X, Home Y and so on.

On a Cartesian or CoreXY printer, test X homing first, then Y homing, then Z homing. When they are all working independently, test homing all axes together. On a delta printer you only have the option to home all towers at once,

# What to look for as the printer begins to move

On a Cartesian or CoreXY printer, the axis being homed should begin to move at a medium speed in the direction that will eventually trip the endstop. It is normal for an axis to trigger the stop, back off, and move more slowly to trigger it again. When you do Home All, the X and Y axes will usually be homed simultaneously, followed by Z.

A delta printer will home all three towers at the same time and each should stop when its endstop switch is triggered.  Once all three towers have triggered during the medium speed move, all three will back off, and move more slowly to trigger again. Then they will lower themselves a small amount.

# What to watch for, and stop, via power off

* If the printer is moving AWAY from the endstop on any axis.  Power off, power back on and reverse that axis via the M569 commands in config.g
* If the printer hits an endstop and "keeps going".  Power off, and go back to the procedure to test endstops. 
* If the printer jams or makes belt/screw grinding noises.  Power off and resolve the issue. 
* If the printer is about to, or does, hit the bed with the nozzle.  For Cartesians, this generally means the Z endstop needs mechanical adjustment, or (if you are homing using the Z probe) the Z probe is not configured or not working.  For Deltas, this means the Z is too large (M665 H parameter, in config.g or config_override.g). 
* If the printer does ANYTHING you "don't like".  Be very ready to hit power off... it doesn't cost you anything but a few seconds of time.