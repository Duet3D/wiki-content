---
title: Getting Started with Duet 2 WiFi / Ethernet
description: This page contains a step by step guide and links to using your Duet 2 Wifi or Ethernet.
published: true
date: 2022-06-29T20:56:21.908Z
tags: 
editor: markdown
dateCreated: 2021-11-29T15:10:28.657Z
---

![duet_2_wifi_v1.04c_top.jpg](/duet_boards/duet_2_wifi_ethernet_maestro/duet_2_wifi_v1.04c_top.jpg =400x)

# Introduction

Congratulations on your new Duet 2 WiFi or Ethernet! This page will guide you through getting connected, updating firmware, setting up initial configuration.

First, read the warnings! See [Warnings](/User_manual/Overview/Warnings), [Fire safety](/User_manual/Overview/Fire_safety) and [USB ground loops](/User_manual/Overview/USB_ground_loops) for precautions you must take.

# Preparation

* See the following pages for choosing components. even if you already have a printer or set of components they can help in understanding the advantages and limitation of certain components.
  * [Choosing stepper motors](/User_manual/Connecting_hardware/Motors_choosing).
  * [Choosing a heater](/User_manual/Connecting_hardware/Heaters_overview).
  * [Choosing the power supply](/User_manual/Connecting_hardware/Power_choosing).
  * [Choosing a Z probe](/User_manual/Connecting_hardware/Z_probe_choosing).
  * [Choosing temperature sensors](/User_manual/Connecting_hardware/Temperature_choosing).

# Follow the getting connected guide

We suggest you get connected to your Duet before you wire it into your printer.

See [Getting connected guide](/How_to_guides/Getting_connected/Getting_connected_to_your_Duet)

# Familiarise yourself with Duet Web Control

The Duet Web Control user interface it the best way to control your Duet. It is used to control the Duet through the remainder of the steps in this guide. A comprehensive manual is here:

[Duet Web Control Manual](/User_manual/Reference/Duet_Web_Control_Manual)

# Firmware versions

## Now with RepRapFirmware 3 installed

From late 2020, Duet 2 boards ship with RepRapFirmware 3 loaded by default. Prior to this Duet 2 boards had RepRapFirmware 2 loaded and an upgrade procedure was required - see links below.

Duet 2 boards with RepRapFirmware 3 installed will have a sticker indicating this (with a QR code that links here).

> **CAUTION** There are guides and tutorials in this documentation and in many other locations on the internet that show how to set up a particular printer or accomplish a particular task using RepRapFirmware 2. Often these will still be useful for RepRapFirmware 3; however many configuration settings may be different.
{.is-warning}

It is strongly recommended to generate the initial configuration for your machine using the [RepRapFirmware config tool](https://configtool.reprapfirmware.org/Start) and the use guides to modify it if necessary.

## Check and update firmware if necessary.

See [updating the firmware](/User_manual/RepRapFirmware/Updating_firmware).

# Connect your Duet to the other components in your 3D printer.

<!-- * Start with the [guide|9|Wiring your Duet guide]. -->
* See the specific wiki page for more detail on each part of the hardware setup that applies to your setup:
  * [Duet 2 WiFi/Ethernet Hardware overview](/Duet3D_hardware/Duet_2_family/Duet_2_WiFi_Ethernet_Hardware_Overview)
  * [SD card](/User_manual/RepRapFirmware/SD_card).
  * [Power wiring](/User_manual/Connecting_hardware/Power_wiring).
  * [Connecting stepper motors](/User_manual/Connecting_hardware/Motors_connecting).
  * [Connecting endstop switches](/User_manual/Connecting_hardware/Sensors_endstops).
  * [Connecting thermistors or PT1000 temperature sensors](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000).
  * [Connecting thermocouples](/User_manual/Connecting_hardware/Temperature_connecting_thermocouples).
  * [Connecting PT100 temperature sensors](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000).
  * [Connecting a bed heater](/User_manual/Connecting_hardware/Heaters_bed).
  * [Connecting and configuring a chamber heater](/User_manual/Connecting_hardware/Heaters_chamber).
  * [Connecting extruder heaters](/User_manual/Connecting_hardware/Heaters_extruders).
  * [Connecting and configuring fans](/User_manual/Connecting_hardware/Fans_connecting).
  * [Connecting a Z probe](/User_manual/Connecting_hardware/Z_probe_connecting).
  * [Connecting an LCD control panel](/User_manual/Connecting_hardware/Display_PanelDue).
  * [Connecting an Emergency Stop button](/User_manual/Connecting_hardware/IO_E_stop).
  * [Connecting and configuring filament-out sensors](/User_manual/Connecting_hardware/Sensors_filament).
  * [DueX2 and DueX5 expansion boards](/Duet3D_hardware/Duet_2_family/DueX2_and_DueX5).
  * [Using external stepper motor drivers](/User_manual/Connecting_hardware/Motors_connecting_external).
  * [Adding additional output ports](/User_manual/Connecting_hardware/IO_additional_IO).

# Initial configuration

Start by generating a configuration file customised for your printer and upload it to the Duet. <!--See the [guide|10|cartesian guide] as a start point (even if you do not have a cartesian printer).-->

There are more detailed wiki pages for each printer type:

* [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian)
* [Configuring RepRapFirmware for a Linear Delta printer](/User_manual/Machine_configuration/Configuration_linear_delta) and more detail on delta calibration here: [Calibrating a delta printer](/User_manual/Tuning/Delta_calibration).
* [Configuring RepRapFirmware for a CoreXY printer](/User_manual/Machine_configuration/Configuration_coreXY)
* [Configuring RepRapFirmware for an IDEX printer](/User_manual/Machine_configuration/Configuration_IDEX)
* [Configuring RepRapFirmware for a CNC machine](/User_manual/Machine_configuration/Configuration_CNC)
<!--* [Configuring RepRapFirmware for a SCARA printer **UPDATE LINK**]()
* [Configuring RepRapFirmware for a Polar printer **UPDATE LINK**]()
* [Configuring RepRapFirmware for a Hangprinter printer **UPDATE LINK**]()-->

# Commissioning tests

<!--* A step by step example guide for basic commissioning can be [guide|39|found here.] It uses a Duet Maestro and an Ender 3, but the steps are broadly applicable.-->

More detailed specifics are found below:

* [Test endstop switches](/User_manual/Connecting_hardware/Sensors_testing)
* [Test temperature sensors at room temperature](https://docs.duet3d.com/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000#testing-temperature-sensors)
* [Test axes or motors individually](/User_manual/Connecting_hardware/Motors_testing)
* [Test homing behaviour](/User_manual/Connecting_hardware/Sensors_testing)
* [Test and calibrate the Z probe](/User_manual/Connecting_hardware/Z_probe_testing)
* Tune heaters, see [Tuning the heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning)
<!--* [Test heating **UPDATE LINK**]()
* [guide|40|Test and calibrate extrusion|stepid=165]
* [guide|40|Configure your slicer|stepid=167]
* [guide|40|Now try a test print!|stepid=179]-->

# If you experience any problems

* See the [FAQ](/User_manual/Troubleshooting/FAQ).
* See the [troubleshooting](/User_manual/Troubleshooting) section.
* Come and ask on the [forum](https://forum.duet3d.com/).