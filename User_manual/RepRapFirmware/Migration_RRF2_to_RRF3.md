---
title: Migration from RRF2 to RRF3
description: RepRapFirmware 3 is the next generation of the leading 32-bit 3D printer firmware, developed by Duet3D and derived from the RepRapFirmware code base developed by Adrian Bowyer.
published: true
date: 2022-10-09T10:10:23.705Z
tags: 
editor: markdown
dateCreated: 2021-11-29T20:21:14.611Z
---

# Introduction

This page describes the differences between RRF2 and RRF3 and the changes needed to configuration and other system files when upgrading to RRF3.

<!--[summary_image|2450]-->

**Important!** These instructions relate to RRF3.0 and to some extent to later versions of RRF3 however they are not kept up to date. Please refer to the [Gcode Dictionary](/User_manual/Reference/Gcodes) and use the [RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/Start) to migrate from RRF2 to RRF3.

# Overview

RepRapFirmware 3 is the next generation version of RepRapFirmware for Duet 3, Duet 2 WiFi/Ethernet/Maestro, and some other 32-bit motion control electronics. There are significant configuration differences from version 2, so upgrading from RRF 2.x to RRF 3.x is not plug-and-play.

RRF3.x uses the GPIO concept extensively. GPIO (General-Purpose Input/Output) is an digital signal pin on a circuit board or processor that can act as an Input or Output, as controlled by the Firmware. GPOUT = output, GPIN = input.

RRF3 uses these programmable pins to allow much greater flexibility and enhanced expansion for the future. The configuration files must define the uses of these pins first, then their assignments

The  primary benefits of RRF 3 are:

* RRF 3 uses pin names instead of logical pin numbers.
* With RRF 3 you can choose how devices are mapped to input and output pins. For example, you can connect fans to heater ports and heaters to fan ports.
* RRF 3 supports multiple endstop switches on axes (one per motor) natively. You no longer need to split axes during homing.
* RRF 3 allows any fan to have an associated tacho input.
* RRF 3 supports multiple tool probes. As well as a standard Z probe, you can define additional probes for use with the [M558](/User_manual/Reference/Gcodes/M558) command.
* RRF 3 supports the large amount of CAN bus based expansion available with Duet 3 boards.

The primary configuration differences compared to RRF 2 are:

* Logical pin numbers are no longer used in [M42](/User_manual/Reference/Gcodes/M42) and [M280](/User_manual/Reference/Gcodes/M280). GPIO port numbers replace them. Before using M42 or M280, the GPIO pin must have been assigned using [M950](/User_manual/Reference/Gcodes/M950).
* Endstop numbers are no longer used in [M591](/User_manual/Reference/Gcodes/M591). Pin names replace them.
* [M585](/User_manual/Reference/Gcodes/M585) now uses either an axis endstop or a probe
* The frequency and pin inversion status of fans and heaters can no longer be set in [M106](/User_manual/Reference/Gcodes/M106) or [M307](/User_manual/Reference/Gcodes/M307). These functions are moved to [M950](/User_manual/Reference/Gcodes/M950).
* RRF 3 requires sensors to be defined independently of heaters. Sensors are configured using the new [M308](/User_manual/Reference/Gcodes/M308) command, this replaces [M305](/User_manual/Reference/Gcodes/M305) used in earlier versions of RRF. When defining a heater, you must specify the sensor that it uses to control its temperature. The association between heaters and sensors is defined using [M950](/User_manual/Reference/Gcodes/M950).
* All RRF versions from 3.01 onwards have no pre-configured heaters, fans or endstop switches. You must assign all the heater, fan and endstop switch ports you require using [M950](/User_manual/Reference/Gcodes/M950) and [M574](/User_manual/Reference/Gcodes/M574).

RRF3 always uses a real time operating system (RTOS). It does not support legacy version 0.6 or 0.8.5 Duets.

# Getting RRF 3

The latest releases are on github here:

[https://github.com/Duet3D/RepRapFirmware/releases](https://github.com/Duet3D/RepRapFirmware/releases)

Do read the whole of this guide to understand the configuration changes required if migrating from RRF2.

**Note:** it is not possible to upgrade a Duet WiFi, Ethernet or Maestro directly from firmware 1.x or 2.x to 3.01 or later. You must upgrade to firmware 3.0 as an intermediate step.

# Conversion summary

## Heaters and temperature sensors

* You must **convert** your [M305](/User_manual/Reference/Gcodes/M305) commands to equivalent [M308](/User_manual/Reference/Gcodes/M308) commands to define the temperature sensors you use.
* You must **add** [M950](/User_manual/Reference/Gcodes/M950) commands to define the heaters you use, and associate them with temperature sensors. These M950 commands should come later in config,g than the M308 commands that create the temperature sensors, but before any M307 or M301 command that refer to that heater number.
* If your machine has a bed heater, you must **add** a [M140](/User_manual/Reference/Gcodes/M140) command to declare which heater is your bed heater, unless your config.g file already has one. Normally this will be M140 H0.

## Fans

* You must **add** [M950](/User_manual/Reference/Gcodes/M950) commands to define the fans you use. (RRF 3.0 on Duet 2 series created fans by default, but 3.01 and later do not.)

## Z probe

* You must declare the input pin used by the Z probe (and the modulation/programming pin, if needed) in your [M558](/User_manual/Reference/Gcodes/M558) command.
* If you are using a BLTouch (or use M280 and/or M42 for any other reason), you must create a GPIO port for it using [M950](/User_manual/Reference/Gcodes/M950) and change your [M280](/User_manual/Reference/Gcodes/M280) commands in deployprobe.g and retractprobe.g to address that port

## Endstops and homing files

* You must declare the endstop pins for all axes using [M574](/User_manual/Reference/Gcodes/M574) commands, one command per axis that has endstops. If you were creating additional axes in order to using multiple endstop switches on a single axis, one per motor, you are recommended to use the built-in support for homing the motors individually instead.
* If you use G1 S1 and/or G1 S2 commands in your homing files, you should change them to use G1 H1 or G1 H2 commands instead.

## GPIO and servos

* If you are using [M42](/User_manual/Reference/Gcodes/M42) or [M280](/User_manual/Reference/Gcodes/M280) to control spare outputs, or you are reading spare inputs using [M577](/User_manual/Reference/Gcodes/M577) or [M581](/User_manual/Reference/Gcodes/M581), you must set up those pins as GPOUT or GPIN pins using [M950](/User_manual/Reference/Gcodes/M950) and change the M42/M280/M577/M581 commands to use the GPOUT or GPIN port numbers that you created.

## Filament monitors

* You must change the [M591](/User_manual/Reference/Gcodes/M591) commands to specify the pin name instead of the logical pin number

## Tools

* If you use any tool numbers greater than 49 in your [M563](/User_manual/Reference/Gcodes/M563) commands then you must renumber them to be in the range 0-49.

# Changes G- and M-commands

## Changes to existing G- and M-commands

The following Gcode and Mcode commands have changed. Please refer to the Gcode dictionary for details.

[G0 and G1](/User_manual/Reference/Gcodes/G1) - Rapid move and Controlled (linear) move.
[G31](/User_manual/Reference/Gcodes/G31) - Set or Report Current Probe status
[M36](/User_manual/Reference/Gcodes/M36) - Return file information
[M42](/User_manual/Reference/Gcodes/M42) - Switch I/O pin
[M98](/User_manual/Reference/Gcodes/M98) - Call Macro/Subprogram
[M106](/User_manual/Reference/Gcodes/M106) - Fan on
[M115](/User_manual/Reference/Gcodes/M115) - Get Firmware Version and Capabilities
[M122](/User_manual/Reference/Gcodes/M122) - Diagnose
[M143](/User_manual/Reference/Gcodes/M143) - Maximum heater temperature
[M280](/User_manual/Reference/Gcodes/M280) - Set servo position
[M291](/User_manual/Reference/Gcodes/M291) - Display message and optionally wait for response
[M305](/User_manual/Reference/Gcodes/M305) - Set temperature sensor parameters
[M307](/User_manual/Reference/Gcodes/M307) - Set or report heating process parameters
[M452](/User_manual/Reference/Gcodes/M452) - Select Laser Device Mode
[M453](/User_manual/Reference/Gcodes/M453) - Select CNC Device Mode
[M500](/User_manual/Reference/Gcodes/M500) - Store parameters
[M501](/User_manual/Reference/Gcodes/M501) - Read stored parameters
[M550](/User_manual/Reference/Gcodes/M550) - Set Name
[M551](/User_manual/Reference/Gcodes/M551) - Set Password
[M558](/User_manual/Reference/Gcodes/M558) - Set Z probe type
[M563](/User_manual/Reference/Gcodes/M563) - Define or remove a tool
[M571](/User_manual/Reference/Gcodes/M571) - Set output on extrude
[M574](/User_manual/Reference/Gcodes/M574) - Set endstop configuration
[M577](/User_manual/Reference/Gcodes/M577) - Wait until endstop is triggered
[M581](/User_manual/Reference/Gcodes/M581) - Configure external trigger
[M584](/User_manual/Reference/Gcodes/M584) - Set drive mapping
[M585](/User_manual/Reference/Gcodes/M585) - Probe Tool
[M591](/User_manual/Reference/Gcodes/M591) - Configure filament sensing
[M670](/User_manual/Reference/Gcodes/M670) - Set IO port bit mapping
[M997](/User_manual/Reference/Gcodes/M997) - Perform in-application firmware update
[M999](/User_manual/Reference/Gcodes/M999) - Restart


## New commands

### [M308](/User_manual/Reference/Gcodes/M308): Create or modify sensor, or report sensor parameters

This command creates a sensor, or modifies an existing sensor, or reports the parameters of an existing sensor. It replaces the M305 command of RepRapFirmware 2.

In earlier versions of RepRapFirmware, sensors only existed in combination with heaters, which necessitated the concept of a "virtual heater" to represent a sensor with no associated heater (e.g. MCU temperature sensor). RepRapFirmware 3 allows sensors to be defined independently of heaters. The association between heaters and sensors is defined using M950.

### [M950](/User_manual/Reference/Gcodes/M950): Create heater, fan, or GPIO/servo port

M950 is used to create heaters, fans and GPIO ports and to assign pins to them. Each M950 command assigns a pin or pins to a single device. So every M950 command must have exactly one of the H, F, J, P or S parameters.

If a M950 command has C and/or Q parameters, then the pin allocation and/or frequency will be configured accordingly. Otherwise, the current configuration will be reported.

# Pin names

## Pin name format

A pin name is a string of characters enclosed in double quotation marks. It is not case sensitive. All instances of _ and - characters are stripped from pin names before comparing them.

Example:

`BED_HEAT`
`bedheat`

These two names refer to the same pin because of the comparison rules.

Some pin names have the form "*connector.pin*" or "*board.pin*" where *connector *or *board *identifies the connector on the Duet or the expansion board, and *pin *identifies the pin within that connector or on that expansion board. Sometimes a pin has multiple names.

Example:

`exp.heater3`
`exp.8`
`duex.e2_heat`
`duex.pwm1`


These four all refer to the heater3 pin, either on the expansion connector (where it is  pin #8 of 50) or on the heater output terminal block of a DueX2 or DueX5. However, there is a difference. The DueX boards buffer and invert the signal. Therefore, when you use one of the forms prefixed with *DueX* the firmware knows it has to invert the signal, but it doesn't invert it if you use one of the other forms. In contrast, when you referred to logical pin 3 in previous versions of RRF, the firmware always inverted the signal - so if you were driving a servo or BLTouch from this pin you would have needed to use the I1 parameter in your M280 command.

You may occasionally need to invert sense of a pin - for example, if you use a fan output to control the PWM input of a 4-wire fan instead of using it to switch the ground supply. You can indicate inversion by prefixing the pin name with exclamation mark.

Example:

`!fan2`

This means the pin labelled FAN2 on the Duet, but with the drive signal inverted.

When declaring an input pin (e.g. in the M574 command), a '^' character before the name indicates that the pullup resistor should be enabled. Otherwise it is not enabled.

Example:

`!^e1stop`

Some GCode commands accept more than one pin name. For example, the M453 command allows you to specify both forward and reverse spindle ports, the M574 command allows you to specify multiple endstop switches per axis, and the M558 command allows you to specify both input and output pins for the Z probe. You can use the + character to indicate multiple pins.

Example:

`z_stop+e0_stop+e1_stop`

Note, if you provide more pin names than the command accepts, the extra ones are likely to be ignored.

## Pin names for Duet boards

For each Duet board, see the hardware pages for a list of pin names and function.

[Duet 3 MB6HC](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6HC_Hardware_Overview#pin-names)
[Duet 3 Mini 5+](/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Hardware_Overview#pin-names)
[Duet 3 Expansion 3HC](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_3HC)
[Duet 3 Tool Board 1LC](/Duet3D_hardware/Duet_3_family/Duet_3_Toolboard_1LC#pin-names)
[Duet 3 Expansion 1XD](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1XD#pin-names)
[Duet 2 WiFi and Ethernet](/Duet3D_hardware/Duet_2_family/Duet_2_WiFi_Ethernet_Hardware_Overview#pin-names)
[DueX2 and DueX5](/Duet3D_hardware/Duet_2_family/DueX2_and_DueX5#pin-names)
[Duet 2 Maestro](/Duet3D_hardware/Duet_2_family/Duet_2_Maestro#pin-names)