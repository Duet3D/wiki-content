---
title: Connecting RS485 and Modbus devices
description: 
published: false
date: 2025-10-15T00:10:24.201Z
tags: 
editor: markdown
dateCreated: 2025-10-10T14:15:26.485Z
---

# Introduction

From RepRapFirmware 3.6, Duet 3 mainboards 6HC, 6XD and Mini 5+ support RS485 serial data transmission standard and the Modbus RTU (Remote Terminal Unit) protocol. These can be used to interface with a wide range of devices, including sensors, relays, Programmable Logic Controllers (PLCs), Variable Frequency Drives (VFDs) and other devices.

## What is RS485?

RS485 is a serial communication standard, and is one of the most widely used. It has a number of advantages over other standards that make it particularly suitable for noisy industrial areas.

* Long Communication Distances: devices can communicate with each other over a long distance (up to 1200m) using RS485 communication. 
* High Data Rates: RS-485 supports a wide range of data rates, from a few hundred bits per second to 10 mbit/s.
* Multidrop Configuration: RS-485 supports multiple devices connected to the same communication bus. This enables the creation of complex networks with a single communication line.
* Robust Performance in Noisy Environments: RS485 uses differential signaling, where data is transmitted as the voltage difference between two lines, with voltage levels from -7V to +12V. This provides better noise immunity and reduces the impact of common-mode interference. Along with shielded and twisted pair cabling, this helps maintain reliable communication in the presence of interference.
* Cost-Effective Networking: Building RS-485 networks is cost-effective, especially for applications where long distances and multiple devices need to be connected. The simplicity of the standard contributes to lower implementation costs.
* Wide Industry Adoption: RS-485 has been widely adopted in industrial and building automation, HVAC systems, process control, and other applications. Its acceptance as an industry standard contributes to the availability of compatible devices and components.

## What is Modbus RTU?



# Requirements

* Duet 3 Mainboard - 6HC, 6XD or Mini 5+ (not supported on Duet 2 due to memory limitations)
* A UART to RS485 adapter or transceiver. 
  * Duet 3 Mainboard 6HC v1.02c and later, and Duet 3 Mainboard 6XD v1.02 and later, have an RS485 adapter built-in. 
  * For other Duet 3 boards, UART to RS485 adapters are available cheaply from many online sellers. Look for one using MAX485 or MAX3485 chip, with automatic Tx/Rx switching. Jay from TeamGloomy did a round-up here: [TeamGloomy github.io](https://teamgloomy.github.io/adapters_rs485.html)
* Wiring
* An RS485 device, such as a sensor, relay, PLC or VFD.

# Wiring

Selecting the right twisted pair cable is crucial for RS-485 communication. The cable’s twisted pairs reduce interference, and matching impedance (around 120 ohms) is essential. Choose an appropriate category (e.g. Cat 5e), consider shielding for high interference, and use termination resistors. Factor in distance, flexibility, and durability, ensuring compatibility with RS-485 connectors (e.g., DB-9, DB-25) for reliable performance.

# Configuration

Setting up and using RS485 and Modbus uses the following Gcodes:

[M575](/User_manual/Reference/Gcodes/M575) - Set up Duet output port
[M261.1](/User_manual/Reference/Gcodes/M261_1) - read data from a device
[M260.1](/User_manual/Reference/Gcodes/M260_1) - write data to a device
[M260.4](/User_manual/Reference/Gcodes/M260_4) - Raw Modbus transaction

## Setup port

```
M575 P2 B9600 S7
```

## Request data from device

Sending this from the console will receive the data as hexidecimal:

```
M261.1 P2 A1 R1 B2 F4
Received 00de 0228
```

From holding/keep registers (ie device settings)

```
M261.1 P2 A1 R257 B1 F3
Received 0001
M261.1 P2 A1 R258 B1 F3
Received 2580
M261.1 P2 A1 R259 B1 F3
Received 0000
M261.1 P2 A1 R260 B1 F3
Received 0000
```

## Send data to device

Eg changing device address

```
M260.1 P2 A1 F6 R257 B3
(didn't cycle power)
M261.1 P2 A1 R1 B2 F4
Received 00e0 022d
(cycled power)
M261.1 P2 A1 R1 B2 F4
Error: M261.1: no or bad response from Modbus device
M261.1 P2 A3 R1 B2 F4
Received 00df 022f
```

# Examples

## Connecting a temperature and humidity sensor

XY-MD01

## Connecting a VFD/spindle


## Connecting a PLC


