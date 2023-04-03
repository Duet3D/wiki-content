---
title: Contributing to development
description: The Duet runs entirely on open-source firmware and software. Whats more it is built using open tools (KiCAD and Eclipse), we want to encourage other to get involved and lower the barrier to entry. 
published: true
date: 2022-06-29T11:44:31.773Z
tags: 
editor: markdown
dateCreated: 2021-11-30T16:04:31.638Z
---

# Introduction

The Duet runs entirely on open-source firmware and software. Whats more it is built using open tools ([KiCad](https://www.kicad.org/){target=_blank} and [Eclipse](https://eclipse.org/){target=_blank}). We want to encourage other to get involved and lower the barrier to entry.

# Source Files

## Duet Hardware

All hardware source files are available on [Github](https://github.com/Duet3D?tab=repositories){target=_blank}. See the [Duet3D Hardware](/Duet3D_hardware) pages for links to hardware schematics and KiCAD sources.

### KiCAD

The Duet is open source hardware, developed using open source tools, specifically [KiCAD](https://www.kicad.org/){target=_blank} for the hardware design. You need at least version 4.0.2 to use the Duet 2 WiFi source files, along with the ["complib" library](https://github.com/Duet3D/Duet-2-Hardware/tree/master/complib.pretty){target=_blank}. Newer projects now use KiCAD 5.0.

### License

We encourage adaptation of the hardware design, get stuck in! Ensure you comply with the [license terms](https://github.com/Duet3D/Duet-2-Hardware/blob/master/LICENSE){target=_blank}.

## RepRapFirmware

**RepRapFirmware**, for controlling 3D printers and related devices, is also [Open Source Software]( http://www.gnu.org/licenses/gpl-3.0.en.html){target=_blank} with source files available and actively maintained on [Github](https://github.com/Duet3D/RepRapFirmware){target=_blank}. This repository provides a bug tracker, which is where you should file **confirmed** bug reports.

## Duet Web Control

The **Duet Web Control** web interface source is [Open Source Software]( http://www.gnu.org/licenses/gpl-3.0.en.html){target=_blank} with source files available and actively maintained on [Github](https://github.com/Duet3D/DuetWebControl){target=_blank}. This repository provides a bug tracker, which is where you should file **confirmed** bug reports.

## Other projects

Other repositories in the Duet ecosystem are available on [Github here](https://github.com/Duet3D?tab=repositories){target=_blank}.

# Contributing

If you want to make a change to the code, the best approach is to [fork the project](https://docs.github.com/en/get-started/quickstart/fork-a-repo){target=_blank}, make some changes, test them on your own machine, and when they work file a [pull request](https://yangsu.github.io/pull-request-tutorial/){target=_blank}. Github will then support discussion of the pull request, which can ultimately be accepted into the main source code. Before submitting a pull request, **please ensure that your changes conform to any coding standard** that is used for that part of the software. The coding standard may be described in the readme file or in a separate file in the root of the corresponding GitHub repository.

# Firmware Structure

The code is divided into several modules, and some modules use components from multiple repositories. If you plan to contribute your changes back to the main project (please consider it!), it is probably best to use [https://git-scm.com/](https://git-scm.com/){target=_blank}.

## Duet 2 and 3 main board firmware

* [RepRapFirmware](https://github.com/Duet3D/RepRapFirmware){target=_blank} is the G-code interpreter and motion control
* [CoreN2G](https://github.com/Duet3D/CoreN2G){target=_blank} is the hardware abstraction library that provides an interface layer to the MCU
* [CANlib](https://github.com/Duet3D/CANlib){target=_blank} declares constants and structs that are used by more than one module, and defines the CAN message formats used by CAN-connected Duet 3 systems
* [FreeRTOS](https://github.com/Duet3D/FreeRTOS){target=_blank} is a fork of the FreeRTOS real-time operating system
* [RRFLibraries](https://github.com/Duet3D/RRFLibraries){target=_blank} contains functions and classes that are used by both main board and expansion board firmware (and in some cases by PanelDueFirmware) but do not belong in CoreN2G

To compile a RepRapFirmware binary see the [build instructions](https://github.com/Duet3D/RepRapFirmware/wiki/Building-RepRapFirmware){target=_blank}.

## Duet 3 expansion board firmware

* [Duet3Expansion](https://github.com/Duet3D/Duet3Expansion){target=_blank} holds the main code for processing messages received via CAN and reporting status via CAN
* [CoreN2G](https://github.com/Duet3D/CoreN2G){target=_blank}, [CANlib](https://github.com/Duet3D/CANlib){target=_blank}, [FreeRTOS](https://github.com/Duet3D/FreeRTOS) and [RRFLibraries](https://github.com/Duet3D/RRFLibraries){target=_blank} are as above.

## HTTP user interface
* [Duet Web Control](https://github.com/Duet3D/DuetWebControl){target=_blank} is the set of static files stored on the on-board SD card in the /www directory, that run in your browser and provide the web interface.

## WiFi server

* [DuetWiFiSocketServer](https://github.com/Duet3D/DuetWiFiSocketServer){target=_blank} is the firmware that runs on the ESP8266, serving up the DuetWebControl files and forwarding commands and file transfers to the MCU
* [CoreESP8266](https://github.com/Duet3D/CoreESP8266){target=_blank} is the low-level library (based on the Arduino ESP8266 library) that provides a hardware abstraction for the ESP8266
* [LwipESP8266](https://github.com/Duet3D/LwipESP8266){target=_blank} is a fork of the LWIP TCP/IP stack

## Single Board Computer interface
* [DuetSoftwareFramework](https://github.com/Duet3D/DuetSoftwareFramework){target=_blank} is software to run on a Single Board Computer connected to the SPI interface provided by some Duets



# Useful links to RRF information

[Object Model of RepRapFirmware (current release)](https://github.com/Duet3D/RepRapFirmware/wiki/Object-Model-Documentation){target=_blank}
[Object Model of RepRapFirmware (Beta & RC)](https://github.com/Duet3D/RepRapFirmware/wiki/Object-Model-Documentation-Beta-&-RC){target=_blank}
[HTTP requests supported by RepRapFirmware](https://github.com/Duet3D/RepRapFirmware/wiki/HTTP-requests){target=_blank}
[JSON status responses](https://github.com/Duet3D/RepRapFirmware/wiki/JSON-responses){target=_blank}
[Supported FTP Commands](https://github.com/Duet3D/RepRapFirmware/wiki/Supported-FTP-Commands){target=_blank}