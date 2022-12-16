---
title: Duet Software Framework
description: DSF Overview
published: true
date: 2022-12-16T13:36:32.101Z
tags: 
editor: markdown
dateCreated: 2022-12-01T14:46:05.540Z
---

# Duet Software Framework

## Overview

Duet Software Framework is a set of applications for ARM/ARM64 acting as middleware between RepRapFirmware and DuetWebControl. Its purpose is to offload certain features like networking, code parsing, and general code flow from RepRapFirmware and to provide several interfaces for modularity and plug-in support. In general, it consists of the following components:

- [DuetControlServer](https://github.com/Duet3D/DuetSoftwareFramework#duetcontrolserver): Main application providing IPC capabilities and implementing SBC link
- [DuetWebServer](https://github.com/Duet3D/DuetSoftwareFramework#duetwebserver): Web server application to provide DuetWebControl and HTTP APIs
- [DuetPluginService](https://github.com/Duet3D/DuetSoftwareFramework#duetpluginservice): Management service running both as `dsf` and `root` users for plugin support
- [DuetTools](https://github.com/Duet3D/DuetSoftwareFramework#tools): Set of applications to interact with DSF
- [DuetPiManagementPlugin](https://github.com/Duet3D/DuetSoftwareFramework/tree/master/src/DuetPiManagementPlugin): Plugin to provide SBC system configuration on [DuetPi](https://github.com/Duet3D/DuetPi)

For security reasons, the main DSF services run under a dedicate `dsf` user and plugins are generally encaged using AppArmor policies.

## Link to RepRapFirmware

DSF uses SPI to exchange data with RepRapFirmware. When RepRapFirmware (with built-in SBC support) starts and no SD card is present, RRF attempts to connect to an SBC automatically over SPI. In order to flag when data may be exchanged, a separate pin, the so-called TfrRdy pin, is used.

For more details, please check out the project's [README](https://github.com/Duet3D/DuetSoftwareFramework) and extra wiki pages.

### Behaviour on connection loss

When RRF or DSF lose connection to each other, the following things are supposed to happen:

1) Pending G/M/T-codes are invalidated and cancelled
2) Open files (including job and macro files) are closed immediately
3) All heaters are turned off

DSF and RRF detect a connection loss if no data exchanged within 4 seconds (within 500ms during transfers).

## Software setup

To set up DSF, check out the discrete guides about installation on a [Raspberry Pi](https://docs.duet3d.com/User_manual/Machine_configuration/DSF_RPi) and [other boards](https://docs.duet3d.com/User_manual/Machine_configuration/DSF_Other).

## Virtual file system

RepRapFirmware relies on FatFs and FatFs file paths. This means RepRapFirmware may want to request file paths like `0:/sys/config.g` or `/macros/someMacro` which is different from the FS structure used on Linux. For this reason, DCS translates paths to the first volume (`0:/` or just `/`) to the DSF base directory (sometimes also called the "virtual file system"). By default, this path is `/opt/dsf/sd` but it may be changed in `/opt/dsf/conf/config.json` using the `BaseDirectory` key.

The major difference between FS operation in standalone and SBC mode is the fact, that file paths in SBC mode are generally case-sensitive whereas file paths in standalone mode are always case-insensitive. 

DuetPi 3.4.4 and newer support auto-mounting of external drives (e.g. USB keys). In order to be able to access G-code or other files from those drives, DuetPi ensures they are mounted using the `dsf` user. DCS reports mounted USB drives as extra volumes using the [object model](https://github.com/Duet3D/RepRapFirmware/wiki/Object-Model-Documentation#volumes).

## Configuration notes

When DuetControlServer and the two DuetPluginService instances have been started, the macro file `dsf-config.g` is automatically invoked. You may use this configuration file to run custom G/M/T-codes that need to be executed when plugins are being started and/or SBC-relevant commands need to be executed.

It is not recommended to put SBC configuration data into `config.g`, because they will probably have no effect and may generate unwanted errors or warnings. See [DuetPiManagementPlugin](https://github.com/Duet3D/DuetSoftwareFramework/tree/master/src/DuetPiManagementPlugin#supported-codes) for a list of plugins that should NOT be put in `config.g` in SBC mode.

## Development

DSF is fully written in C# using .NET. In order to write custom C# applications connecting to it, the following NuGet packages are available:

- DuetAPI
- DuetAPIClient
- DuetHttpClient

Further information about third-party plugins (installable via DWC) is available [here](https://github.com/Duet3D/DuetSoftwareFramework/wiki/Third-Party-Plugins).

Note that DuetAPI and DuetHttpClient are platform-independent and may be used even with .NET Framework 4.8. DuetHttpClient also supports Duets running in standalone mode. For a general overview of HTTP requests provided by SBC mode, see [here](https://github.com/Duet3D/DuetSoftwareFramework/wiki/REST-API-(v3.4.4-or-newer)).

For developers preferring Python, the [dsf-python](https://github.com/Duet3D/dsf-python) library is availble.

In case neither is suitable, please refer to the [IPC documentation](https://github.com/Duet3D/DuetSoftwareFramework/wiki/API-Overview) to implement your own client library.

