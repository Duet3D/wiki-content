---
title: Multiple motion systems
description: This page documents the support for multiple motion systems provided in RepRapFirmware 3.5 on Duet 3 boards.
published: true
date: 2023-04-11T14:51:28.340Z
tags: 
editor: markdown
dateCreated: 2022-03-22T10:08:15.620Z
---

# Overview

RepRapFirmware 3.5 provides experimental support for multiple motion systems that move asynchronously with respect to each other. Each motion system has its own current tool. A single GCode job file or input stream controls all the motion systems. Additional commands in the GCode stream specify which motion system each command is destined for. Some commands (in particular M400) cause motion to be synchronised, i.e. when the command is reached the system waits for all commands for any motion system prior to that command to complete before continuing.

The primary application is to speed up printing by having the printer work on separate parts of a large print concurrently, or to print two different objects at the same time.

# Prerequisites

-   Due to memory limitations, RRF on Duet 2 series boards does not support this feature. All Duet 3 main boards support it, however performance on Duet 3 Mini might be limiting.
-   Only two motion systems are currently supported. In the future we may increase this limit on Duet 3 MB6HC and MB6XD boards.
-   By default, the second motion queue is only three elements long. Use the M595 command to lengthen it, for example: M595 Q1 P60 will increase it to 60 elements (the same number as in the primary movement queue on MB6HC and MB6XD boards).
-   At any time, each motion system “owns” a set of physical axes and extruders. See below for details of ownership of axes and extruders.
-   For any two axes owned by different motion systems, those axes may not share a motor. For example, on a CoreXY machine the X and Y axes cannot be owned by different motion systems.

# Selecting  a motion queue

Use the [M596](https://docs.duet3d.com/en/User_manual/Reference/Gcodes#m596-select-movement-queue-number) command to specify which motion system any motion or tool operations in the following commands should be targeted at. The selection of target motion system remain in force until one of the following occurs:

-   Another M596 command is encountered
-   The current macro ends. The motion system selected immediately prior to calling the macro is selected.
-   A M121 (pop stack) command is executed. The motion system selected immediately prior to executing the corresponding M120 (push stack) command is selected.
-   The job is terminated by end-of file or by a M0, M1 or M2 command. Motion system 0 is selected.
-   M32 or M23 is executed to start a new file or to prepare to start a file. Motion system 0 is selected.

# Avoiding collisions

Use the [M597](https://docs.duet3d.com/en/User_manual/Reference/Gcodes#m597-collision-avoidance) command to set up a restriction on how close together the positions of two axes can get. For example, if you machine has separate Y and V gantries that move independently, use M597 to prevent them from coming too close together. Only one pair of axes can be restricted in this way.

# Synchronising the motion systems

There will be times when the two motion systems need to be synchronised. For example, when the two motion systems are used to drive the two print heads of a IDEXY machine independently, they will need to synchronise at each layer change because they share a common Z axis. Use the M598 command to cause the movement system that reaches the command first to wait for the other one to catch up.

# Command execution using multiple motion queues

The way that command streams that use multiple motion systems are processed depends on the source of the command stream.

For command streams from USB, async serial input (including PanelDue) and the DWC console, the commands are processed by a single GCode processor. This means that the ability of the system to execution motion of both systems concurrently will be limited, because when there is a long block of motion commands for one system, the GCode processor must wait until it has queued all those commands before it can process subsequent commands that are targeted at another motion system.

For command streams that originate from file, each motion system uses a separate GCode processor to process commands targeted at that motion system, skipping commands that are targeted at other motion systems. This is when there is a need to synchronise the motion systems at particular points in the file. For example, there may be blocks of motion commands for each motion system that can execute concurrently because each motion system operates within its own defined area, such that collisions will not occur. Subsequently it may be necessary to change these defined areas so that parts of the job that were not not accessible using the previous areas can be accessed. Or, where the motion systems share a common Z axis, it may be necessary for them to both complete the current layer of a 3D print before Z can be moved ready for the next layer.

The commands streams can be synchronised using a M400 command, or another command that locks motion and waits for queued motion to complete (except for G4, see later). Each GCode processor that reaches the M400 command will pause until all other processors have caught up. Note, the G4 command will only wait for motion in the *current* motion system to complete, therefore it cannot be used for perform synchronisation.

# Notes on particular commands when multiple GCode processors are used

In the following table, the “primary processor” means the GCode processor whose motion system is current.

All implemented G-commands are listed here. For M-commands not listed here, and for all G- and M-commands implemented by user-defined macros, the action is that only the primary processor executes the command.

Note: some configuration commands that are not normally used in job files wait for all motion to stop before they are executed except when they are used used in report-only mode. When multiple motion systems are active, as only the primary processor executes these commands, the firmware only waits for the primary motion system to stop. In the event that these commands are used in job files, this could result in unexpected behaviour. Examples of such commands are M92 and M350.

| **Command** | **Notes** | Implemented? |
| --- | --- | --- |
| G0 - G3 | The motion is executed by the primary processor using the axis mapping specified for the current tool of the current motion system. Other processors skip the command. | yes |
| G4  | The primary processor waits for all queued moves in its motion system to complete (but not moves in other motion systems) and then executes the delay. Other processors skip the command. | yes |
| G10 retract, G11 | Executed by the primary processor only. | yes |
| G10 other | Tool offset setting: executed by the primary processor only.  <br>Workplace coordinate origin setting: causes sync, then executed by the primary processor but will affect all processors using the same workplace number. | yes |
| G17 - G19 | Sync then executed by all processors. | yes |
| G20, G21 | Executed by all processors | yes |
| G28 | Sync then executed by all processors. Commands in the homing files are/are not executed according to the usual rules. | yes |
| G29 | Sync then if no S parameter is present, all GCode processors execute mesh.g and execution of the contents follows the usual rules; but if the S parameter is present, only the processor for the current motion system executes the command. | yes |
| G30 | Sync then executed by the primary processor only | yes |
| G31 | Executed by the primary processor only. | yes |
| G32 | Sync then all processors execute bed.g. Execution of the contents of bed.g follows the usual rules. | yes |
| G38.2 to G38.5 | Sync then executed by the primary processor only. | yes |
| G53 | Executed by the primary processor only. |     |
| G54 - G59.3 | Executed by all processors. | yes |
| G60 | Executed by primary processor only. | yes |
| G68, 69, 90, 91 | Executed by all processors. | yes |
| G92 | Executed by the primary processor only. | yes |
| M0, M1 | Sync then all processors execute stop.g or sleep.g if present, if not present then the primary processor turns all heaters off. Then motion system 0 is selected. | TODO |
| M17, 18, 84 | Sync then executed by the primary processor only | yes |
| M23 | Sync then motion system 0 is selected and the job file is prepared. | TODO |
| M24 | ??  | TODO |
| M26 | ??? Executed by primary processor only | TODO |
| M32 | Sync then motion system 0 is selected and execution of the job file commences. | TODO |
| M37 | With P parameter: as for M32 except that simulation mode is set. Without P parameter: processors synchronise, then the simulation mode is changed. Simulation mode it global i.e. it affects all motion systems. | TODO |
| M82, 83 | Executed by all processors | yes |
| M81 | Sync then executed by the primary processor only | yes |
| M98 | The current motion system number is included in the information stacked. All processors shall run the macro. Execution of the contents follows the usual rules. When the macro completes, the current motion system number shall be restored from the stack; therefore the effects of any M596 commands contains in the macro are not persistent. | yes |
| M99, 121 | All processors shall execute the command. The current motion system number is restored from the stack. | yes |
| M112 | Executed by the first processor that reaches it | yes |
| M116 | Executed by the primary processor only. When M116 is used to wait for bed or chamber heaters that are shared between motion systems, M116 should be followed by M400 so that all processors wait for temperature to be reached. | yes |
| M120, 121 | All processors shall execute the command. The current motion system number is stacked by M120 and restored by B121 along with the other values. | yes |
| M150 | Executed by the primary processor only. If multiple processors try to set LED colours at around the same time then the colours may become garbled. | yes |
| M190, 191 | Sync then executed by the primary processor only. If multiple motion systems share a bed or chamber heater then the M190 or M191 command should be followed by M400 so that all processors wait for temperature to be reached. | yes |
| M204 | Executed by the primary processor only. Each processor maintains its own printing and travel accelerations. | yes |
| M206 | Sync then executed by the primary processor only but will affect all processors using the same workplace number | yes |
| M220 | Executed by the primary processor only. Each motion system has its own speed factor. | yes |
| M221 | Executed by the primary processor only. Each extruder has its own extrusion factor. | yes |
| M302 | Executed by the primary processor only but will affect all processors | yes |
| M375 | Sync then executed by the primary processor only |     |
| M400 | Sync | yes |
| M451, 452, 453 | Sync then executed by the primary processor only | yes |
| M555 | Executed by all processors | yes |
| M596 | All processors process this command to check whether the current motion system is changing | yes |
| T   | Only the processor for the current motion system shall process this command and run any applicable tool change files. The tool selection of other motion systems does not change. The other motion system can continue operating while the tool change is in process. A check is made that the requested tool is not already in use by the other motion system. | yes |