---
title: Controlling IO pins
description: 
published: true
date: 2023-06-09T09:22:46.846Z
tags: 
editor: markdown
dateCreated: 2021-10-18T14:03:58.975Z
---

<!--
# REWRITE FOR GPIO

Originally based on [Connecting servos and DC motors](/User_manual/Connecting_hardware/Motors_servos). Expand to cover inputs and outputs. Also see [Using servos and controlling unused IO pins](https://duet3d.dozuki.com/Wiki/Using_servos_and_controlling_unused_IO_pins) on Dozuki.
-->
# Introduction

RepRapFirmware allows you to connect other devices to unused outputs. Expansion boards also provide additional inputs and outputs. The following Gcodes allow re-purposing of some pins, and direct control of those pins for peripherals such as PWM hobby servos, DC motors and pumps. 
* [M950](/User_manual/Reference/Gcodes/M950) - Defines heater, fan, spindle, GPIO or servo pin (RRF 3.x)
* [M42](/User_manual/Reference/Gcodes/M42) - Defines (RRF 2.x) and controls I/O pin 
* [M280](/User_manual/Reference/Gcodes/M280) - Sets servo position

There are a number of options for extra control: 
* PWM pins that provide a 3.3V or 5V signal to control a connected peripheral, eg servo motor, VFD
* PWM pins that control the voltage through a peripheral, such as those for heaters and fans which are switched on the GND side, can be used to control DC motors and pumps.
* Digital pins (on/off) that can be used to control switches and relays, or read inputs from external switches and triggers.

### Number of General Purpose Input/Output (GPIO) ports supported (as of RRF 3.4)

| | Duet 3 MB6HC/6XD | Duet 3 Mini 5+ | Duet 2 WiFi/Ethernet | Duet 2 Maestro | Notes |
|---|---|
| MaxGpInPorts | 32 (16 in RRF3.3) | 32 (16 in RRF3.3) | 20 | 10 | The maximum number of general purpose input ports. |
| MaxGpOutPorts | 32 | 32 | 20 | 10 | The maximum number of general purpose output ports |

# Firmware configuration

## Fan or heater output as a GPIO

In RepRapFirmware 3.x, define the fan connector with [M950](/User_manual/Reference/Gcodes/M950) as a general purpose I/O pin that can be controlled using [M42](/User_manual/Reference/Gcodes/M42).

In RepRapFirmware 2.x, using the I-1 parameter in the M106 command for a fan disables it, which frees up the pin for use as a general purpose I/O pin that can be controlled using M42.


# Tabs {.tabset}

## RepRapFirmware 3.x and later

In RRF 3.x and later, pins are undefined at startup and no GPIO ports are allocated by default. Create a GPIO port using [M950](/User_manual/Reference/Gcodes/M950). Control the port using [M42](/User_manual/Reference/Gcodes/M42) or [M280](/User_manual/Reference/Gcodes/M280), where the P parameter is the GPIO port number. Duet 2 supports up to 10 GPIO ports, numbered 0 to 9. 

In RRF_3, the F and I parameters are no longer supported in M42. Instead, use the F and I parameters in M950 when you create the GPIO port.

Example - RRF_2 code:

`M42 P3 I1 S0.5 F500  ; set Heater 3 pin to 50% PWM at 500Hz`

RRF_3 code:

```
M950 P0 C"exp.heater3" Q500  ; allocate GPIO port 0 to heater3 on expansion connector, 500Hz
...
M42 P0 S0.5  ; set 50% PWM on GPIO port 0
```

For a list of pin names, see [RRF3 Pin names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names).

## RepRapFirmware 2.x and earlier

The rest of this page shows how to set up M42 and M280 in RRF 2.x.

### Logical pin numbers

Starting with firmware version 1.16, pin numbers in the [M42](/User_manual/Reference/Gcodes/M42) command are logical pin numbers, not Arduino-like pin numbers. The [M280](/User_manual/Reference/Gcodes/M280) servo control uses a subset of the same pin numbers.

*Note that these logical pin numbers are not the physical pin numbers on the expansion header, or the internal pin numbering within the firmware.*

To see a list of logical pin numbers, and where these pins are on the 2nd Generation Duets, see the input/output section and wiring diagram on the [Duet 2 WiFi and Ethernet hardware overview](/Duet3D_hardware/Duet_2_family/Duet_2_WiFi_Ethernet_Hardware_Overview) and [Duet 2 Maestro hardware overview](/Duet3D_hardware/Duet_2_family/Duet_2_Maestro) pages.


