---
title: Test and calibrate a Z probe
description: 
published: true
date: 2025-02-24T15:42:20.850Z
tags: 
editor: markdown
dateCreated: 2021-10-28T11:29:02.047Z
---

# Static test of the Z probe

## Static test using the web interface

* Apply power to the printer.
* Connect to the printer from a browser.
* If the probe needs to be deployed before use (e.g. BLTouch), test the deploy and retract functions, by sending `M401` to deploy the probe and `M402` to retract it.
* With the Z probe deployed (if applicable) but not close enough to the bed to trigger it, check that the Z probe reading in Duet Web Control is zero or close to zero.
* If your Z probe is of a type that produces a continuous output when triggered (for example IR, inductive, capacitive and switch-type probes), hold a surface below the Z probe to cause it to trigger (or jog the nozzle towards the bed until it is close enough to trigger). Check that the Z probe reading in DWC is correct (about 537 for the Mini Differential IR probe, and about 1000 for most other types).
* If your Z probe produces a short pulse when it triggers (e.g. Smart Effector, Precision Piezo, FSRs with John SL board, BLTouch) then the pulse will probably be too short for you to see. Proceed to the dynamic test.

## Static test using Panel Due or a USB connection

As above, but either monitor the Z probe reading in PanelDue if you have one, or send `G31` via USB every time you want to read it.

# Dynamic test

* Apply power to the printer.
* Connect to the printer from a browser or via USB.
* If your printer is Cartesian or CoreXY, home X and Y. If it is a Delta, home all.
* Position the print head well above the bed.
* Send command G30 to do a single Z probe. This will deploy the probe (if applicable) and start the head descending or the bed rising.
* Hold a suitable surface below the print head to trigger the Z probe. The probe should trigger and the Z movement should stop.
* If the Z movement doesn't stop, turn off power to the printer before the head crashes into the bed. It is also a good idea to reduce motor currents in case of a crash.

# Calibrate the Z probe trigger height

1. Make sure the  dynamic test is successful (Z probe stops when it senses the bed) before doing this.
1. Cancel any currently active mesh compensation with [`M561`](/User_manual/Reference/Gcodes/M561)
1. Use the X and Y jog buttons to position the nozzle over the centre of the bed
1. Jog the nozzle down until it is just touching the bed or just gripping a sheet of paper. If the firmware doesn't let you jog it down far enough, send `M564 S0` to disable axis limits.
1. Once you have the nozzle touching the bed, send command `G92 Z0` to  tell the firmware that the head is at Z=0
1. Jog the head up by 5 to 10mm
1. Send command `G30 S-1`. The nozzle will descend or the bed rise until the probe triggers and the Z height at which the probe stopped will be reported in the console. If you are using a nozzle-contact Z probe, the trigger height will be slightly negative. For any other type of Z probe where the probe triggers before the nozzle contacts the bed, it will be positive.
1. Repeat  from step 5 two or three times to make sure that the  trigger height is consistent.
1. In Duet Web Control, go to Settings -> System Editor and edit the config.g file. Set the Z parameter in the `G31` command to the trigger height that was reported. Save the file.
1. Open config-override.g and check that there are no `G31` commands in it. If you find any, delete those lines and save the file.
1. To apply the new trigger height, restart the Duet by sending `M999` or pressing Emergency Stop.

## Fine tuning the trigger height

After you've done the procedure above, you can fine tune your G31 Z value to get a good first layer. During a test print, watch the first layer go down and adjust the Z position using the baby stepping function. If you have to move the nozzle closer to the bed, increase the G31 Z value by the amount of baby stepping used. If you have to move the nozzle farther away from the bed, decrease the G31 Z value by the amount of baby stepping used.

# Measuring Probe X Y Offset

* The firmware needs to know the position of the probe in relation to the tool. In most cases, the nozzle tip.
* If your probe uses the nozzle tip, the X Y offset would be G31 X0 Y0.
* If you have access to the CAD files for your probe mount you may already know the probe X Y offset, or it may have been provided to you with the parts.
* Otherwise, you will need to measure the distance between probe and nozzle.

### Here is an easy way to measure the offset:

1. Tape down a piece of paper onto the bed.
1. Bring the nozzle down to the bed surface and lower it until the nozzle starts to depress into the paper, making a small impression. 
1. Mark the impression with a marker tip to make it more visible. 
1. Now jog X and Y until the probe is directly over the spot where the nozzle was. 
1. Take the amount you jogged as your X and Y offset to use in G31.
1. When the probe is to the left of the nozzle the X value is negative.
1. When the probe is in front of nozzle the Y value is negative.
1. To the right and behind the values are positive.

**Note** that the offsets will follow the right hand coordinate system, meaning that X movement to the left of the nozzle will be negative, and right of the nozzle will be positive. And Y movement behind the nozzle will be positive, and movement in front of the nozzle will be negative.

# More information on Z Probes

* Help with [Choosing a Z probe](/User_manual/Connecting_hardware/Z_probe_choosing)
* Information on [Connecting a Z probe](/User_manual/Connecting_hardware/Z_probe_connecting)