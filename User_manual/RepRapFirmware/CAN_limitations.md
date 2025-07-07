---
title: Duet 3 with CAN expansion firmware configuration limitations
description: RepRapFirmware (as at version 3.4) for Duet 3 with CAN-connected tool or expansion boards currently has the following limitations when tool boards or expansion boards are used.
published: true
date: 2025-07-07T13:40:15.925Z
tags: 
editor: markdown
dateCreated: 2021-12-03T15:19:36.333Z
---

# Introduction

RepRapFirmware for Duet 3 has the following limitations when CAN-connected tool boards or expansion boards are used. A main board used as an expansion board has the same limitations as a regular expansion board.

For the current maximum number of CAN connected devices etc, please see the [RepRapFirmware configuration limits](/User_manual/RepRapFirmware/RepRapFirmware_overview#firmware-configuration-limits)

# Permanent limitations

We do not intend to remove these in future firmware versions.

* After upgrading firmware on an expansion or tool board, its configuration settings are lost. You must restart the main board, or at least re-run config.g. Duet Web Control usually offers a main board restart automatically after auto-installing firmware on an expansion or tool board.
* A heater on an expansion or tool board can only be controlled by a temperature sensor on the same expansion board. This is a safety precaution, because it ensures that temperature control is maintained even if CAN communication is lost.
* Filament monitors must be connected to the same board as the corresponding extruder motor. This is so that the firmware can correlate the measured filament movement and the commanded extruder movement in real time. This restriction is lifted for simple switch-type filament presence detectors in RRF 3.5 and later.

# Semi-permanent limitations

We have no current plans to remove the following limitations, although removing them would be technically possible.

* Z probes connected to expansion or tool boards are limited to [types 8, 9 and 11](/User_manual/Connecting_hardware/Z_probe_connecting). Firmware before 3.5 does not support type 11.
* Ports used for spindle control or for laser control must be on the main board
* DHT temperature/humidity sensors connected to expansion boards are not supported.
* Where an expansion board supports multiple drives, or a main board is used as an expansion board, if multiple motion systems are used then the drivers on that board may not be split between motion systems (i.e. all drivers in use must at any time be used by just one motion system).
* Accelerometers connected to main boards used as expansion boards are not supported.
* A Smart Effector connected to a tool or expansion board cannot be reprogrammed using M672.
* The M670 command cannot be used on ports on CAN expansion boards.

# Temporary limitations

We plan to remove these in future firmware releases.

## Limitations in firmware 3.6 and earlier
* The M571 command cannot be used in conjunction with extruders driven from CAN-connected expansion boards, or IO on expansion boards.
* Using the reset button on the Duet 3 mainboards does not reset the expansion boards, they need to be reset explicitly (`M999 Bnn`). A soft reset of the mainboard (`M999`) will cause the expansion boards to reset.
* In the object model sub-object `sensors.filamentMonitors[].calibrated` is not available for filament monitors on CAN-connected board and will be reported as null.

## Additional limitations in firmware 3.5 and earlier
* Stalls of expansion board motors cannot be used for homing.
* The tower motors of a delta printer cannot be driven via CAN-connected expansion boards.
* In firmware 3.5 the machine coordinates in the object model are updated in semi-real-time (every 250ms) instead of at tne end of each move; however this does not work for axes driven by a CAN-connected expansion board.

## Additional limitations in firmware 3.4 and earlier
* When filament monitors are configured on expansion boards, the "calibrated" values in the object model are not updated; however they can be queried using M591 as usual.
* Input shaping is not supported on axis motors driven by expansion boards.
* Cold extrusion prevention is not enforced on extruders driven from CAN-connected expansion boards.

## Additional limitations in firmware 3.3 and earlier
* The main board does not react to heater faults on expansion boards by pausing the print.
* MCU temperatures and monitored voltages on expansion boards are not yet reported in the object model, although they can be queried using M122.
* A thermostatically-controlled fan on an expansion board can only be controlled by a temperature sensor on the same expansion board.
* Due to CAN latency the motors connected to expansion boards may slightly overshoot the position at which the endstop or Z probe was triggered. This would not usually matter for an endstop switch, but it does mean that if the Z motor(s) is/are connected to an expansion board then repeated probing with a Z probe (e.g. for mesh bed compensation) is not advisable.
* Stalls of expansion board motors are not reported.
* Change of stepper driver status on expansion boards are not proactively reported, e.g. overheat warnings, short to ground etc. although they can be queried using M122.

## Additional limitations in firmware 3.2 and earlier
* Heater tuning (M303) is not supported on expansion or tool boards (M303).
* Endstop switches and Z probes connected to the main board cannot control motors on an expansion board.