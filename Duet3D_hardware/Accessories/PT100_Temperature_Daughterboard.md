---
title: Duet3D PT100 temperature sensor daughterboard
description: Overview of the Duet3D PT100 daughterboard
published: true
date: 2025-01-28T10:47:15.823Z
tags: 
editor: markdown
dateCreated: 2021-09-07T11:12:36.201Z
---

![pt100_db_v1.1_top.jpg](/hardware/temperature_db/pt100_db_v1.1_top.jpg =400x)

# Introduction

The Duet range of machine controllers support one or more SPI daughterboards which provide expanded sensor connectivity. These daughterboards connect to the Duet digitally over SPI.

# Features

## Supported hardware

Each daughterboard supports 2 channels, ie two PT100 sensors per daughterboard. Duet mainboards and expansion boards support none, one or two daughterboards. Refer to the table below.

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

## PT100 sensor support

The PT100 daughterboard uses the Maxim Integrated MAX31865 RTD sensor IC.  This IC supports 2,3 or 4 wire PT100 connections. The mode can be set using jumpers on the board. With the jumpers on/bridged the board expects a 2 wire PT100 sensor. With the jumpers off/cut a 4 wire PT100 sensor can be used. The change between older versions and 1.1 was to move from solder jumpers to pin jumpers.

## MAX31865 sensor IC details

An external resistor sets the sensitivity for the RTD being used (in the case of the Duet3D daughterboard this is a PT100) and a precision delta-sigma ADC converts the ratio of the RTD resistance to the reference resistance into digital form. High Accuracy: 15-Bit ADC Resolution; Nominal Temperature Resolution 0.03125°C (Varies Due to RTD Nonlinearity). Total Accuracy Over All Operating Conditions: 0.5°C (0.05% of Full Scale) max. For more information see the [Maxim Integrated MAX31865 product page](https://www.maximintegrated.com/en/products/interface/sensor-interface/MAX31865.html){target=_blank}.

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

