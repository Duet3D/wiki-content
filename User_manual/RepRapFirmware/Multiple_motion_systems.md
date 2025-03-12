---
title: Multiple motion systems
description: This page documents the support for multiple motion systems provided in RepRapFirmware 3.5 on Duet 3 boards.
published: true
date: 2025-03-12T12:37:15.432Z
tags: 
editor: markdown
dateCreated: 2022-03-22T10:08:15.620Z
---

# Overview

RepRapFirmware 3.5 provides experimental support for multiple motion systems that move asynchronously with respect to each other. Each motion system has its own current tool. A single GCode job file or input stream controls all the motion systems. Additional commands in the GCode stream specify which motion system each command is destined for. Some commands (in particular [M400](/User_manual/Reference/Gcodes/M400)) cause motion to be synchronised, i.e. when the command is reached the system waits for all commands for any motion system prior to that command to complete before continuing.

The primary application is to speed up printing by having the printer work on separate parts of a large print concurrently, or to print two different objects at the same time.

# Prerequisites

-   Due to memory limitations, RRF on Duet 2 series boards does not support this feature. All Duet 3 main boards support it, however performance on Duet 3 Mini might be limiting.
-   Only two motion systems are currently supported. In the future we may increase this limit on Duet 3 MB6HC and MB6XD boards.
-   By default, the second motion queue is only three elements long. Use the [M595](/User_manual/Reference/Gcodes/M595) command to lengthen it, for example: `M595 Q1 P60` will increase it to 60 elements (the same number as in the primary movement queue on MB6HC and MB6XD boards).
-   At any time, each motion system “owns” a set of physical axes and extruders. It may also own a tool. No other motion system can use those axes/extruders or that tool until the owning motion system releases it. See below for details of ownership of axes and extruders.
-   For any two axes owned by different motion systems, those axes may not share a motor. For example, on a CoreXY machine the X and Y axes cannot be owned by different motion systems.

# Enabling and selecting a motion queue

If running from a file (i.e. a job or macro on the SD card), use [M606](/User_manual/Reference/Gcodes/M606), by adding `M606 S1` at the beginning of the file, to enable the forking of the file to each motion system queue.

Use the [M596](https://docs.duet3d.com/en/User_manual/Reference/Gcodes#m596-select-movement-queue-number) command to specify which motion system any motion or tool operations in the following commands should be targeted at. The selection of target motion system remains in force until one of the following occurs:

-   Another [M596](/User_manual/Reference/Gcodes/M596) command is encountered
-   The current macro ends. The motion system selected immediately prior to calling the macro is selected.
-   A [M121](/User_manual/Reference/Gcodes/M121) (pop stack) command is executed. The motion system selected immediately prior to executing the corresponding [M120](/User_manual/Reference/Gcodes/M120) (push stack) command is selected.
-   The job is terminated by end-of file or by a [M0](/User_manual/Reference/Gcodes/M0), [M1](/User_manual/Reference/Gcodes/M1) or [M2](/User_manual/Reference/Gcodes/M2) command. Motion system 0 is selected.
-   [M32](/User_manual/Reference/Gcodes/M32) or [M23](/User_manual/Reference/Gcodes/M23) is executed to start a new file or to prepare to start a file. Motion system 0 is selected.

# Avoiding collisions

Use the [M597](https://docs.duet3d.com/en/User_manual/Reference/Gcodes#m597-collision-avoidance) command to set up a restriction on how close together the positions of two axes can get. For example, if you machine has separate Y and V gantries that move independently, use M597 to prevent them from coming too close together. Only one pair of axes can be restricted in this way.

# Synchronising the motion systems

There will be times when the two motion systems need to be synchronised. For example, when the two motion systems are used to drive the two print heads of a IDEXY machine independently, they will need to synchronise at each layer change because they share a common Z axis. 
* Use the [M598](/User_manual/Reference/Gcodes/M598) command to cause the movement system that reaches the command first to wait for the other one to catch up. Upon reaching the M598 command, each motion system will release the axes that it owns, but not any extruders used by a tool that it owns.
* Use [M400](/User_manual/Reference/Gcodes/M400) to wait until all motion stops, and release all owned axes and extruders
* Use `M400 S1` or `G4 P0` to wait until all motion stops, but NOT release any owned axes and extruders

Certain other commands will also cause synchronisation (details to follow).

# Command execution using multiple motion queues

The way that command streams that use multiple motion systems are processed depends on the source of the command stream.

## Command streams from USB, serial input and DWC console

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

## Command streams from file

For command streams that originate from file (i.e. job or macro on the SD card), each motion system uses a separate GCode processor to process commands targeted at that motion system, skipping commands that are targeted at other motion systems. 

**Note:** to enable the separate Gcode processors, the file/macro should include an [M606](/User_manual/Reference/Gcodes/M606) command to fork the input file, and allow the input stream to be forked to each Gcode processor. Add `M606 S1` at the beginning of any file that uses multiple motion systems.

This is when there is a need to synchronise the motion systems at particular points in the file. For example, there may be blocks of motion commands for each motion system that can execute concurrently because each motion system operates within its own defined area, such that collisions will not occur. Subsequently it may be necessary to change these defined areas so that parts of the job that were not not accessible using the previous areas can be accessed. Or, where the motion systems share a common Z axis, it may be necessary for them to both complete the current layer of a 3D print before Z can be moved ready for the next layer.

The commands streams can be synchronised using a M598 command. Each GCode processor that reaches the M598 command will pause until all other processors have caught up.

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

When a job file completes or executes M0/1/2, RRF waits for both motion system to stop.

## Macros and motion systems

If a job file, HTTP or SBC calls a macro then both motion systems execute it; but the contained commands will generally be executed by only one of File and File2. The reason for this is that a macro may contain M596 commands, so both File and File2 need to execute it. Each will skip the commands that don't apply to it.

A few other commands lock both motion systems but they are mostly configuration commands, e.g. M569 and M569.1 when used with parameters other than just the driver number, M574 when changing axis endstops, M150 when addressing Neopixels on a bit-banged port, M563 when used to create or delete a tool, M81, etc.

## Input channel flags

Each input channel (SD card, USB, http, telnet etc) has its own set of flags. For all of the Gcode commands below, the following is true:
* Each input channel has its own flag for each state.
* At the end of running config.g at startup, the flag state is copied to all input channels. E.g. M83 in config.g will default all input channels to relative extrusion.
* The flag state is saved when a macro starts and is restored when a macro ends. E.g. M82/M83 in a macro will affect the remainder of the macro, but will not affect commands after the M98 command that invoked the macro. This means that macros can safely change to absolute or relative without messing up whatever job or script called them.

| Command | Description | Default flag | Notes |
|---|---|---|
| G1/G2/G3 (and G0 in FDM mode) | Feed rate | 3000mm/min | flag for feed rate if the next G0/1/2/3 command doesn't include an F parameter |
| G17/G18/G19 | The selected plane for arc moves | G17 (XY plane) | |
| G20/G21 | Units (inches or mm) | G21 (mm) | |
| G90/G91 | Axis absolute/relative positioning | G90 (absolute) | |
| G93/G94 | Inverse time mode/units per minute mode | G94 (units per minute) | |
| M82/83 | Extruder absolute/relative mode | M82 (absolute) | |
| M200 | Volumtric extrusion | Disabled | |

