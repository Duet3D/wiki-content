---
title: Duet3D Roto Filament Monitor
description: 
published: true
date: 2024-07-02T13:16:16.642Z
tags: 
editor: markdown
dateCreated: 2024-07-02T13:06:07.052Z
---

![Image of the Duet 3 Roto Magnetic Filament Monitor assembly, a blue PCB mounted on a black housing](/hardware/roto_filament_monitor/duet_3_rfm_01_xs.png)

# Introduction

The Duet3D filament monitors sense filament movement and so can indicate that filament has run out, jammed or has been ground in the extruder. In addition skipping extruder motor steps can be detected. This version is a derivative of the [Duet3D Magnetic Filament Monitor](/Duet3D_hardware/Accessories/Rotating_Magnet_Filament_Monitor), shrunk down and designed to fit in the sensor module slot on the E3D Revo Roto.  It uses a rotating magnet to very accurately detect filament movement and is  mechanically very tightly coupled to the Revo Roto extruder. Electronically it is directly connected to the [Duet 3 Roto Toolboard](/Duet3D_hardware/Duet_3_family/Duet_3_Roto_Toolboard).


# Hardware


|---|---|
| **Magnetic Encoder** | [AS5601](https://ams.com/as5601)  |
| **Comms** | Direct I2C connection with the Roto Toolboard processor |
| **Input power voltage** | 3.3V|
| **Max Ambient Temperature** | 75C, limited by the Roto Toolboard |
| **LEDs** | Red and Green LEDs for state indication |
| **Button** | Button to trigger macros |

![Annotated image of the Roto Filament Monitor showing the location of the action button and the LEDs](/hardware/roto_filament_monitor/rotofilamentmonitor_0.2_description.png  =400x)


# Mounting

![The Duet3 Roto filament monitor mounted in the E3D Revo Roto extruder, showing the Duet3 Roto Toolboard on the front of the extruder and the FFC connection between the filament monitor and the toolboard.](/hardware/roto_filament_monitor/duet3_rfm_mounted_s.jpg)


The monitor is installed directly into the E3D Revo Roto sensor slot, replacing the filament presence sensor (if fitted). 


### Preparation

Follow the [E3D documentation](https://e3d-online.com/pages/roto-assembly-guide) to remove the heatsink cooling can and the existing sensor module if fitted.

[Mount Roto toolboard](/Duet3D_hardware/Duet_3_family/Duet_3_Roto_Toolboard#mounting) to the Revo Roto extruder if not already fitted.

### FFC to Roto Filament Monitor

Fit the Flat Flexible Cable (FFC) to the Roto MFM connecter:
* **VERY Carefully** open the connector by sliding the plastic lock out from the connector by ~1mm. This is very easy to break and takes very little force to open
* Place the FFC connector into the connector, contacts facing down
* Gently slide the plastic lock back into place

![Three images showing the Duet3 Roto Filament monitor with the FFC connector open, and the FFC cable fitted](/hardware/roto_filament_monitor/duet3_rfm_ffc_fitting_s.jpg)

### FFC to Roto Toolboard

Following the same process to open the Roto FFC connector carefully, connect the other end of the FFC to the I2C header on the roto toolboard, contacts facing down:

![duet3_rfm_ffc_roto_toolboard_fitting.jpg](/hardware/roto_filament_monitor/duet3_rfm_ffc_roto_toolboard_fitting.jpg)


### Fit the Filament Monitor to the Revo Roto

Insert the Roto FM into the sensor slot, so the FFC will forms an "S" bend.

![duet3_rfm_roto_fitting.jpg](/hardware/roto_filament_monitor/duet3_rfm_roto_fitting.jpg)

Complete the reassembly of the E3D Revo Roto, referring to the e3d documentation as required.

![duet3_rfm_roto_fitting2.jpg](/hardware/roto_filament_monitor/duet3_rfm_roto_fitting2.jpg)

# Wiring

The FFC connector can only fit correctly with the contacts down. do not use a longer FFC than supplied (30mm)

![rotofilamentmonitor_0.2_wiring_d1.0.png](/hardware/roto_filament_monitor/rotofilamentmonitor_0.2_wiring_d1.0.png)



# Configuration

### Configuring the Filament Monitor

The filament monitor virtual pin name is "<board_id>.i2c.mfm" so a typical configuration command would be:

`M591 D0 P3 C"121.i2c.mfm" E3 S1 L25.2 R70:120`

For further information on Filament Monitor firmware configuration, commissioning and troubleshooting, see [Connecting and configuring filament-out sensors](/User_manual/Connecting_hardware/Sensors_filament).

### Configuring the action button

The filament monitor action button virtual pin name is "<board_id>.i2c.mfm.button" so a typical configuration command would be:

`M950 J2 C"121.i2c.mfm.button"`

That would set the button up as input #2. you can then use that input to trigger a macro as described here:

https://docs.duet3d.com/en/User_manual/Tuning/Triggers

So for example, this button mapped to input #2 can then be used to trigger a macro:
`M581 P2 S0 T2 R2    ; trigger2.g will be called by the Roto MFM button only when a file is not printing`

and trigger2.g can contain the logic required for loading filament.

# Revisions

# Tabs {.tabset}

## Revision 0.2

Initial release.