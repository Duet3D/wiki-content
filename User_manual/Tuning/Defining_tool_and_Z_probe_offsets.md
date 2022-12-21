---
title: Defining tool and Z-probe offsets
description: 
published: true
date: 2022-12-21T16:43:09.601Z
tags: 
editor: markdown
dateCreated: 2022-12-21T16:43:09.601Z
---

# Defining tool and Z-probe offsets
In RepRapFirmware all tool offsets, Z-probe offsets and machine limits are relative to the Head Reference Point (hereafter referred to as the HRP). The HRP must be somewhere on the print carriage so that it moves with the nozzle and (if present) the Z-probe, but other than that it can be wherever you want. Therefore, before you specify any tool or Z-probe offsets r the machine limits, you should define where the HRP is on your machine.

## Typical choices for the HRP:

* On a 3D printer with a single nozzle, the tip of the nozzle is normally used as the HRP
* On a 3D printer with a single carriage having more than one nozzle, you might choose the tip of one nozzle to be the HRP; or you might choose a point midway between the two nozzles to be the HRP.
* On a tool changing 3D printer or CNC machine, you would normally choose some part of the tool pickup head to be the HRP. If the tool pickup head incorporates a Z probe (e.g. as on the E3D Tool Changer) then you might choose the position of the Z probe wnen it triggers to be the HRP.
* On an IDEX printer, the HRP includes the coordinate of the second X axis (typically called the U axis). You might choose each HRP to be the tip of the nozzle on the first carriage.  The nozzle on the second carriage would ideally have the same Y and Z coordinates, but in practice may have a slight offset. To get both nozzles to print at the same X location, you would typically set the U axis limits to get both nozzles printing approximately at the same place (i.e for any coordinate nnn that is reachable by both nozzles, Unnn and Xnnn have the same X position) and then add a small U offset to fine-tune the position of the second nozzle.

## Once you have defined the HRP

* Set the M208 axis limits to be the values that you want the HRP to be limited to, and (for axes using endstop sensors) give you the desired values when you home those axes. RRF will constrain the location of the HRP (not the nozzle) to remain within those limits.
* If the carriage or tool pickup has a Z probe, set the Z probe offsets in the G31 command to be the offsets of the Z probe relative to the HRP; except that the Z vaue in the G31 command is the trigger height, which can be thought of as the negative of the Z probe offset.
* In your G10 tool offset setting commands (these G10 commands have axis letter parameters and a P parameter but no L parameter), specify the offsets of the nozzle or cutting head relative to the HRP.
