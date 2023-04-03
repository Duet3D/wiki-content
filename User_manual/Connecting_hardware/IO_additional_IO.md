---
title: Adding additional output ports
description: 
published: true
date: 2022-06-29T21:31:28.148Z
tags: 
editor: markdown
dateCreated: 2021-10-26T21:19:55.566Z
---

# Introduction

If you need additional input or output ports, there are a number of options depending on which Duet board you have.

# Duet 3

Additional output ports are provided by connecting CAN-bus expansion ports, for example the [Duet 3 Expansion 3HC](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_3HC), which provides an additional 3x extruder heater outputs (up to 6A each),6x PWM-controlled fan outputs, and 6x on-board I/O connectors.

# Duet 2

## Duet 2 Maestro

There are no I/O expansion options available for the Duet 2 Maestro.

## Duet 2 WiFi/Ethernet

### Duet3D expansion boards

The Duet3D DueX2, DueX5 and Expansion Breakout Board offer additional input/output ports for your Duet 2 WiFi/Ethernet. See [DueX2 and DueX5](/Duet3D_hardware/Duet_2_family/DueX2_and_DueX5) and [Duet Expansion Breakout Board](/Duet3D_hardware/Duet_2_family/Duet_Expansion_Breakout_Board) documentation for details.

### SX1509 expansion board

RepRapFirmware 1.19 and later for the Duet 2 WiFi/Ethernet support expansion of the number of output ports by using an SX1509B chip attached to the I2C bus. This facility is available whether or not you have a DueX2 or DueX5 expansion board connected to your Duet.

You can buy expansion boards based on the SX1509 from Sparkfun and on eBay. Note that the SX1509B is only for relatively slowly changing signals. It is not appropriate for stepper drivers.

Connect the SX1509 expansion board to the I2C pins TWC0 and TWD0 on the Duet. If you have a DueX2/5 connected then they are also available on the GPIO connector, labelled TWC and TWD (NOTE some DueX boards have these incorrectly labelled, check DueX wiring notes).

You must configure the expansion board so that the SX1509B chip is at I2C address 0x71. This is achieved by connecting the two ADDR inputs of the SX1509B chip to Vcc (logic level 1) instead of to ground (logic level 0). Expansion boards based on the SX1509B normally have a traces that can be cut and solder pads that case be bridged to achieve this. Check the SX1509 expansion board is recognised by sending [M115](/User_manual/Reference/Gcodes/M115):

```
FIRMWARE_NAME: RepRapFirmware for Duet 2 WiFi/Ethernet FIRMWARE_VERSION: 3.3beta1 ELECTRONICS: Duet WiFi 1.02 or later + SX1509B expander FIRMWARE_DATE: 2021-02-14 16:00:49
```

In RepRapFirmware 3.x, the pin names to use are sx1509b.0 through sx1509b.15. Define the pin in M950 and control it with [M42](/User_manual/Reference/Gcodes/M42), [M280](/User_manual/Reference/Gcodes/M280) and [M670](/User_manual/Reference/Gcodes/M670).

In RepRapFirmware 2.x, the logical pin numbers to address the SX1509B outputs in [M42](/User_manual/Reference/Gcodes/M42), [M280](/User_manual/Reference/Gcodes/M280) and [M670](/User_manual/Reference/Gcodes/M670) commands are 120 through 135.