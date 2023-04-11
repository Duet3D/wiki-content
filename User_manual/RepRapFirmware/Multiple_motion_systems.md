---
title: Multiple motion systems
description: This page documents the support for multiple motion systems provided in RepRapFirmware 3.5 on Duet 3 boards.
published: true
date: 2023-04-11T20:16:05.390Z
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

# Selecting a motion queue

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

| Single Gcode Processor |||
| Input | Object Model input | Notes |
|---|---|---|
| Http | 0 | |
| Telnet | 1 |  |
| USB | 3 | |
| Aux | 4 | UART (eg PanelDue) |
| Trigger | 5 | |
| LCD | 7 |  |
| SBC | 8 | Used for codes from the SBC, e.g. dsf-config.g, http for the web interface, plugins and the CodeConsole tool. Generally behaves the same as the http channel. |
| Daemon | 9 | |
| Aux2 | 10 | UART2 |
| Autopause | 11 | |

For command streams that originate from file, each motion system uses a separate GCode processor to process commands targeted at that motion system, skipping commands that are targeted at other motion systems. This is when there is a need to synchronise the motion systems at particular points in the file. For example, there may be blocks of motion commands for each motion system that can execute concurrently because each motion system operates within its own defined area, such that collisions will not occur. Subsequently it may be necessary to change these defined areas so that parts of the job that were not not accessible using the previous areas can be accessed. Or, where the motion systems share a common Z axis, it may be necessary for them to both complete the current layer of a 3D print before Z can be moved ready for the next layer.

The commands streams can be synchronised using a M400 command, or another command that locks motion and waits for queued motion to complete (except for G4, see later). Each GCode processor that reaches the M400 command will pause until all other processors have caught up. Note, the G4 command will only wait for motion in the *current* motion system to complete, therefore it cannot be used for perform synchronisation.

## How the queue works

| Multiple Gcode Processors |||
| Input | Object Model input | Notes |
|---|---|---|
| File | 2 | First logical input. |
| File2 | | Second logical input. File2 can not be used directly. |
| Queue | 6 | Queue is used for commands received from File that need to be delayed to sync with movement |
| Queue2 | | Second queue for second file stream. Queue2 does the same as Queue, but for File2. Queue2 can not be used directly. |

The File and File2 channels are logically a single channel. When you start a GCode job, the job file is opened by both of them. If the job contains no commands to use the second motion system then File2 will quickly reach the end of file or the M0 command, and wait for File to catch up. The point of having two channels that are logically a single channel is so that one can get well ahead of the other. Whereas if you send commands for both motion systems through one of the other channels, then the most that one channel can get ahead of the other is limited by the length of the movement queue.

All user-accessible channels default to the primary motion system, but you can use M596 to switch. So you could have a daemon.g that switches to motion system 1 and use it to do background tasks, while everything else uses motion system 0, e.g. the following example for daemon.g runs fully in the second motion system:
```
; NOTE: this example is for continuous motion of another axis.
; The motor is defined as an extruder instead of an axis, then a dummy tool is created that uses it. 
; Then in daemon.g select that tool and send relative extrusion commands.

M596 P1 ; use second motion system
T1      ; select continuous tool
G91     ; use relative extrusion
while {global.runContinuous == true}
  G1 E10 F1000
  G4 P500
```
Each input can be allocated to a motion system, and remains on that motion system until switched back. If you allocate DWC (http) or the PanelDue (Aux) to motion system 1, then all inputs from that relate only to motion system 1. (That means both those UIs need to change motion systems depending on which axes are being jogged etc)

If you are using multiple motion systems then you must explicitly set daemon.g to the motion system it applies to (and the same for all other single stream inputs, e.g. DWC).

When a job file completes or executes M0/1/2, RRF waits for both motion system to stop, not when a macro finishes.

## Macros and motion systems

If a job file, HTTP or SBC calls a macro then both motion systems execute it; but the contained commands will generally be executed by only one of File and File2. The Gcodes that are executed by both Gcode processors regardless of which motion system was selected by the most recent M596 command are: G4, G10L2, G10L20, G17, G18, G19, G20, G21, G54, G55, G56, G57, G58, G59, G68, G69, G90, G91, G93, G94, M0, M1, M23, M24, M32, M37, M82, M83, M98, M99, M112, M120, M121, M400, M555 and M596.

The reason for this is that a macro may contain M596 commands, so both File and File2 need to execute it. Each will skip the commands that don't apply to it.

A few other commands lock both motion systems but they are mostly configuration commands, e.g. M569 and M569.1 when used with parameters other than just the driver number, M574 when changing axis endstops, M150 when addressing Neopixels on a bit-banged port, M563 when used to create or delete a tool, M81, etc. etc.

# Notes on particular commands when multiple GCode processors are used

In the following table, the “primary processor” means the GCode processor whose motion system is current.

All implemented G-commands are listed here. For M-commands not listed here, and for all G- and M-commands implemented by user-defined macros, the action is that only the primary processor executes the command.

Note: some configuration commands that are not normally used in job files wait for all motion to stop before they are executed except when they are used used in report-only mode. When multiple motion systems are active, as only the primary processor executes these commands, the firmware only waits for the primary motion system to stop. In the event that these commands are used in job files, this could result in unexpected behaviour. Examples of such commands are M92 and M350.

| Command | Wait for motion to stop on all system processes? | Notes | Implemented? |
| --- | --- | --- |
| G0 - G3 | No | The motion is executed by the primary processor using the axis mapping specified for the current tool of the current motion system. Other processors skip the command. | yes |
| G4 | Yes | The primary processor waits for all queued moves in its motion system to complete (but not moves in other motion systems) and then executes the delay. Other processors skip the command. | yes |
| G10 retract, G11 | No | Executed by the primary processor only. | yes |
| G10 other | Yes | Tool offset setting: executed by the primary processor only.  <br>Workplace coordinate origin setting: causes sync, then executed by the primary processor but will affect all processors using the same workplace number. | yes |
| G17 - G19 | Yes | Sync then executed by all processors. | yes |
| G20, G21 | Yes | Executed by all processors | yes |
| G28 | No | Sync then executed by all processors. Commands in the homing files are/are not executed according to the usual rules. | yes |
| G29 | No | Sync then if no S parameter is present, all GCode processors execute mesh.g and execution of the contents follows the usual rules; but if the S parameter is present, only the processor for the current motion system executes the command. | yes |
| G30 | No | Sync then executed by the primary processor only | yes |
| G31 | No | Executed by the primary processor only. | yes |
| G32 | No | Sync then all processors execute bed.g. Execution of the contents of bed.g follows the usual rules. | yes |
| G38.2 to G38.5 | | Sync then executed by the primary processor only. | yes |
| G53 | No | Executed by the primary processor only. |     |
| G54 - G59.3 | Yes | Executed by all processors. | yes |
| G60 | No | Executed by primary processor only. | yes |
| G68, 69, 90, 91 | Yes | Executed by all processors. | yes |
| G92 | No | Executed by the primary processor only. | yes |
| G93, 94 | Yes | Executed by all processors. | yes |
| M0, M1 | Yes | Sync then all processors execute stop.g or sleep.g if present, if not present then the primary processor turns all heaters off. Then motion system 0 is selected. | TODO |
| M17, 18, 84 | No | Sync then executed by the primary processor only | yes |
| M23 | Yes | Sync then motion system 0 is selected and the job file is prepared. | TODO |
| M24 | Yes | ??  | TODO |
| M26 | No | ??? Executed by primary processor only | TODO |
| M32 | Yes | Sync then motion system 0 is selected and execution of the job file commences. | TODO |
| M37 | Yes | With P parameter: as for M32 except that simulation mode is set. Without P parameter: processors synchronise, then the simulation mode is changed. Simulation mode it global i.e. it affects all motion systems. | TODO |
| M81 | No | Sync then executed by the primary processor only | yes |
| M82, 83 | Yes | Executed by all processors | yes |
| M98 | Yes | The current motion system number is included in the information stacked. All processors shall run the macro. Execution of the contents follows the usual rules. When the macro completes, the current motion system number shall be restored from the stack; therefore the effects of any M596 commands contains in the macro are not persistent. | yes |
| M99, 121 | Yes | All processors shall execute the command. The current motion system number is restored from the stack. | yes |
| M112 | Yes | Executed by the first processor that reaches it | yes |
| M116 | No | Executed by the primary processor only. When M116 is used to wait for bed or chamber heaters that are shared between motion systems, M116 should be followed by M400 so that all processors wait for temperature to be reached. | yes |
| M120, 121 | Yes | All processors shall execute the command. The current motion system number is stacked by M120 and restored by M121 along with the other values. | yes |
| M150 | See note | Executed by the primary processor only. If multiple processors try to set LED colours at around the same time then the colours may become garbled. Locks all motion systems when addressing Neopixels on a bit-banged port. | yes |
| M190, 191 | No | Sync then executed by the primary processor only. If multiple motion systems share a bed or chamber heater then the M190 or M191 command should be followed by M400 so that all processors wait for temperature to be reached. | yes |
| M204 | No | Executed by the primary processor only. Each processor maintains its own printing and travel accelerations. | yes |
| M206 | No | Sync then executed by the primary processor only but will affect all processors using the same workplace number | yes |
| M220 | No | Executed by the primary processor only. Each motion system has its own speed factor. | yes |
| M221 | No | Executed by the primary processor only. Each extruder has its own extrusion factor. | yes |
| M302 | No | Executed by the primary processor only but will affect all processors | yes |
| M375 | No | Sync then executed by the primary processor only |     |
| M400 | Yes | Sync | yes |
| M451, 452, 453 | No | Sync then executed by the primary processor only | yes |
| M555 | Yes | Executed by all processors | yes |
| M596 | Yes | All processors process this command to check whether the current motion system is changing | yes |
| T | No | Only the processor for the current motion system shall process this command and run any applicable tool change files. The tool selection of other motion systems does not change. The other motion system can continue operating while the tool change is in process. A check is made that the requested tool is not already in use by the other motion system. | yes |