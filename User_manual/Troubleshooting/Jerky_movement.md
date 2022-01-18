---
title: Jerky movement when printing
description: If your printer moves jerkily during printing, here are the most common causes.
published: true
date: 2022-01-18T16:18:19.944Z
tags: 
editor: markdown
dateCreated: 2021-12-05T23:12:33.044Z
---

If your printer moves jerkily during printing, here are the most common causes: 
 
1. Printing over USB with incorrect flow control. Preferably, print from the internal SD card of the Duet instead. If you must print via USB, set the host program on your PC so that it does not wait for an OK response after sending each command. To do this in Repetier Host, disable "ping-pong". To do this in Simplify3d, set flow control to "Hardware".
1. Printing with debugging enabled. If debugging is enabled with [M111](/User_manual/Reference/Gcodes/M111), RepRapFirmware sends large quantities of debugging data to the USB port, and waits a reasonable time to allow the host program to pick them up. This can greatly slow down the processing of GCode commands. Note that Repetier Host enables debug automatically in its default configuration, because Repetier firmware appropriates the M111 debugging command for its own use (see [RepRap GCode dictionary here](https://reprap.org/wiki/G-code#M111_in_Repetier)).
1. Microstepping set too high. If you set microstepping too high, the processor may not be able to generate step pulses fast enough. If this happens then high speed moves may be jerky. To check whether you have microstepping set in a reasonable range, execute some long high speed moves, then run [M122](/User_manual/Reference/Gcodes/M122) and look at the MaxReps figure in the report. This value should be kept below 100 and preferably below 50.
1. XY jerk ([M566](/User_manual/Reference/Gcodes/M566)) set too low. If the jerky movements happen while printing circles, this is often a sign that the XY jerk has been set too low. Jerk in this context means instantaneous commanded speed change. The STL models that are input to slicing programs approximate curved surfaces by many flat surfaces. The sudden change in direction where the flat surfaces meet demands an instantaneous speed change to avoid having to bring the print head to a stop at the junction.
1. Inconsistent extrusion rate in the GCode. Some slicers (notably Simplify3D) produce GCode with inconsistent extrusion rate in adjacent segments, requiring the extruder to accelerate and decelerate at the junctions between segments. Increasing extruder jerk may mitigate this; or choose a better slicer.

