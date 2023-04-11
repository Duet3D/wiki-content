---
title: Heaters overview
description: 
published: true
date: 2023-04-11T14:23:14.115Z
tags: 
editor: markdown
dateCreated: 2021-09-20T12:20:29.631Z
---

# Introduction

The Duet range of controller boards have a number of outputs to directly connect heaters. These are supplied by VIN voltage, ie the voltage of the PSU that powers the Duet, and are generally protected by a fuse. Mainboards usually have one 15A output, primarily intended for a 3D printer heated bed, and two or three (depending on board) 6A outputs, primarily intended for a 3D printer's extruder heaters. See the wiring diagram for your board for specific information and wiring. Additional heaters can be controlled by adding expansion boards, and/or by using PWM outputs on board to control Solid State Relays.

Heater outputs can also be used for other applications, eg CNC spindle control, laser cutter/engraver control, pumps and other electric motors that draw more current or require a higher voltage signalling level than other PWM pins on the board can support.

# Heater support

| Board | 'Bed heater' output | 'Extruder heater' outputs | RRF3 pin name | RRF2 heater number
|:---|:---|
| Duet 3 Mainboard 6HC | 1 x 18A (15A fuse) | 3 x 6A | out0 (bed), out1, out2, out3 | - |
| Duet 3 Mini 5+ | 1 x 15A (15A fuse) | 2 x 5A | out0 (bed), out1, out2 | - |
| Duet 3 Expansion 3HC | - | 3 x 6A | out0, out1, out2 | - |
| Duet 3 Toolboard 1LC | - | 1 x 5A | out0 | - |
| Duet 2 WiFi/Ethernet | 1 x 18A (15A fuse) | 2 x 6A | bedheat, e0heat, e1heat | 0 (bed), 1, 2 |
| DueX2 and DueX5 | - | 2 (DueX2) or 5 (DueX5) x 6A | duex.e2heat to duex.e6heat | 3 to 7 |
| Duet 2 Maestro | 1 x 18A (15A fuse) | 2 x 6A | bedheat, e0heat, e1heat | 0 (bed), 1, 2 |

Note that some boards are rated to 18A on the bed heater channel; however all Duets are supplied with a 15A fuse fitted (18A fuses are difficult to source). If you need 18A on the bed heater channel then you need to fit a 20A fuse and take further precautions against over-current. This rating has been increased from 15A due to [updated thermal testing](http://blog.think3dprint3d.com/2017/04/duetwifi-updated-thermal-testing.html).

# Heater configuration limits

RepRapFirmware has some limits on how heaters can be configured. As of RRF 3.4 these are:

| | Duet 3 MB6HC/6XD | Duet 3 Mini 5+ | Duet 2 WiFi/Ethernet | Duet 2 Maestro | Notes |
|---|---|
| MaxHeaters | 32 | 32 | 10 | 4 | The maximum number of heaters |
| MaxPortsPerHeater | 3 | 2 | 2 | 2 | The maximum number of output ports per heater |
| MaxMonitorsPerHeater | 3 | 3 | 3 | 3 | The maximum number of monitors per heater |
| MaxBedHeaters | 12 | 2 | 4 | 2 | The maximum number of bed heaters |
| MaxChamberHeaters | 4 | 2 | 4 | 2 | The maximum number of chamber heaters |
| MaxHeatersPerTool | 8 | 2 | 8 | 2 | The maximum number of heaters per tool |

# Connecting heaters

See the following sections for more detail on connecting and configuring heaters:
[Connecting and configuring extruder heaters](/User_manual/Connecting_hardware/Heaters_extruders)
[Connecting and configuring a bed heater](/User_manual/Connecting_hardware/Heaters_bed)
[Connecting and configuring a chamber heater](/User_manual/Connecting_hardware/Heaters_chamber)
[Tuning the heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning)

# Temperature sensing

Note that while it's possible to configure a heater with no temperature control, for safety all heaters should have a temperature sensing channel associated with them. The Duet series supports 4 types of temperature sensor: thermistor and PT1000, thermocouple, and PT100. For more details see [Choosing temperature sensors](/User_manual/Connecting_hardware/Temperature_choosing).

# Using heater connectors for other functions

See [Connecting servos and controlling IO pins](/User_manual/Connecting_hardware/Motors_servos).