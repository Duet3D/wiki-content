---
title: Duet 3 INDX Toolboard
description: The Duet 3 INDX Toolboard controls of all functions of the nozzle-swapping Bondtech INDX toolhead.
published: false
date: 2026-02-09T13:09:09.479Z
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
- 3x2-pin USB/CAN select jumper block. When running RepRapFirmware, do not install any jumnpers in this block. Jumpers should only be inserted when running alternaive firmware that uses USB communication instead of CAN-FD.
- 2-pin CAN_RESET jumper. Only install this if the firmware running on the board is non-functioning. When the board is powered up with this jumper installed, it tells the bootloader to reset the CAN address to default (121) and then fetch and install new firmware even if firmware is already installed.
- 2-pin CAN_TERM jumper. Install this if the board is the last board at one end of the CAN bus.

## LED indications

LEDs are provided to indicate the following:

| Label | Colour | Function |
|--|--|--|
| **VIN** | Blue | Indicates presence of VIN power (VIN should be externally fused) |
| **3.3V** | Green | Indicates presence of 3.3V power from on-board regulator |
| **ACT / LED 1** | Green | Indicates activity on the CAN-FD bus |
| **STATUS / LED 0** | Red | Status LED. See description below |

**Status LED:** In normal use, the red LED flashes slowly (approx 1Hz) in sync with the main board to indicate that it has CAN sync, or flashes continuously and rapidly to indicate that it doesn't. It also flashes startup error codes, for example if the bootloader doesn't find valid firmware on the board. For a list of these error codes see [CAN_connection basics](https://docs.duet3d.com/User_manual/Machine_configuration/CAN_connection#led-behaviour-and-error-codes).

## Software notes
The firmware file for this board is called Duet3Firmware_TOOLINDX.bin.

The bootloader file for this board is called Duet3Bootloader-SAME5x_composite.bin.

The minimum firmware version for this board is 3.7.0-alpha dated 2026-02-09 or later. This applies to the firmware running on the main board too. If older main board firmware is used then some of the functionality may be missing, in particular the heater and the load cell are unlikely to work.

The default CAN address (which is also the CAN address after the reset jumper is used) is 121.

**CAUTION!** The inductive heater is fast and powerful. It can easily heat the nozzle or other metalwork placed inside the heater coil to dangerously high temperatures. Use only the correct firmware versions, and keep the firmware up to date. If the nozzle assemble is not fully inserted into the heater coil or is misaligned, this can result in the temperture being under-read, resulting in heating to a higher temperature than was intended. Do not allow paper or other flaammable material to enter the heater coil area.

## Pin names

For more information on pin names, see [Pin Names](https://docs.duet3d.com/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names).

RepRapFirmware 3 uses pin names for user-accessible pins, rather than pin numbers, to communicate with individual pins on the PCB. In RRF 3 no user-accessible pins are defined at startup by default. Pins can be defined for use by a number of gcode commands, eg M574, M558, M950.

The Duet 3 series uses the pin name format "expansion-board-address.pin-name" to identify pins on expansion board, where *expansion-board-address* is the numeric CAN address of the board. A pin name that does not start with a sequence of decimal digits followed by a period, or that starts with "0." refers to a pin on the Duet 3 Mainboard.

| Function | Pin location | RRF3 Pin name | Notes |
|---|---|---|
| Outputs | FAN (on VF board) | hsfan | Heatsink fan, VIN voltage |
| ^^ | ^^ | hsfan.tach | Pulled up to +5V |
| ^^ | FAN (on MCU board) | out1 | Intended for print cooling fan,  VIN voltage |
| ^^ | ^^ | out1.tach | Pulled up to +5V |
| ^^ | LED | led | 5V drive for WS2812 or similar LED strings |
| Inputs | IO_0 | io0 .in | 3.3V power, 30V tolerant |
| ^^ |  | boardtemp | MCU board temperature |
| ^^ | Coil FFC | coiltemp | Scanning Z probe coil temperature |

### Configuring the induction heater and IR sensor

The thermopile sensor is configured using the M308 command with sensor type `"thermopile_tpis.object"` and pin name `"i2c"`. As well as the main output which provides nozzle temperature, it has two additional outputs, which may be used for monitoring. Auxiliary output 1 has type `"thermopile_tpis.ambient"` and is the ambient temperature reported by the thermopile sensor. Auxiliary output 2 has type `"thermopile_tpis.environment"` and is the temperature of the nozzle surround reported by the auxiliary thermistor.

The inductive heater is configured using the M950 command with pin name "nozzleheat". The temperature sensor number in the M950 command but refer to the thermopile sensor primary output.

Example configuration, using sensor #4 for the nozzle temperature and heater #1, and the default CAN address (121):

```
M308 S4 Y"thermopile_tpis.object" P"121.i2c" A"Thermopile" T100000 B4311   ; configure thermopile main output
M308 S5 Y"thermopile_tpis.ambient" P"121.S4.1" A"Thermopile ambient"       ; configure thermopile ambient output (optional)
M308 S6 Y"thermopile_tpis.ambient" P"121.S4.2" A"Hot end surround"         ; configure nozzle environment output (optional)
M950 H1 C"121.nozzleheat" T4

```

