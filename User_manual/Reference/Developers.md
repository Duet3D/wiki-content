---
title: Contributing to development
description: The Duet runs entirely on open-source firmware and software. Whats more it is built using open tools (KiCAD and Eclipse), we want to encourage other to get involved and lower the barrier to entry. 
published: true
date: 2021-12-10T14:03:31.571Z
tags: 
editor: markdown
dateCreated: 2021-11-30T16:04:31.638Z
---

# Introduction

The Duet runs entirely on open-source firmware and software. Whats more it is built using open tools ([KiCad](https://www.kicad.org/) and [Eclipse](https://eclipse.org/)). We want to encourage other to get involved and lower the barrier to entry.

# Source Files

## Duet Hardware

All hardware source files are available on [Github](https://github.com/Duet3D?tab=repositories). See the [Duet3D Hardware](/Duet3D_hardware) pages for links to hardware schematics and KiCAD sources.

### KiCAD

The Duet is open source hardware, developed using open source tools, specifically [KiCAD](https://www.kicad.org/) for the hardware design. You need at least version 4.0.2 to use the Duet 2 WiFi source files, along with the ["complib" library](https://github.com/Duet3D/Duet-2-Hardware/tree/master/complib.pretty). Newer projects now use KiCAD 5.0.

### License

We encourage adaptation of the hardware design, get stuck in! Ensure you comply with the [license terms](https://github.com/Duet3D/Duet-2-Hardware/blob/master/LICENSE).

## RepRapFirmware

**RepRapFirmware**, for controlling 3D printers and related devices, is also [Open Source Software]( http://www.gnu.org/licenses/gpl-3.0.en.html) with source files available and actively maintained on [Github](https://github.com/Duet3D/RepRapFirmware). This repository provides a bug tracker, which is where you should file **confirmed** bug reports.

## Duet Web Control

The **Duet Web Control** web interface source is [Open Source Software]( http://www.gnu.org/licenses/gpl-3.0.en.html) with source files available and actively maintained on [Github](https://github.com/Duet3D/DuetWebControl). This repository provides a bug tracker, which is where you should file **confirmed** bug reports.

## Other projects

Other repositories in the Duet ecosystem are available on [Github here](https://github.com/Duet3D?tab=repositories).

# Contributing

If you want to make a change to the code, the best approach is to [fork the project](https://help.github.com/articles/fork-a-repo/), make some changes, test them on your own machine, and when they work file a [pull request](https://yangsu.github.io/pull-request-tutorial/). Github will then support discussion of the pull request, which can ultimately be accepted into the main source code. Before submitting a pull request, **please ensure that your changes conform to any coding standard** that is used for that part of the software. The coding standard may be described in the readme file or in a separate file in the root of the corresponding GitHub repository.

# Firmware Structure

The code is divided into multiple repositories:

* [RepRapFirmware](https://github.com/Duet3D/RepRapFirmware) is the G-code interpreter and motion control.
* [CoreNG](https://github.com/Duet3D/CoreNG) is the hardware abstraction library (like the Arduino libraries) that provides a hardware interface layer for the MCU
* [Duet Web Control](https://github.com/Duet3D/DuetWebControl) is the set of static files stored on the on-board SD card in the /www directory, that run in your browser and provide the web interface.
* [DuetWiFiServer](https://github.com/Duet3D/DuetWiFiSocketServer) is the firmware that runs on the ESP8266, serving up the DuetWebControl files and forwarding commands and file transfers to the MCU
* [CoreESP8266](https://github.com/Duet3D/CoreESP8266) is the low-level library (based on the Arduino ESP8266 library) that provides a hardware abstraction for the ESP8266
* [PanelDueFirmware](https://github.com/Duet3D/PanelDueFirmware) is the firmware that runs on the [PanelDue](/Duet3D_hardware/Accessories/PanelDue)

To compile a firmware binary, you will need the code for the firmware and for any underlying libraries. The detailed setup process is in the [build instructions](https://github.com/Duet3D/RepRapFirmware/wiki/Building-RepRapFirmware).

If you plan to contribute your changes back to the main project (please consider it!), it is probably best to use [https://git-scm.com/](https://git-scm.com/).

# Useful links to RRF information

[Object Model of RepRapFirmware (current release)](https://github.com/Duet3D/RepRapFirmware/wiki/Object-Model-Documentation)
[Object Model of RepRapFirmware (Beta & RC)](https://github.com/Duet3D/RepRapFirmware/wiki/Object-Model-Documentation-Beta-&-RC)
[HTTP requests supported by RepRapFirmware](https://github.com/Duet3D/RepRapFirmware/wiki/HTTP-requests)
[JSON status responses](https://github.com/Duet3D/RepRapFirmware/wiki/JSON-responses)
[Supported FTP Commands](https://github.com/Duet3D/RepRapFirmware/wiki/Supported-FTP-Commands)