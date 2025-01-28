---
title: Duet3D Thermocouple daughterboard
description: Overview of the Duet 3D Thermocouple daughter board.
published: true
date: 2025-01-28T10:47:22.573Z
tags: 
editor: markdown
dateCreated: 2021-09-07T12:01:36.003Z
---

![thermocouple_db_v1.1_top.jpg](/hardware/temperature_db/thermocouple_db_v1.1_top.jpg =400x)

# Introduction

The Duet range of machine controllers support one or more temperature daughterboards which provide expanded temperature sensor connectivity using Maxim integrated ICs. These daughterboards connect to the Duet digitally over SPI.

# Features

## Supported hardware

Each daughterboard supports 2 channels, ie two thermocouples per daughterboard. Duet mainboards and expansion boards support none, one or two daughterboards. Refer to the table below.

| Duet board | Number of boards supported |
|---|---|
| Duet 3 Mainboard 6HC | 2, stacked |
| Duet 3 Mainboard 6XD | 2, stacked |
| Duet 3 Mini 5+ | 1* |
| Duet 3 Expansion 3HC | 2, stacked |
| Duet 3 Expansion 1HCL | 1, v2.0 only |
| Duet 3 Roto Toolboard | Not supported ||
| Duet 3 Toolboard 1LC | 0 |
| Duet 3 Expansion 1XD | 0 |
| Duet 2 WiFi / Ethernet | 2, stacked |
| DueX2 / DueX5 | 2, stacked |
**Note,the Mini 5+ Ethernet v1.03 supports two SPI DB boards stacked, v1.02a and earlier support 1*

Mainboards and expansion boards that support only one daughterboard can have either an ADC, Thermocouple or PT100 temperature sensor daughterboard connected, but not two. Those that support two SPI daughterboards can have different daughterboards connected.

## Thermocouple Support

There have been two versions of the thermocouple board. 
| Board revision | Sensor IC | Supported thermocouples |
|---|---|
| v1.1 | MAX31856 | 2 x thermocouples of type B, E, J, K, N, R, S or T |
| v1.0 and earlier | MAX31855 | 2 x thermocouples of type K |

## MAX31856 sensor IC details

The MAX31856 performs cold-junction compensation and digitizes the signal from any type of thermocouple. The output data is formatted in degrees Celsius. This converter resolves temperatures to 0.0078125°C, allows readings as high as +1800°C and as low as -210°C (depending on thermocouple type), and exhibits thermocouple voltage measurement accuracy of ±0.15%.  See the [Maxim integrated website for more details on the MAX31856](https://www.maximintegrated.com/en/products/sensors/MAX31856.html){target=_blank}.

## Operating limits

* Maximum ambient temperature: 70°C

## Open source

* Hardware [source files](https://github.com/Duet3D/TempDaughterboards/tree/master/MAXTempTC){target=_blank} are available on Github.
* Duet hardware is built with Open tools: designed in [KiCad](http://kicad.org/){target=_blank}. Using open tools means the barrier to getting involved is as low as possible.

## 3D model

The STEP file for the Duet3D Thermocouple daughterboard is shared on the [Duet3D github here](https://github.com/Duet3D/TempDaughterboards/blob/master/MAXTempTC/MAXTempTC.step){target=_blank}.

# Installation and configuration

For setting up Duet controllers to use thermocouple sensors see: [Connecting thermocouples](/User_manual/Connecting_hardware/Temperature_connecting_thermocouples)

# Other Temperature sensors

The Duet range of machine controllers support various other temperature sensors. See:

* [A comparison of temperature sensors](/User_manual/Connecting_hardware/Temperature_choosing)
* [Thermistors or PT1000 temperature sensors](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000)
* [PT100 temperature sensors](/User_manual/Connecting_hardware/Temperature_connecting_PT100)

# Revision history

# Tabs {.tabset}

## Version 1.1

* MAX31856-based daughterboards, each supporting two thermocouples of type B, E,J K, N, R, S or T.
* Addition of two LEDs labelled FAULT, one per channel.

![thermocouple_db_v1.1_top.jpg](/hardware/temperature_db/thermocouple_db_v1.1_top.jpg =300x)

## Older versions

* MAX31855-based daughterboards, each supporting two K-type thermocouples