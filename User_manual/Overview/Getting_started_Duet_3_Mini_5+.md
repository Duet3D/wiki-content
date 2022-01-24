---
title: Getting Started with Duet 3 Mini 5+
description: This page contains a step by step guide and links to using your Duet 3 Mini 5+ Wifi or Ethernet.
published: true
date: 2022-01-24T15:08:29.144Z
tags: 
editor: markdown
dateCreated: 2021-11-29T15:21:27.557Z
---


<!-- ![duet_3_mini_5+_getting_started_01.jpg](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_getting_started_01.jpg =250x)  -->

![duet_3_mini_5+_getting_started_02.jpg](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_getting_started_02.jpg =500x)

# Introduction

Congratulations on your new Duet 3 Mini 5+! This page will guide you through getting connected, updating firmware, setting up initial configuration.

First, read the warnings! See [Warnings](/User_manual/Overview/Warnings), [Fire safety](/User_manual/Overview/Fire_safety) and [USB ground loops](/User_manual/Overview/USB_ground_loops) for precautions you must take.

## Package contents

![duet_3_mini_5+_getting_started_03.jpg](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_getting_started_03.jpg =50%x){.align-right}

* 1 x Duet 3 Mini 5+ (WiFi or Ethernet)
* 1 x Connector kit, including:
  * Würth Elektronic WR-WTB series connectors (Molex-KK compatible)
  * Ferrules for power input and bed heater
  * Connectors and crimps for hotend heaters.
* 100 x crimps for connectors
* 1 x SD Card
* 1 x Single Board Computer (SBC) ribbon cable
* 1 x External antenna (WiFi version only)

## Standalone mode vs SBC mode

The Duet 3 mini 5+ is designed to either be directly connected to a network, or to connect to a Single Board Computer (SBC) like a Raspberry Pi. All standard printer functions, different kinematics, the network UI ([Duet Web Control](/User_manual/Reference/Duet_Web_Control_Manual)) etc work in both modes.

SBC mode allows part of the functionality to be handled by the SBC. For example an HDMI or Pi touch screen can be used as the UI, The Pi can act as the network server and plugins for Duet Software Framework on the Pi allow for adding custom features for more advanced control and monitoring.

# Minimum configuration

## {.tabset}

### Standalone mode (no SBC)

* Duet 3 Mini 5+ mainboard
* Power supply (min. 12V. max 25V, recommended 24V) and cables
* Stepper motors, heaters, thermistors, endstop switches etc. as required
* Supplied SD card in the Duet 3

### With Raspberry Pi (SBC)

* Duet 3 Mini 5+ mainboard
* Raspberry Pi (Model 3B or 4) or other compatible Single Board Computer (SBC), with power supply.
* Ribbon cable to connect the Raspberry Pi to the Duet. Use the 26-pin to 40-pin cable supplied. Recommended length is 100 to 150mm. Longer cables will degrade the SPI signal between the Raspberry Pi and the Duet.
* Power supply (min. 12V. max 25V, recommended 24V) and cables
* Stepper motors, heaters, thermistors, endstop switches etc. as required
* SD card with Duet Pi image in the Raspberry Pi
* No SD card in the Duet 3

## Optional extras

* Duet 3 expansion board(s) and/or toolboards
* RJ11 twisted pair cables to connect the expansion board(s).  The cable wiring must be straight-through. Only a single twisted pair is required, connected to pins 3 and 4 (the middle 2 pins) of the connector.
* 2-core red/black cable to daisy chain power to the expansion board(s)

# Getting started

## {.tabset}

### Standalone mode

The following section expects that you are setting up your Duet 3 Mini 5+ without an SBC (Single Board Computer, eg Raspberry Pi) connected. 

#### Connect external WiFi antenna

If you have the WiFi version of the Duet 3 Mini 5+, don't forget to connect the external antenna. Ideally, mount the antenna and Duet on your machine first; the antenna connector is quite a tight fit, and is not intended for frequent connecting/disconnecting. Carefully align the connector as square and straight as you can before firmly pushing on. You can also unscrew the aerial from the other end of the cable, which you'll need to do to mount the antenna, and leave the other end connected to the Duet. The antenna mounts through a 1/4" or 6.3mm hole.

![duet_3_mini_5+_getting_started_04.jpg](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_getting_started_04.jpg =600x)

#### Initial USB setup

You will need to initially setup the Duet 3 Mini 5+ using a USB cable (USB A to Micro B). Make sure the USB cable is capable of carrying power AND data; some USB cables are power-only. Then follow the guide here: [Getting connected to your Duet](/How_to_guides/Getting_connected/Getting_connected_to_your_Duet).

After following this guide, you should be able to connect to your Duet 3 Mini 5+ over your network, and connect to the Duet Web Console (DWC).

#### Update firmware

Update to the latest release firmware. Follow the guide here: [Updating Duet 3 Mini 5+ firmware](/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Firmware)

### SBC mode

#### Connecting it up

The Duet 3 Mini cannot power the Pi. You must power the Pi separately. The Raspberry Pi is sensitive to the input voltage, and many smartphone chargers or other USB power supplies cannot supply sufficient voltage. Therefore, we strongly recommend that you use the official Raspberry Pi PSU, or another PSU specifically designed to power a Raspberry Pi. If the red LED on the Pi is not continuously illuminated, the power supply is insufficient.

In other respects, SBC connection to the Duet and configuration is the same for the Duet 3 Mini 5+ as for the Duet 3 MB6HC. See [SBC Setup for Duet 3](/User_manual/Machine_configuration/SBC_setup).

When using an attached Raspberry Pi or other SBC, the WiFi or Ethernet interface on the Duet 3 Mini 5+ is disabled.

# Commissioning

## Familiarise yourself with Duet Web Control

The Duet Web Control user interface is the best way to control your Duet. A comprehensive manual is here: [Duet Web Control Manual](/User_manual/Reference/Duet_Web_Control_Manual)

## Upload machine configuration

So far, the Duet is not configured for your machine. Use the [RepRapFirmware Configuration Tool](https://configtool.reprapfirmware.org/Start) to produce a set of files as a zip file to upload to the Duet. Upload the zip file (no need to extract the files) to the Duet using Duet Web Control, by going to System > Upload System Files. For more information, see [Duet Web Control Manual](/User_manual/Reference/Duet_Web_Control_Manual)

# Connecting hardware

## Wiring

See the [Connecting hardware section of the User manual](/User_manual/Connecting_hardware) for choosing, connecting and testing each component.

Acquaint yourself with the [Duet 3 Mini 5+ wiring diagram](/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Hardware_Overview#physical-connections), to help connect components.

## Connecting a PanelDue

A PanelDue can be connected to connector IO_0 using a 4-core cable wired like this.

![displays_paneldue_d3_wiring_01.jpg](/manual/displays/displays_paneldue_d3_wiring_01.jpg =400x) ![displays_paneldue_d3_wiring_02.jpg](/manual/displays/displays_paneldue_d3_wiring_02.jpg =400x)

Include ```M575 P1 B57600 S1``` in your config.g to enable the PanelDue.

See [Connecting a PanelDue](/en/User_manual/Connecting_hardware/Display_PanelDue#option-1-4-way-cable) for more information.

## Connecting CAN bus expansion boards

Additional expansion boards add further functionality to your Duet 3 mainboard. These are connected by CAN bus. For an overview of connecting these, see [CAN connection basics](/User_manual/Machine_configuration/CAN_connection).

If you connect one or more expansion boards via CAN, you must install the two CAN termination jumpers on the last board in the chain, but not on any additional boards in the middle of the chain.

For a list of expansion boards that can be connect to a Duet 3 mainboard, see the 'CAN expansion' section of the [Duet 3 family](/Duet3D_hardware/Duet_3_family) page.

## Reporting issues

Please use the forum to report any issues: [https://forum.duet3d.com/](https://forum.duet3d.com/)