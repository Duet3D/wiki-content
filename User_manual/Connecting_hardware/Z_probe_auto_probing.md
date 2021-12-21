---
title: Setting up automatic probing of the print bed
description: 
published: true
date: 2021-12-15T22:42:11.986Z
tags: 
editor: markdown
dateCreated: 2021-10-28T14:00:23.307Z
---

# Introduction

The first layer of a print must be printed at the right height, to within perhaps 50 microns, everywhere across the print bed, to ensure that the print sticks to the bed and the nozzle doesn't crash into it. For a Cartesian printer this may be as simple as ensuring that the bed is level, but a delta printer has a number of additional parameters to adjust. Even if your printer is well calibrated and unchanging, a high-quality Z probe may be more repeatable than your endstop. It is often useful to run a quick autoprobe before every print (and not re-home afterwards). Setting up such an autoprobe requires a certain amount of configuration.

For a comparison of advantages and disadvantages for various Z probe options see [Choosing a Z probe](/User_manual/Connecting_hardware/Z_probe_choosing).

See [Connecting a Z probe](/User_manual/Connecting_hardware/Z_probe_connecting) for details on the electrical connections.

# Choosing which types(s) of probing to use

RepRapFirmware supports two different types of Z probing:

## Bed plane levelling/auto calibration

Running the [G32](/User_manual/Reference/Gcodes/G32) command causes the firmware to execute macro file /sys/bed.g. This macro normally uses G30 commands to define the probe points individually, including an optional Z probe trigger height adjustment for each point, and probe the bed, then:
* on a Delta printer, perform auto calibration, or
* on a Cartesian or CoreXY printer, perform bed plane levelling by moving the Z leadscrews independently, or
* display the manual corrections needed to the bed levelling screws.

In some cases you may want to combine bed plane levelling with manual correction of bed levelling screws.

For full details, see:
[Calibrating a Delta printer](/User_manual/Tuning/Delta_calibration)
[Bed levelling using multiple independent Z motors](/User_manual/Connecting_hardware/Z_probe_auto_levelling)
[Using the Manual Bed Levelling Assistant](/User_manual/Connecting_hardware/Z_probe_manual_levelling)

## Mesh bed compensation

Mesh bed compensation allows the printer to adjust the nozzle height during printing to compensate for an uneven bed or for sag in the gantry. 

[G29](/User_manual/Reference/Gcodes/G29) probing uses a grid of points defined by its size and the spacing between points. You can probe a large number of points without having to define their coordinates individually. It is only used for mesh bed compensation.

On a Delta printer, you can if you wish use G32 to perform [auto calibration](/User_manual/Tuning/Delta_calibration) followed by G29 to compensate for any residual bed height errors. 
On a Cartesian or CoreXY printer you can use G32 bed probing for [Bed levelling using multiple independent Z motors](/User_manual/Connecting_hardware/Z_probe_auto_levelling), followed by G29 probing to do bed compensation.

For full details, see [Using mesh bed compensation](/User_manual/Connecting_hardware/Z_probe_mesh_bed)

# Probing-related G-codes

* [G30](/User_manual/Reference/Gcodes/G30) - run a single probe.
* [G31](/User_manual/Reference/Gcodes/G31) - set up probe, in particular the Z height at which the probe triggers; note the firmware supports multiple probes, so you must run M558 first to specify which one.
* [G32](/User_manual/Reference/Gcodes/G32) - run a full autoprobe (that is, execute bed.g or uses the matrix defined by M557)
* [G92](/User_manual/Reference/Gcodes/G92) - redefine coordinates so that current position is zero (or values specified)
* [M114](/User_manual/Reference/Gcodes/M114) - report current position.
* [M557](/User_manual/Reference/Gcodes/M557) - Set Z probe point or define probing grid.
* [M558](/User_manual/Reference/Gcodes/M558) - set probe type (e.g. analog); also select which probe to use, so run this early in the setup process
* [M561](/User_manual/Reference/Gcodes/M561) - clear all autocalibration or G92 settings
* [M665](/User_manual/Reference/Gcodes/M665) - set delta configuration; this includes the manual calibration values that are the starting point for autocalibration. You can also use this command after an autoprobe to see the fitted values
* [M666](/User_manual/Reference/Gcodes/M666) - set delta endstop adjustment; this includes the manual calibration values that are the starting point for autocalibration. You can also use this command after an autoprobe to see the fitted values