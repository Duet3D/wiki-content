---
title: Choosing temperature sensors
description: 
published: true
date: 2021-12-15T14:54:29.752Z
tags: 
editor: markdown
dateCreated: 2021-09-07T15:12:42.651Z
---

# Scope

**This document is relevant to:** all Duet boards
**Firmware versions:** all firmware versions

# Choosing temperature sensors

## Supported sensor types

The Duet series supports 4 types of temperature sensor: thermistor, PT1000, thermocouple, and PT100.

## Capability table

Here are the pros and cons of each:

| Property | [Thermistor](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000) | [Thermocouple](/User_manual/Connecting_hardware/Temperature_connecting_thermocouples) | [PT100](/User_manual/Connecting_hardware/Temperature_connecting_PT100) | [PT1000](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000) |
|:---|:---|
| **Cost** | Low | Medium | Higher | Higher |
| **Precision** | Low | Medium | High | Medium |
| **Needs adapter board?** | No | Yes | Yes | No |
| **Susceptible to interference?** | No | Very | Somewhat | No |
| **Temperature limit** | Typically 280-300C | Up to 1100C depending on type | Up to 500C depending on type | Up to 500C depending on type |
| **Special considerations** | Because thermistors are not very accurate, if you replace the thermistor or the electronics then the readings may change by several degC. So you may have to re-learn what temperature indications give you good prints. | To get accurate cold junction compensation, you need to run the twisted pair thermocouple leads all the way from the hot end to the adapter board. So you can't easily include the thermistor connections in a connector for a demountable hot end. | For best accuracy, use a 4-wire connection | Lower resolution than other sensors. |

# General recommendations

* A thermistor is adequate for monitoring bed or chamber temperature
* If you have only one printer and one hot end, a thermistor is normally adequate for the hot end
* If it is important to you that multiple identical printers or multiple hot ends need the same indicated temperatures to provide identical performance, use PT100 or PT1000 sensors
* For temperatures above 300C, use PT100 or PT1000 sensors
* For very high temperatures, use thermocouples
* PT1000 sensors are potentially a good cost and performance compromise between thermistors and PT100 sensors. The thermistor inputs on the Duet 2 Maestro and Duet 3 are optimised to work well with PT1000 sensors.
