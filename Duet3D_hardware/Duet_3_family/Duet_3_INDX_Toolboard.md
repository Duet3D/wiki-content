---
title: Duet 3 INDX Toolboard
description: The Duet 3 INDX Toolboard controls of all functions of the nozzle-swapping Bondtech INDX toolhead.
published: false
date: 2026-02-09T10:05:33.302Z
tags: 
editor: markdown
dateCreated: 2026-02-09T09:34:17.141Z
---

# INDX Tool Board

## Hardware notes

The INDX tool board comprises two PCBs connected by two 20-way FFCs (Flexible Flat Cables). These will typiclly be supplied ready-mounted on a tool head.

The VF board is connected to the induction heater, IR temperature sensor, heatsink fan, and load cell. Do not make any other connectons to the VF board, or remove the exisitng connections.

The MCU board is connected to the rest of a Duet/RepRapFirmware system using a single XT30 2+2 connector. This provides power to the board (thick red and black wires, positive and ground respectively) and CAN (yellow and white wires, CANH and CANL respectively).

The board requires 24V nominal power, fused externally at 3A or 5A. A Duet 3 Tool Distribution Board is recommended to hbe used because it includes the necessary fuse and simplifies wiring. If the INDX is the only CAN-connected expansion in your system then you can use a directl CAN connection to the main board instead and an inline auto fuse in the positive supply wire.

The MCU board also provides the following connections:
- 4-pin connector for the stepper motor
- 3-pin JST PA connector for the print cooling fan with optional tacho
- 3-pin IO0 connector, for an endstops or other device
- 3 Pin connector for WS2812 (aka Neopixel) or similar LEDs
- 4-pin FFC connector for optional inductive sensor coil
- 5-pin USB OUT connector. This is not used when running RepRapFirmware.

### Connecting the 20-way FFCs

If you need to disconnect and reconnect the FFCs linking the two boards, be aware of the following:
- Be sure to place the contact side of the FFC against the contacts in the socket
- Be sure to insert the cable straing into the middle of the connector. It is easy to insert a cable so that it is skewed and shorts the pins out. IUf you do this and then power up the board, it is likely to be damaged.
- The latching mnechanisms on the vertical FFCs on the MCU board are counter-intuitive. Thet are unlatched when the latch is in the up position. After inserting the FFCs, push the latch down towards the PCB to lock the FFC in place.

### Jumpers
The following jumper blocks are provided:
- 3x2-pin USB/CAN select jumper block. When running RepRapFirmware, do not install any jumnpers in this block. Jumpers should only be inserted when running alternaive firmwarer that uses USB communications instead of CAN-FD.
- 2-pin CAN_RESET jumper. Onlt install this if the firmware running on the board is non-functioning. When the board is powered upnwoth this jumper installed, it tells the bootloader to reset the CAN address to default (121) and then fetch and install new firmware even if firmware is already installed.
- 2-pin CAN_TERM jumper. Install this is the board is the last board at one end of the CAN bus.

## Software notes