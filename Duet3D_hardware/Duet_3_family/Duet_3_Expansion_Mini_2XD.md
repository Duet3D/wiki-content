---
title: Duet 3 Expansion Mini 2XD
description: The Duet 3 Expansion Mini 2XD is an expansion board designed to connect two external drivers to the Duet 3 mini 5+ mainboard.
published: true
date: 2025-07-25T16:27:40.851Z
tags: 
editor: markdown
dateCreated: 2025-07-25T16:27:40.851Z
---



# Introduction

The Duet 3 Expansion Mini 2XD is specifically designed for the expansion header of the [Duet 3 Mini 5+](/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Hardware_Overview). It may be possible to use it with other boards but this is not supported. It provides 2 external motor driver channels that match those on the Duet 3 6XD.

# Features

## Hardware specification

The main hardware features of the Duet 3 Expansion Mini 2XD are listed below.

| HARDWARE SPECIFICATION ||
|:---|:---|
| **External Driver Support** | 2 ports with step/direction/enable outputs, and fault inputs. Signalling is 5V single ended. |
| **Other I/O** | NIL |
| **Connection** | Directly mounted on the Duet 3 Mini 5+ Mainboard. |

## Operating limits

|---|---|
| **Input power voltage** | 11V to 25V (Via the Duet 3 Mini 5+ expansion header) |
| **Fuses** | No on board fuses, shared V_FUSED protection from  Duet 3 Mini 5+ |
| **Maximum ambient temperature** | 70°C |

## Firmware notes

Compatible RepRapFirmware versions: RRF 3.6.1 and later on the Duet 3 Mini 5+ mainboard. There is no firmware loaded directly onto the Duet 3 Expansion Mini 2XD itself.

The drivers are numbered "5" and "6" to reference them, for example to assign them as the X and Y axis:

`M584 X5 Y6`

use M569 to set them up with appropriate timing chraracteristics for the external driver:

`M569 P5 T2:2:2:2`
`M569 P6 T2:2:2:2`

# Physical properties

## Dimensions
![duet3_eb_mini2xd_v0.1_d1.0_dimensions.png](/duet_boards/duet_3_mini_5_plus/duet3_eb_mini2xd_v0.1_d1.0_dimensions.png =400x)

## Mounting

The Duet 3 Expansion Mini 2XD has mounting standoff holes that align with matching holes on the Duet 3 Mini 5+.

These pictures show the Mini 2+ mounted but the procedure is the same of the Mini 2XD
![duet_3_mini_2+_front.png](/duet_boards/duet_3_mini_5_plus/duet_3_mini_2+_front.png =250x) ![duet_3_mini_2+_mounting_01.png](/duet_boards/duet_3_mini_5_plus/duet_3_mini_2+_mounting_01.png =250x) ![duet_3_mini_2+_mounting_02.png](/duet_boards/duet_3_mini_5_plus/duet_3_mini_2+_mounting_02.png =250x)

Nylon standoffs are supplied with the Duet 3 Expansion Mini 2XD to mount it securely.

* 11mm tall for 1.6mm PCB with 2.5mm holes -e.g. FIX-MADA-11 or TRDLCBST-7-01

# Physical connections

## Wiring diagram
[![duet3_eb_mini2xd_v0.1_d1.0_wiring.png](/duet_boards/duet_3_mini_5_plus/duet3_eb_mini2xd_v0.1_d1.0_wiring.png =500x)](/duet_boards/duet_3_mini_5_plus/duet3_eb_mini2xd_v0.1_d1.0_wiring.png){target=_blank}

## Connecting External Motor Drivers

The Duet 3 Expansion Mini 2XD supports directly connecting external stepper drivers that have opto-isolated or similar inputs. Note that:
* The **Step** and **Dir** outputs from the 2XD are either low (when "on") or floating/high-impedance when "off". 
  * The 2XD will generate negative-going step pulses, and this will only work if the driver samples steps on the leading edge of the pulse. For stepstick and similar drivers, that will depend on the driver chip. Otherwise, add an inverter between the step output and the driver.
  * Direction can be flipped using M569 S parameter.
* The **Enable** output is either low when "on" and high impedance when "off", or vice versa, depending on the position of the 'Driver Enable Polarity' (En_Pol) jumper.
  * The jumper sets the default value in case a M569 command with R parameter isn't seen. The main aim of this is to power up with the drivers disabled.
  * The `M569 R#` parameter overrides the jumper. 
  **R0** = signal not energised to enable the driver. On v1.01 and earlier boards this means high impedance; on v1.02 this means pulled to +5V. 
  **R1** = signal energised to enable the driver. This means the output is pulled to low impedence.

This diagram shows connection to a "typical" optoisolated stepper motor driver, it is the same as on the 6XD.

[![Connection of Driver 0 on the Duet 3 MB 6XD to a "typical" optoisolated stepper motor driver, note the connection is the same as on the 2XD](/duet_boards/duet_3_mb6xd/duet3_mb_6xd_v1.0_later_dvr_conn.png =400x)](/duet_boards/duet_3_mb6xd/duet3_mb_6xd_v1.0_later_dvr_conn.png){target=_blank} [![duet_3_mb6xd_ext_driver.jpg](/duet_boards/duet_3_mb6xd/duet_3_mb6xd_ext_driver.jpg =400x)](/duet_boards/duet_3_mb6xd/duet_3_mb6xd_ext_driver.jpg){target=_blank}

To connect a Pololu/StepStick/similar driver to the 2XD, a 10K pullup resistor is needed from each of the Step/Dir/En lines to +5V. This diagram shows connection to a stepper motor driver that has +5V inputs and a common ground.

[![Connection of Driver 0 on the Duet 3 MB 6XD to a stepper motor driver with +5V inputs and common ground, note the connection is the same as on the 2XD](/duet_boards/duet_3_mb6xd/duet3_mb_6xd_v1.0_later_dvr_conn_2.png =400x)](/duet_boards/duet_3_mb6xd/duet3_mb_6xd_v1.0_later_dvr_conn_2.png){target=_blank}

For firmware configuration, including mapping drives to axes, configuring enable polarity, and configuring step timing, see the [Connecting external stepper and servo drivers](/User_manual/Connecting_hardware/Motors_connecting_external) wiki page.

# Revision History

# Tabs{.tabset}
## Revision 0.1

First version
