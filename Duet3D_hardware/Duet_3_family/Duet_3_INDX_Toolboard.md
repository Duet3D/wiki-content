---
title: Duet 3 INDX Toolboard
description: The Duet 3 INDX Toolboard controls of all functions of the nozzle-swapping Bondtech INDX toolhead.
published: false
date: 2026-02-09T10:22:49.466Z
tags: 
editor: markdown
dateCreated: 2026-02-09T09:34:17.141Z
---

# INDX Tool Board

INDX is a trademark of Bondtech [TODO choose the correct wording here]

## Hardware notes

The INDX tool board comprises two PCBs connected by two 20-way FFCs (Flexible Flat Cables). These will normally be supplied ready-mounted on a tool head.

The VF board is connected to the induction heater, IR temperature sensor, heatsink fan, and load cell. Do not make any other connectons to the VF board, or remove the existing connections.

The MCU board is connected to the rest of a Duet/RepRapFirmware system using a single XT30 2+2 connector. This provides power to the board (thick red and black wires, positive and ground respectively) and CAN (yellow and white wires, CANH and CANL respectively).

The board requires 24V nominal power, fused externally at 3A or 5A. A Duet 3 Tool Distribution Board is recommended to be used because it includes the necessary fuse and simplifies wiring. If the INDX is the only CAN-connected expansion in your system then you can use a direct CAN connection to the main board instead and an inline auto fuse in the positive supply wire.

The MCU board also provides the following connections:
- 4-pin connector for the stepper motor in the INDX toolhead
- 3-pin JST PA connector for the print cooling fan, with optional tacho
- 3-pin IO0 connector, for an endstops or other device
- 3 Pin connector for WS2812 (aka Neopixel) or similar LEDs
- 4-pin FFC connector for optional inductive sensor coil
- 5-pin USB OUT connector. This is not used when running RepRapFirmware.

[Diagram to be provided here]

### Features
- Supports induction heater, thermopile sensor, load cell and heatsink fan integrated with INDX toolhead
- Supports INDX tool head extruder motor, with closed loop option if a suitable diametrically-magnetised magnet is attached to the end of the motor shaft
- RGB LEDs, visible via light pipes in the INDX toolhead
- Supports connection of a print cooling fan with optional tacho
- WS2812 or similar LED string support
- Supports connection of sensor coil for scanning inductve sensor
- On-board accelerometer
- Uncommitted input for endstop or similar

### Connecting the 20-way FFCs

If you need to disconnect and reconnect the FFCs linking the two boards, be aware of the following:
- Be sure to place the contact side of the FFC against the contacts in the socket
- Be sure to insert the cable straight into the middle of the connector. It is easy to insert a cable so that it is skewed and shorts the pins out. If you do this and then power up the board, it is likely to be damaged.
- The latching mechanisms on the vertical FFCs on the MCU board are counter-intuitive. Thet are unlatched when the latch is in the up position. After inserting the FFCs, push the latch down towards the PCB to lock the FFC in place.

### Jumpers
The following jumper blocks are provided:
- 3x2-pin USB/CAN select jumper block. When running RepRapFirmware, do not install any jumnpers in this block. Jumpers should only be inserted when running alternaive firmwarer that uses USB communications instead of CAN-FD.
- 2-pin CAN_RESET jumper. Only install this if the firmware running on the board is non-functioning. When the board is powered up with this jumper installed, it tells the bootloader to reset the CAN address to default (121) and then fetch and install new firmware even if firmware is already installed.
- 2-pin CAN_TERM jumper. Install this if the board is the last board at one end of the CAN bus.

## Software notes
The firmware file for this board is called Duet3Firmware_TOOLINDX.bin.

The bootloader file for this board is called Duet3Bootloader-SAME5x_composite.bin.

### Pins available for use in RepRapFirmware configuration commands:

TODO

### Configuring the induction heater and IR sensor

TODO


