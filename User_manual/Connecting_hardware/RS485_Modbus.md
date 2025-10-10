---
title: Connecting RS485 and Modbus devices
description: 
published: false
date: 2025-10-10T14:15:26.485Z
tags: 
editor: markdown
dateCreated: 2025-10-10T14:15:26.485Z
---

# Introduction

From RepRapFirmware 3.6, Duet 3 mainboards 6HC, 6XD and Mini 5+ support RS485 serial data transmission standard and the Modbus RTU (Remote Terminal Unit) protocol. These can be used to interface with a wide range of devices, including sensors, relays, Programmable Logic Controllers (PLCs), Variable Frequency Drives (VFDs) and other devices.

## What is RS485



## What is Modbus RTU



# Requirements

* Duet 3 Mainboard - 6HC, 6XD or Mini 5+
* 

# Wiring

# Configuration

[M575](/User_manual/Reference/Gcodes/M575) - Set up Duet output port
[M261.1](/User_manual/Reference/Gcodes/M261_1) - read data from a device
[M260.1](/User_manual/Reference/Gcodes/M260_1) - write data to a device
[M260.4](/User_manual/Reference/Gcodes/M260_4) - Raw Modbus transaction

## Setup port

```
M575 P2 B9600 S7
```

## Request data from device

From input registers:

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


