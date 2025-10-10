---
title: Connecting RS485 and Modbus devices
description: 
published: false
date: 2025-10-10T16:36:39.790Z
tags: 
editor: markdown
dateCreated: 2025-10-10T14:15:26.485Z
---

# Introduction

From RepRapFirmware 3.6, Duet 3 mainboards 6HC, 6XD and Mini 5+ support RS485 serial data transmission standard and the Modbus RTU (Remote Terminal Unit) protocol. These can be used to interface with a wide range of devices, including sensors, relays, Programmable Logic Controllers (PLCs), Variable Frequency Drives (VFDs) and other devices.

## What is RS485



## What is Modbus RTU



# Requirements

* Duet 3 Mainboard - 6HC, 6XD or Mini 5+ (not supported on Duet 2 due to memory limitations)
* A UART to RS485 adapter. 
  * Duet 3 Mainboard 6HC v1.02c and later, and Duet 3 Mainboard 6XD v1.02 and later, have an RS485 adapter built-in. 
  * For other Duet 3 boards, UART to RS485 adapters are available cheaply from many online sellers. Look for one using MAX485 or MAX3485 chip, with automatic Tx/Rx switching. Jay from TeamGloomy did a round-up here: [TeamGloomy github.io](https://teamgloomy.github.io/adapters_rs485.html)
* Wiring
* An RS485 device, such as a sensor, relay, PLC or VFD.

# Wiring



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


