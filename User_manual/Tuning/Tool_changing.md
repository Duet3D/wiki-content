---
title: Multiple tools and Tool change macros
description: 
published: true
date: 2021-12-03T16:26:59.585Z
tags: 
editor: markdown
dateCreated: 2021-12-03T16:26:56.963Z
---

# Tool Preheat

When starting up you can arrange for both tools to start heating as shown in the following example

```
G10 P0 S200 R150 ; Set tool 0 active and standby temperatures
G10 P1 S200 R150 ; Set tool 1 active and standby temperatures
T1 P0            ; Select tool 1 to turn it on but don't execute tool change scripts
T0               ; Select tool 0
M116             ; Wait for all temperatures to be reached
```

This will turn both heaters on, heating tool 0 heater to 200C and tool 1 heater to 150C.

# Tool Change Macros

RepRapFirmware handles multiple extruders through a tool definition mechanism. If you have multiple nozzles, you will normally define one tool for each nozzle. There is nothing to stop you defining several tools that use the same nozzle if you want, or a single tool that uses more than one nozzle.

Only one tool is active at a time. To switch between tools, use the T command. Tools are conventionally numbered from zero, so in a dual nozzle printer the tools would normally be T0 and T1. The current PanelDue firmware expects the tools to be numbered T0, T1 and so on; but the web interface doesn't mind if the tools are not numbered consecutively.

When the firmware receives a T command and the requested tool number is not already active, it goes through the following sequence:

1. If another tool is already selected and all axes have been homed, run macro tfree#.g where # is the number of that tool
1. If another tool is already selected, deselect it and set its heaters to their standby temperatures (as defined by the R parameter in the most recent G10 command for that tool)
1. If all axes have been homed, run macro tpre#.g where # is the number of the new tool
1. Set the new tool to its operating temperatures specified by the S parameter in the most recent G10 command for that tool
1. If all axes have been homed, run macro tpost#.g where # is the number of the new tool. Typically this file would contain at least a M116 command to wait for its temperatures to stabilise.
1. Apply any X, Y, Z offset for the new tool specified by G10
1. Use the new tool.

The tool change macro files are not run if the printer has not been homed since power up. This allows you to put a T0 command at the end of config.g, which is convenient if you have only one tool.

Here is a sample tfree0.g file:

```
; Put G/M Codes in here to run when Tool 0 is freed
M83 ; relative extruder mode
G1 E-4 F2500 ; retract 4mm
```

Here is a sample tpre0.g file:

```
; Put G/M Codes in here to run when Tool 0 is about to be activated
G1 X0 Y0 F6000 ; move the head away from the print
```

Here is a sample tpost0.g file

```
; Put G/M Codes in here to run after Tool 0 is activated
M116 P0 ; wait for tool 0 only to reach operating temperature
M83 ; relative extruder mode
G1 E4 F2500 ; undo retraction
```

You may not need to include the commands to retract and un-retract the filament if your slicer does that for you. As when any macro file is run, the absolute/relative axis and extruder movement states are restored to their original values when the macro file completes.

The tfree1.g, tpre1.g and tpost1.g files would be similar, except that tpost1.g would use command M116 P1. The purpose of the P parameter on the M116 command is to allow you to wait for the new tool to heat up to active temperature without also waiting for the previously-selected tool to cool down to standby temperature.