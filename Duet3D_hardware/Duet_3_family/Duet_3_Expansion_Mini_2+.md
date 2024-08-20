---
title: Duet 3 Expansion Mini 2+
description: The Duet 3 Expansion Mini 2+ is a two driver expansion board designed to connect directly onto the expansion header of the Duet 3 mini 5+ mainboard. It provides 2 x TMC 2209 stepper motors drivers.
published: true
date: 2024-08-20T09:51:24.579Z
tags: 
editor: markdown
dateCreated: 2021-07-14T10:24:30.415Z
---

![duet_3_mini_2+.jpg](/duet_boards/duet_3_mini_5_plus/duet_3_mini_2+.jpg =400x)

# Introduction

The Duet 3 Expansion Mini 2+ is specifically designed for the expansion header of the [Duet 3 Mini 5+](/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Hardware_Overview). It may be possible to use it with other mainboards but this is not supported. It provides 2 x TMC 2209 stepper motors drivers with the same configurability as the 5 mounted on the Duet 3 Mini 5+ (i.e. UART control of all supported TMC2209 features, and stall detection signals).

# Features

## Hardware specification

The main hardware features of the Duet 3 Expansion Mini 2+ are listed below.

| HARDWARE SPECIFICATION ||
|:---|:---|
| **On-board stepper drivers** | 2 x [TMC2209](https://www.trinamic.com/products/integrated-circuits/details/tmc2209-la/) |
| **Stepper driver features** | Up to 2.0A peak current (TBC), microstep interpolation from any setting to x256, stall detection, stealthChop2 |
| **Other I/O** | NIL |
| **Connection** | Directly mounted on the Duet 3 Mini 5+ Mainboard. |

## Operating limits

|---|---|
| **Stepper drivers** | Up to 2.0A peak current (TBC) |
| **Input power voltage** | 11V to 25V (Via the Duet 3 Mini 5+ expansion header) |
| **Fuses** | No on board fuses, shared V_FUSED protection from  Duet 3 Mini 5+ |
| **Maximum ambient temperature** | 70°C |


## Firmware notes

Compatible RepRapFirmware versions: RRF 3.2 and later on the Duet 3 Mini 5+ mainboard. There is no firmware loaded directly onto the Duet 3 Expansion Mini 2+ itself.

The drivers are numbered "5" and "6" to reference them, for example to assign them as the third and fourth extruder:

`M584 X0 Y1 Z2 E3:4:5:6`

## Open Source

Importantly Duets are Open:

* The Duets are Open Hardware, https://github.com/Duet3D/Duet3-Mini5plus/blob/main/LICENSE
* All hardware [source files](https://github.com/Duet3D/Duet3-Mini5plus) are available on Github.
* Both the [Duet Web Control](https://github.com/Duet3D/DuetWebControl) web interface and [RepRapFirmware](https://github.com/Duet3D/RepRapFirmware) are [Open Source Software](http://www.gnu.org/licenses/gpl-3.0.en.html) with source files available and actively maintained, see [Contributing to development](/User_manual/Reference/Developers) for more information.
* The Duet hardware and RepRapFirmware are built with Open tools: designed in [KiCad](http://kicad.org/) and [Ecplise](https://eclipse.org/) using open tools means the barrier to getting involved is as low as possible.

# Physical properties

## Dimensions

![duet_3_mini_2+_dimensions_v1.0.png](/duet_boards/duet_3_mini_5_plus/duet_3_mini_2+_dimensions_v1.0.png =400x)

## Mounting

The Duet 3 Expansion Mini 2+ has mounting standoff holes that align with matching holes on the Duet 3 Mini 5+.

![duet_3_mini_2+_front.png](/duet_boards/duet_3_mini_5_plus/duet_3_mini_2+_front.png =250x) ![duet_3_mini_2+_mounting_01.png](/duet_boards/duet_3_mini_5_plus/duet_3_mini_2+_mounting_01.png =250x) ![duet_3_mini_2+_mounting_02.png](/duet_boards/duet_3_mini_5_plus/duet_3_mini_2+_mounting_02.png =250x)

Nylon standoffs are supplied with the Duet 3 Expansion Mini 2+ to mount it securely.

* 11mm tall for 1.6mm PCB with 2.5mm holes -e.g. FIX-MADA-11 or TRDLCBST-7-01

## Cooling

The stepper drivers' heatsinks are connected to the PCB and the majority of the heat is dissipated via the PCB. In many applications passive cooling will be sufficient, especially if the Duet 3 Mini 5+ with Duet 3 Expansion Mini 2+ combination is mounted vertically in a well ventilated position. If active cooling is needed then a fan blowing from the side along the line of the stepper drivers is recommended this should cause airflow across the back of the mini5+ and the back of the Duet 3 Expansion Mini 2+.

## 3D Model

* [STEP file](https://forum.duet3d.com/assets/uploads/files/1611937366939-duet3_exp_2plus.step)

# Physical connections

## Wiring diagram

[![duet_3_mini_2+_wiring_v1.0.png](/duet_boards/duet_3_mini_5_plus/duet_3_mini_2+_wiring_v1.0.png =500x)](/duet_boards/duet_3_mini_5_plus/duet_3_mini_2+_wiring_v1.0.png){target=_blank}

