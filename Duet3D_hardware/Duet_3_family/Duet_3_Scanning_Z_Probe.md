---
title: Duet 3 Scanning Z Probe
description: The Duet 3 Scanning Z probe allows for quick inductive scans of metallic bed surfaces to build a point mesh of the surface to be used for mesh bed compensation.
published: true
date: 2023-11-05T11:50:23.699Z
tags: 
editor: markdown
dateCreated: 2023-11-05T11:50:23.699Z
---

![duet_3_szp_with_12mm_coil.jpg](/duet_boards/duet_3_can_expansion/duet_3_szp/duet_3_szp_with_12mm_coil.jpg =600x)

# Introduction

The Duet 3 Scanning Z probe is a CAN-FD connected inductive sensing board, with compatible inductive PCB coils. It allows for a point mesh of the bed to be built up quickly as no movement in Z is required to read the bed distance, and individual readings happen very quickly. An accelerometer has been added to the PCB as well to allow for the SZP board to also be used for input shaping calibration.

![image_792points.png](/duet_boards/duet_3_can_expansion/duet_3_szp/image_792points.png =800x)
Example mesh of 792 points that took ~20seconds to produce.


# Features

## Hardware specification

|---|---|
| **Processor** | [ATSAMC21G18A](https://www.microchip.com/en-us/product/ATSAMC21G18A) |
| **Processor features** | 32-bit, 48MHz ARM Cortex M0+, 256KB Flash, 32KB RAM |
| **Networking/Comms** | CAN-FD interconnect to Duet 3 Tool Distribution Board or Duet 3 Mainboard; serial port |
| **Scanning IC** | [LDC1612](https://www.ti.com/product/LDC1612) |
| **Accelerometer** | [LIS2DW12](https://www.st.com/en/mems-and-sensors/lis2dw12.html)  |
| **Power monitoring** | VIN voltage reporting |
| **Input power voltage** | 5V |
| **Power Consuption** | <200mA |

## Firmware notes

* Compatible RepRapFirmware versions: RRF 3.5 or later

<!--- Upsate with links
## Open source

Importantly Duets are Open:

* The Duets are Open Hardware, see [our license here](https://github.com/Duet3D/Duet3-Mainboard-6HC/blob/master/LICENSE).
* All hardware [source files](https://github.com/Duet3D/Duet3-SZP) are available on Github.
* Both the [Duet Web Control](https://github.com/Duet3D/DuetWebControl) web interface and [RepRapFirmware](https://github.com/Duet3D/RepRapFirmware) are [Open Source Software]( http://www.gnu.org/licenses/gpl-3.0.en.html) with source files available and actively maintained, see [Contributing to development](/User_manual/Reference/Developers) for more information.
* The Duet hardware and RepRapFirmware are built with Open tools: designed in [KiCad](http://kicad.org/) and [Eclipse](https://eclipse.org/) using open tools means the barrier to getting involved is as low as possible.
--->

# Physical properties

## Dimensions

There are three M2 mounting holes and overall dimensions are 38mmx20mm

[![Diagram showing the dimensions and mounting holes for the Duet 3 SZP v1.0](/duet_boards/duet_3_can_expansion/duet_3_szp/szp_dimensions.png)](/duet_boards/duet_3_can_expansion/duet_3_szp/szp_dimensions.png){target=_blank}

<!---
### 3d Model
The STEP file is available [on Github here](https://github.com/Duet3D/Duet3-Toolboard-1LC/blob/master/Toolboard_1LC_v1.3/Duet3_TB_1LC_v1.3_STEP.zip){target=_blank}.
--->

## Coil dimensions

## Tabs {.tabset}

### 12mm Coil

![Diagram showing the outer dimensions of the 12mm Coil](/duet_boards/duet_3_can_expansion/duet_3_szp/12mm.png =500x)

<!---
The STEP file is available [on Github here](https://github.com/Duet3D/Duet3-Toolboard-1LC/blob/master/Toolboard_1LC_v1.3/Duet3_TB_1LC_v1.3_STEP.zip){target=_blank}.
--->

### 15mm Coil

![Diagram showing the outer dimensions of the 15mm Coil](/duet_boards/duet_3_can_expansion/duet_3_szp/15mm.png =500x)

<!---
The STEP file is available [on Github here](https://github.com/Duet3D/Duet3-Toolboard-1LC/blob/master/Toolboard_1LC_v1.3/Duet3_TB_1LC_v1.3_STEP.zip){target=_blank}.
--->

## Mounting

The SZP sensor board can be mounted anywhere within reach of the supplied FFC cable (100mm)

Here is an example with it mounted on a Mini Stealth Burner tool on a Voron:
![tridex_mount4.jpg](/duet_boards/duet_3_can_expansion/duet_3_szp/tridex_mount4.jpg =400x)



# Physical connections

## Wiring diagram

*To follow*

<!---
## Wiring notes

* **JST ZH:** (CAN, v1.0 headers) These are difficult to crimp because they are so small, so Duet3D supply pre-terminated connectors. You can connect longer wires to them by hand soldering or using small size (white) solder sleeves.
**NOTE:** Some of the supplied JST ZH pre-terminated connectors may have a different order for the colours of the wires! Please check your wiring before powering on.
* **Screw terminals:** These are not high current so fitting the wires directly into the screw terminals is fine. Using small ferrules is also fine; Duet3D supply 0.5mm^2 white ferrules.
* We recommend connecting the hot end metalwork to ground on the toolboard via a resistor (10k to 1M Ohm), if it is not grounded through its mounting system. This will prevent the hot end building up static charge, which might otherwise occur as filament is extruded and may then flash over to the tool board thermistor input, causing damage.
* On the v1.3 toolboard the mounting screw holes are all grounded via 100K resistors to provide a path to ground for any static build up in the extruders, assuming the toolboard is mounted to the extruder.
--->
## Description of Connections

Duet 3 Scanning Z probe has the following connectors:

| Connector | Label | Function |
|--
| 4-pin JST PA | GND, 5V, CAN_L, CAN_H| +5V power and CAN|
| 4-pin 0.5mm pitch FFC | Temp, VSSA, COILA, COILB | Connection for FFC cable to coil|


## LED indications

LEDs are provided to indicate the following:

| Label | Colour | Function |
|--|--|--|
| **ACT ** | Green | Indicates activity on the CAN-FD bus |
| **STATUS** | Red | Status LED. See description below |

**Status LED:** In normal use, the red LED flashes slowly in sync with the main board to indicate that it has CAN sync, or flashes continuously and rapidly to indicate that it doesn't. It also flashes startup error codes, for example if the bootloader doesn't find valid firmware on the board. For a list of these error codes see [CAN_connection basics](https://docs.duet3d.com/User_manual/Machine_configuration/CAN_connection#led-behaviour-and-error-codes).

## CAN

Connect the 4-pin CAN and power connector to a source of 5V (e.g. an IO port on the Duet 3 mainboard), connect the CAN to the CAN-FD bus. If just using an SZP and a mainboard with no tool boards or other Duet 3 expansion boards CAN can be connected directly to the CAN port on the mini 5+ or other mainboard.

#### Terminating resistor

There is a solderable jumper on the back of the SZP to set the termination resistor, if it is to be the last board on the CAN-Fd bus.
![termination_resistor.png](/duet_boards/duet_3_can_expansion/duet_3_szp/termination_resistor.png =200x)


# Commissioning

All boards in the system must have different CAN addresses. SZPs are shipped set to a default CAN address of 120. If you ave more that one SZP on a bus, **only one of them must be powered up and connected to the CAN bus initially until a new address is set.**


## Factory Reset

The board will do a factory reset if you power it up with the CAN RESET button held down. The CAN address will be reset to the default (120), the CAN bus timing will also be reset to default (1Mbps), and the bootloader will request a firmware update.

![can_reset.png](/duet_boards/duet_3_can_expansion/duet_3_szp/can_reset.png =200x)


## Startup Time

It is recommended to add the following to config.g, before any commands that reference any CAN bus connected expansion boards

`G4 S2 ; wait for expansion boards to start`

## Testing communication

Check that you can communicate with the SZP, by sending 

`M115 B120`


## Update the bootloader

Duet 3 expansion boards and tool boards have a bootstrap loader written to the start of flash so that they can load firmware from the main board via CAN. This bootloader may occasionally need to be updated in order to support new features. See [Updating the bootloader on Duet 3 expansion and tool boards](/User_manual/RepRapFirmware/Updating_bootloader).

## Updating the firmware

The firmware filename is Duet3Firmware_SZP.bin and this needs to be uploaded to the /firmware folder of the SD card on the attached SBC, or the SD card in the Duet 3 main board if it is running in standalone mode.

Update the firmware by using the M997 B# command, where # is the CAN address of the new board.

## Set the CAN address

* Send command M115 B# to verify that the main board can communicate with the SZP, where # is the original CAN address (normally 120)
* Send command M952 B# A## where ## is the new address you want to use, e.g. M952 B120 A100.
* Power the system down and up again, or send M999 B120. This will cause the SZP to restart with the new address.
* Send command M122 B100 (or whatever address you chose) to verify that you can communicate with the SZP at its new address
* You can now power up the next SZP and commission it in the same way if you have moe than one in the system


## Accelerometer

For an overview of using accelerometers to capture data on axis movement see: [Connecting an accelerometer](/User_manual/Connecting_hardware/Sensors_Accelerometer)

### Orientation

![accelerometer_szp.png](/duet_boards/duet_3_can_expansion/duet_3_szp/accelerometer_szp.png =500x)

See [M955](/User_manual/Reference/Gcodes/M955) for how to setup and configure the accelerometer, including its orientation in relation to the printer XYZ axis. 

# PCB Revision History

# Tabs{.tabset}

## PCB revision v1.0

* Changed to LIS2DW12 accelerometer
* Added coil connecter test pads for ATE
* Changed to a 4 pin FFC from 6 pin on v0.1
* Silkscreen improvements

## PCB revision v0.1

initial prototype, internal testing only

