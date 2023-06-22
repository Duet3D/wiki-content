---
title: Duet3D  Accelerometer
description: A standalone accelerometer board using the LIS3DH MEMS digital motion sensor. It designed to plug into the SPI Daughterboard header on Duet 2 and 3 mainboards
published: true
date: 2023-06-22T01:09:12.785Z
tags: 
editor: markdown
dateCreated: 2023-06-22T01:09:12.785Z
---

# Introduction

The Duet 3 Toolboard 1LC (v1.1 and later) has a builtin accelerometer however for systems not using a toolboard this small stand alone accelerometer can be used. It connects to the SPI Daughterboard header (also labelled Temp DB on earlier Duets) with the supplied 1m ribbon cable. Acelleromater readings captured can be sued to tune input shaping parameters to avoid ringing artifacts.

# Features

---|---|
| **Accelerometer** | [LIS3DH](https://www.st.com/en/mems-and-sensors/lis3dh.html) |
| **Supply voltage** | 3.3V - Do not connect to 5V it will be damaged |
| **Interface** | SPI Slave - Duet Mainboard is SPI master|
| **Additional details** | See the [Datasheet](https://www.st.com/resource/en/datasheet/lis3dh.pdf)|

## Firmware notes

* Compatible RepRapFirmware versions: RRF 3.4.6 and 3.5beta4 or later

# Physical properties and connections

## Dimensions

[![Diagram showing the outer dimensions and mounting hole for the Duet 3D Accelerometer v0.2](/hardware/accelerometer/duet3d_accelerometer_v0.2_d1.0_dimensions.png =500x)](/hardware/accelerometer/duet3d_accelerometer_v0.2_d1.0_dimensions.png){target=_blank}

## Mounting

The hole in the PSB is 6.1mm so it can be mounte dusing a standard 3d printing nozzle as well as systems like the Revo nozzles:

![Duet3d accelerometer on revo micro](/hardware/accelerometer/duet3d_accelerometer_on_revo_micro.jpg  =400x)

The accelerometer needs to be firmly mounted for data collection so it the nozzle is <6mm (sich as the revo) use a m4 washer between the revo top of heatbreak and the accelerometer PCB. This is not necessary for V6 and similar nozzles.

Note the orientation of the accelerometer board is important the correct data collection and analysis, see the orientation section below.

## 3D model

A STEP 3D model will be published shortly


## Wiring Diagram

[![[diagram showing the pinout for the header on the Duet 3D Accelerometer v0.2](/hardware/accelerometer/duet3d_accelerometer_v0.2_wiring.png =500x)](/hardware/accelerometer/duet3d_accelerometer_v0.2_wiring.png)

