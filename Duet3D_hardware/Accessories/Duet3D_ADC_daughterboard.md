---
title: Duet3D ADC daughterboard
description: Overview of the Duet3D Analog-to-Digital Converter (ADC)  daughterboard
published: false
date: 2025-01-09T18:13:39.161Z
tags: 
editor: markdown
dateCreated: 2025-01-09T18:02:17.231Z
---

![pt100_db_v1.1_top.jpg](/hardware/temperature_db/pt100_db_v1.1_top.jpg =400x)

# Introduction

This daughterboard provides two high resolution analog-to-digital converter (ADC) channels. This is useful for reading analog signals such as pressure feedback from external hardware. This daughterboard is one of a number of SPI connected daughterboards that also includes the [PT100](/Duet3D_hardware/Accessories/PT100_Temperature_Daughterboard) and [Thermocouple](/Duet3D_hardware/Accessories/Thermocouple_Daughterboard) daughterboards. 

# Features

## Supported hardware

Each daughterboard supports 2 channels, ie two thermocouples per daughterboard. Duet mainboards and expansion boards support none, one or two daughterboards. Refer to the table below.

| Duet board | Number of boards supported |
|---|---|
| Duet 3 Mainboard 6HC | 2, stacked |
| Duet 3 Mainboard 6XD | 2, stacked |
| Duet 3 Mini 5+ | 1 |
| Duet 3 Expansion 3HC | 2, stacked |
| Duet 3 Expansion 1HCL | 1, v2.0 only |
| Duet 3 Toolboard 1LC | 0 |
| Duet 3 Expansion 1XD | 0 |

Boards that support only one daughterboard can have either a ADC, Thermocouple or PT100 temperature sensor daughterboard connected, but not two.

# Features

## Hardware specification

|---|---|
| **ADC IC** | [ADS131A02](https://www.ti.com/product/ADS131A02){target=_blank}. 24-bit 128-kSPS 2-channel simultaneous-sampling delta-sigma ADC 
| **Analog voltage input range** | 0-10V |
| **Power monitoring** | VIN voltage reporting |



## Operating limits

|---|---|
| **Input power voltage** | 5V |
| **Power consuption** | <200mA |
| **Maximum ambient temperature** | 75°C |
| **Absolute maximum board temperature** | 85°C |

Note: The coil PCB can likely cope with higher temperatures than those quoted above, having no active components. 
Duet-supplied FFC cables are rated at 80°C, but cables are available with a higher temperature rating.

## Firmware notes

* Compatible RepRapFirmware versions: RRF 3.6 or later

# Physical properties

## Dimensions





## ADC Details




## Operating limits

* Maximum ambient temperature: 70°C

## Open source

* Hardware [source files](https://github.com/Duet3D/TempDaughterboards/tree/master/MAXTempRTD){target=_blank} are available on Github.
* Duet hardware is built with Open tools: designed in [KiCad](http://kicad.org/){target=_blank}. Using open tools means the barrier to getting involved is as low as possible.

## 3D model

The STEP file for the Duet3D PT100 temperature sensor daughterboard is shared on the [Duet3D github here](https://github.com/Duet3D/TempDaughterboards/blob/master/MAXTempRTD/MAXTempRTD.step){target=_blank}.

# Installation and configuration

For setting up the Duet us use PT100 sensors see [Connecting PT100 temperature sensors](/User_manual/Connecting_hardware/Temperature_connecting_PT100)


# Other Temperature sensors

The Duet range of machine controllers support various other temperature sensors. See:

* [A comparison of temperature sensors](/User_manual/Connecting_hardware/Temperature_choosing)
* [Thermistors or PT1000 temperature sensors](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000)
* [Thermocouples](/User_manual/Connecting_hardware/Temperature_connecting_thermocouples)

# Revision history

# Tabs {.tabset}

## v1.1a

The following minor changes from v1.1 to 1.1a:

* The addition of a ground point between the two sets of screw terminals to make it easier to ground shielded wires.
* Added 0u1 filtering capacitors on RTDIN(+/-).
* Switched from 1x400R to 2x200R in series reference resistors for component availability reasons.

## v1.1

From version 1.1 onwards there are two pin jumpers per input channel.

![pt100_db_v1.1_top.jpg](/hardware/temperature_db/pt100_db_v1.1_top.jpg =400x)

## Older versions

Versions of the board before v1.1 had solder jumpers to convert from 2 wire to 4 wire.

![pt100_db_v0.3_top.jpg](/hardware/temperature_db/pt100_db_v0.3_top.jpg =400x)

