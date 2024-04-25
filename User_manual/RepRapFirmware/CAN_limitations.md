---
title: Duet 3 with CAN expansion firmware configuration limitations
description: RepRapFirmware (as at version 3.4) for Duet 3 with CAN-connected tool or expansion boards currently has the following limitations when tool boards or expansion boards are used.
published: true
date: 2024-04-25T11:58:38.157Z
tags: 
editor: markdown
dateCreated: 2021-12-03T15:19:36.333Z
---

# Introduction

RepRapFirmware (as at version 3.4) for Duet 3 with CAN-connected tool or expansion boards currently has the following limitations when tool boards or expansion boards are used.

For the current maximum number of CAN connected devices etc, please see the [RepRapFirmware configuration limits](/User_manual/RepRapFirmware/RepRapFirmware_overview#firmware-configuration-limits)

# Permanent limitations

We do not intend to remove these in future firmware versions.

* After upgrading firmware on an expansion or tool board, its configuration settings are lost. You must restart the main board, or at least re-run config.g. Duet Web Control usually offers a main board restart automatically after auto-installing firmware on an expansion or tool board.
* A heater on an expansion or tool board can only be controlled by a temperature sensor on the same expansion board. This is a safety precaution, because it ensures that temperature control is maintained even if CAN communication is lost.
* Filament monitors must be connected to the same board as the corresponding extruder motor. This use so that the firmware can correlate the measured filament movement and the commanded extruder movement in real time.

# Semi-permanent limitations

We have no current plans to remove the following limitations, although removing them would be technically possible.

* Z probes connected to expansion or tool boards are limited to [types 8 and 9](/User_manual/Connecting_hardware/Z_probe_connecting). Firmware 3.5 also supports type 11.
* Ports used for spindle control must be on the main board
* DHT temperature/humidity sensors connected to expansion boards are not supported.

# Temporary limitations

We plan to remove these in future firmware releases.

* ~~Heater tuning is not yet supported on expansion or tool boards (M303).~~ Support is included in release 3.3
* ~~Endstop switches and Z probes connected to the main board cannot control motors on an expansion board.~~ This is fixed in release 3.3.
* ~~The main board does not react to heater faults on expansion boards by pausing the print.~~ This restriction is removed in firmware 3.4.
* ~~MCU temperatures and monitored voltages on expansion boards are not yet reported in the object model (but can be queried using M122).~~ This restriction is removed in firmware 3.4.
* ~~A thermostatically-controlled fan on an expansion board can only be controlled by a temperature sensor on the same expansion board.~~ This restriction is removed in firmware 3.4.
* ~~Due to CAN latency the motors connected to expansion boards may slightly overshoot the position at which the endstop or Z probe was triggered. This would not usually matter for an endstop switch, but it does mean that if the Z motor(s) is/are connected to an expansion board then repeated probing with a Z probe (e.g. for mesh bed compensation) is not advisable.~~ This restriction is removed in firmware 3.4.
* ~~Stalls of expansion board motors are not yet reported.~~ This restriction is removed in firmware 3.4.
* ~~Change of stepper driver status on expansion boards are not proactively reported (but can be queried using M122). e.g. overheat warnings, short to ground etc.~~ This restriction is removed in firmware 3.4.
* Stalls of expansion board motors cannot be used for homing. We expect to remove this restriction in firmware 3.6.
* Cold extrusion prevention is not enforced on extruders driven from CAN-connected expansion boards. We expect to remove this restriction in firmware 3.5.
* When filament monitors are configured on expansion boards, the "calibrated" values in the object model are not updated; however they can be queried using M591 as usual. We expect to remove this restriction in firmware 3.5.
* The M571 command cannot be used in conjunction with extruders driven from CAN-connected expansion boards.
* The tower motors of a delta printer cannot be driven via CAN-connected expansion boards. Workaround: use M669 to enable segmentation with short (e.g. 1mm) segments.
* Input shaping is not supported on axis motors driven by expansion boards. We expect to remove this restriction in firmware 3.5.
* Using the reset button on the Duet 3 mainboards does not reset the expansion boards. they need to be reset explicitly (M999 Bnn). A soft reset of the mainboard (M999) will cause the expansion boards to reset.
* In firmware 3.5 the machine coordinates in the object model are updated in semi-real-time (every 250ms) instead of at tne end of each move; however this does not work if the Z axis is driven by an expansion board.
* Accelerometers connected to main boards used as expansion boards are not supported.
* A Smart Effector connected to a tool or expansion boadr cannot be rpogrammed using M672.