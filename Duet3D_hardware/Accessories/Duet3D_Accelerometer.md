---
title: Duet3D Accelerometer
description: A standalone accelerometer board using the LIS2DW12 MEMS digital motion sensor. It designed to plug into the SPI Daughterboard header on Duet 2 and 3 mainboards
published: true
date: 2026-08-10T11:06:36.913Z
tags: 
editor: markdown
dateCreated: 2023-06-22T01:09:12.785Z
---

# Introduction

Various Duet 3 toolboards have a builtin accelerometer. However for systems not using a toolboard, this small stand alone accelerometer can be used. It connects to the SPI Daughterboard header (also labelled Temp DB on earlier Duets) with the supplied 1m ribbon cable. Accelerometer readings captured can be used to tune input shaping parameters to avoid ringing artefacts.

# Features

| | Duet3D Accelerometer v0.2 | Duet3D Accelerometer v1.0 |
|---|---|---|
| **Accelerometer** | [LIS3DH](https://www.st.com/en/mems-and-sensors/lis3dh.html){target=_blank} | [LIS2DW12](https://www.st.com/en/mems-and-sensors/lis2dw12.html){target=_blank} |
| **Supply voltage** | 3.3V - Do not connect to 5V it will be damaged ||
| **Interface** | SPI Slave - Duet Mainboard is SPI master||
| **LED** | There is a green LED on the board to indicate power is applied ||
| **Maximum ambient temperature** | 85°C ||
| **Additional details** | [LIS3DH datasheet](https://www.st.com/resource/en/datasheet/lis3dh.pdf){target=_blank} | [LIS2DW12 datasheet](https://www.st.com/resource/en/datasheet/lis2dw12.pdf){target=_blank} |

## Firmware notes

* **Duet3D Accelerometer v0.2** is compatible RepRapFirmware v3.4.6 or v3.5beta4 and later
* **Duet3D Accelerometer v1.0** is compatible RepRapFirmware v3.5.0-rc.2 and later

# Physical properties and connections

## Dimensions

[![Diagram showing the outer dimensions and mounting hole for the Duet 3D Accelerometer v0.2](/hardware/accelerometer/duet3d_accelerometer_v0.2_d1.0_dimensions.png =500x)](/hardware/accelerometer/duet3d_accelerometer_v0.2_d1.0_dimensions.png){target=_blank}

## Mounting

The hole in the PCB is 6.1mm so it can be mounted using a standard 3d printing nozzle as well as systems like the Revo nozzles:

![Duet3d accelerometer on revo micro](/hardware/accelerometer/duet3d_accelerometer_on_revo_micro.jpg =400x)

The accelerometer needs to be firmly mounted for data collection so it the nozzle is <6mm (such as the revo) use a m4 washer between the flanged top of the revo heatbreak and the accelerometer PCB. This is not necessary for V6 and similar nozzles.

In the case of the revo the spring holding the heater core against the end of the nozzle is not sufficent to hold the accelerometer PCB firmly in position by itself, hence the requirement to mount it using an M4 wahser and the flange of the nozzle.

Note the orientation of the accelerometer board is important the correct data collection and analysis, see the orientation section below.

> **WARNING: Do not heat the hotend with the accelerometer mounted using the nozzle.**
Manually set the active and standby temperature to 0°C before using the IS plugin.
{.is-danger}

## 3D model

A STEP 3D model is [available on github.](https://github.com/Duet3D/Duet3D-Accelerometer/tree/main/Duet3D%20Accelerometer%20v0.2){target=_blank}

## Wiring Diagram

[![diagram showing the pinout for the header on the Duet 3D Accelerometer v0.2](/hardware/accelerometer/duet3d_accelerometer_v0.2_wiring.png =400x)](/hardware/accelerometer/duet3d_accelerometer_v0.2_wiring.png)

## Wiring notes

The accelerometer comes with a 1m long 10 way IDC cable that plugs directly into the SPI DB header on Duet mainboards. Ensure the connector orentation is correct so pin1 on the IDC goes to pin 1 on the mainboard:

![Duet3D accelerometer connected to the Duet 3 6HC mainboard](/hardware/accelerometer/duet3d_accelerometer_6hc.jpg =400x)
Duet3D accelerometer connected to the Duet 3 6HC mainboard

![Duet3D accelerometer connected to the Duet 3 Mini5+ mainboard](/hardware/accelerometer/duet3d_accelerometer_mini5+.jpg =400x)
Duet3D accelerometer connected to the Duet 3 Mini5+ mainboard

# Orientation

The I (orientation) parameter tells the firmware which of the 24 possible orientations the accelerometer chip is in relative to the printer axes. It is expressed as a 2-digit number. The first digit specifies which machine direction the Z axis of the accelerometer chip (usually the top face of the chip) faces, as follows: 0 = +X, 1 = +Y, 2 = +Z, 4 = -X, 5 = -Y, 6 = -Z. The second digit expresses which direction the X axis of the accelerometer chip faces, using the same code. If the accelerometer chip axes line up with the machine axis, the orientation is 20. This is the default orientation if no orientation has been specified.

In all the images below the image of the accelerometer is shown as if stood in front of a "normal" machine where X+ is to the right and Y+ is towards the back

| Accelerometer axis to machine axis |||||||
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| | +X to +X | +X to +Y | +X to +Z | +X to -X | +X to -Y | +X to -Z |
| +Z to +X | NA | ![](/hardware/accelerometer/orientation/01.png){target=_blank}<br>`M955 P[##] I01` | ![](/hardware/accelerometer/orientation/02.png){target=_blank}<br>`M955 P[##] I02`| NA | ![](/hardware/accelerometer/orientation/05.png){target=_blank}<br>`M955 P[##] I05` | ![](/hardware/accelerometer/orientation/06.png){target=_blank}<br>`M955 P[##] I06` |
| +Z to +Y | ![](/hardware/accelerometer/orientation/10.png){target=_blank}<br>`M955 P[##] I10` | NA | ![](/hardware/accelerometer/orientation/12.png){target=_blank}<br>`M955 P[##] I12` | ![](/hardware/accelerometer/orientation/14.png){target=_blank}<br>`M955 P[##] I14` | NA | ![](/hardware/accelerometer/orientation/16.png){target=_blank}<br>`M955 P[##] I16` |
| +Z to +Z | ![](/hardware/accelerometer/orientation/20.png){target=_blank}<br>`M955 P[##] I20` | ![](/hardware/accelerometer/orientation/21.png){target=_blank}<br>`M955 P[##] I21` | NA | ![](/hardware/accelerometer/orientation/24.png){target=_blank}<br>`M955 P[##] I24` | ![](/hardware/accelerometer/orientation/25.png){target=_blank}<br>`M955 P[##] I25` | NA |
| +Z to -X | NA | ![](/hardware/accelerometer/orientation/41.png){target=_blank}<br>`M955 P[##] I41` | ![](/hardware/accelerometer/orientation/42.png){target=_blank}<br>`M955 P[##] I42` | NA | ![](/hardware/accelerometer/orientation/45.png){target=_blank}<br>`M955 P[##] I45` | ![](/hardware/accelerometer/orientation/46.png){target=_blank}<br>`M955 P[##] I46` |
| +Z to -Y | ![](/hardware/accelerometer/orientation/50.png){target=_blank}<br>`M955 P[##] I50` | NA | ![](/hardware/accelerometer/orientation/52.png){target=_blank}<br>`M955 P[##] I52` | ![](/hardware/accelerometer/orientation/54.png){target=_blank}<br>`M955 P[##] I54` | NA | ![](/hardware/accelerometer/orientation/56.png){target=_blank}<br>`M955 P[##] I56` |
| +Z to -Z | ![](/hardware/accelerometer/orientation/60.png){target=_blank}<br>`M955 P[##] I60` | ![](/hardware/accelerometer/orientation/61.png){target=_blank}<br>`M955 P[##] I61` | NA | ![](/hardware/accelerometer/orientation/64.png){target=_blank}<br>`M955 P[##] I64` | ![](/hardware/accelerometer/orientation/65.png){target=_blank}<br>`M955 P[##] I65` | NA |

# Configuration and use

See the [connecting an accelerometer](/User_manual/Connecting_hardware/Sensors_Accelerometer) page for details on how to configure and use the accelerometer

See the [input shaping](/User_manual/Tuning/Input_shaping) page for details on how to use input shaping.

# Revision History

# Tabs{.tabset}

## Revision 1.0

* LIS3DH accelerometer replaced with LIS2DW12

## Revision 0.2

* Initial version
