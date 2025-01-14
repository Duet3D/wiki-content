---
title: Duet3D ADC daughterboard
description: Overview of the Duet3D Analog-to-Digital Converter (ADC)  daughterboard
published: false
date: 2025-01-14T12:23:17.259Z
tags: 
editor: markdown
dateCreated: 2025-01-09T18:02:17.231Z
---

![spi_adc_0.2_1_xs.jpg](/hardware/adc_db/spi_adc_0.2_1_xs.jpg =x300)

# Introduction

This daughterboard provides two high resolution analog-to-digital converter (ADC) channels. This is useful for reading analog signals such as pressure feedback from external hardware. This daughterboard is one of a number of SPI connected sensor boards that also include the [PT100](/Duet3D_hardware/Accessories/PT100_Temperature_Daughterboard) and [Thermocouple](/Duet3D_hardware/Accessories/Thermocouple_Daughterboard) daughterboards. 

# Features

## Supported hardware

Each daughterboard supports 2 channels, ie two seperate analog inputs per daughterboard. Duet mainboards and expansion boards support two, one or zero daughterboards. Refer to the table below.

| Duet board | Number of boards supported | Notes
|---|---|--|
| Duet 3 Mainboard 6HC | 2 | stacked|
| Duet 3 Mainboard 6XD | 2 | stacked |
| Duet 3 Mini 5+ | 1 | Direct connection fouls the TEMP0 connector so a short ribbon cable <100mm (not supplied) is required to connect the board |
| Duet 3 Expansion 3HC | 2 | stacked |
| Duet 3 Expansion 1HCL | 1 | v2.0 only. Direct connection fouls the DRIVER connector so a short ribbon cable <100mm (not supplied) is required to connect the board|
| Duet 3 Toolboard 1LC | Not supported ||
| Duet 3 Roto Toolboard | Not supported ||
| Duet 3 Expansion 1XD | Not supported ||

Mainboards and expansion boards that support only one daughterboard can have either a ADC, Thermocouple or PT100 temperature sensor daughterboard connected, but not two. Those that support two SPI daughterboards can have different daughterboards connected.

# Features

## Hardware specification

|---|---|
| **ADC IC** | [ADS131A02](https://www.ti.com/product/ADS131A02){target=_blank}. 24-bit 128-kSPS 2-channel simultaneous-sampling delta-sigma ADC 
| **Analog voltage input range**||
| Unipolar| 0 to +10V |
| Bipolar| -5 to +5V |

## Operating limits

|---|---|
| **Input power voltage** | 12-36V |
| **Input power max current** | <200mA |
| **Data signalling voltage** | 3.3V |
| **Maximum ambient temperature** | 80°C |
| **Absolute maximum board temperature** | 85°C |

## Firmware notes

* Compatible RepRapFirmware versions: RRF 3.6 or later

# Physical properties

## Dimensions







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

