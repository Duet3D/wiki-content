---
title: RepRapFirmware Object Model
description: All Duet software projects share the same object model to store configuration and sensor data.
published: true
date: 2025-07-22T15:58:07.203Z
tags: 
editor: markdown
dateCreated: 2023-02-20T14:49:10.535Z
---

# Introduction

All Duet software projects (RepRapFirmware, Duet Software Framework) provide a central 'Object Model' (OM) that replicates the entire machine state, storing configuration and sensor data. This data model is synchronized with Duet Web Control as well as stored in its backend, and can be accessed by Gcode and meta Gcode commands, macros and external data requests. This allows for powerful control and feedback systems to be built.

# The object model

For information about the object model structure, check out the [DSF code documentation](https://duet3d.github.io/DuetSoftwareFramework/api/DuetAPI.ObjectModel.ObjectModel.html){target=_blank}.
For a list of the currently-supported fields, see [Object Model documentation](https://github.com/Duet3D/RepRapFirmware/wiki/Object-Model-Documentation){target=_blank}

# Viewing and using the Object Model

## DWC

Use the Object Model plugin to browse the Object Model tree. Enable the plugin in DWC > Settings > Plugins, and it will appear in DWC > Plugins > Object Model.

## Gcode

Examples of Gcode using Object Model fields are on the [Meta Gcode page here](/User_manual/Reference/Gcode_meta_commands#examples-of-use){target=_blank}.

## External applications

Query the OM from external applications using [M409](/User_manual/Reference/Gcodes/M409){target=_blank} or the [HTTP request rr_model](https://github.com/Duet3D/RepRapFirmware/wiki/HTTP-requests){target=_blank}.


# Job information and custom Object Model keys

When a job runs, RepRapFirmware extracts information from the Gcode file and stores it in the Object Model. This data can be used using Gcode and [Meta Gcode](/User_manual/Reference/Gcode_meta_commands){target=_blank} to, for example, determine any additional actions that need to be taken. 

A list of the information gathered is [here](https://github.com/Duet3D/RepRapFirmware/wiki/Object-Model-Documentation#job){target=_blank}, and includes (not an exhaustive list):
* File name, file size, file modification date, the application that generated the gcode
* Object height, layer height, number of layers, filament usage, print time
* File thumbnail size, width, file type
* M486 (object cancellation) info: list of build objects, position, whether cancelled
* Name and duration of the previous job
* When the job is running, OM variables are updated with current layer number, time left, percentage complete

Additionally, from RepRapFirmware 3.6.0, user-defined Object Model keys and values can be embedded in the Gcode file, which will be created in the OM when the Gcode file is run:
* The header and footer of a job file may now contain comments of the form 
`;customInfo <key>=<value>` 
where `<key>` is an identifier and `<value>` is an expression. Such comments will result in the specified key-value pairs being added to the object model under job.file.customInfo. They are also returned as additional information in the rr_fileinfo response. The M36 response now includes any customInfo found in the GCode file (although there is a known issue with this feature, see [1096](https://github.com/Duet3D/RepRapFirmware/issues/1096){target=_blank})

# Notes on using the Object Model

* Some fields may not be available in standalone mode because some fields are only maintained by DSF and/or DWC. It is advised to consider this when developing applications that address Duets in standalone and SBC mode.

* Certain fields have class names in braces (...) appended to the object model path. These class names are present for items where different item types may be configured. If a class inherits values from a base type, the inheritance is marked using a colon (:) followed by the base class name. So, for example, LaserFilamentMonitor : FilamentMonitor means that a LaserFilamentMonitor inherits all the properties from the FilamentMonitor base class.

* In standalone mode, each main key (like boards or heat) has its own sequence number in the seqs key which is not documented here. Whenever a non-live field is updated (see M409 F"f"), this sequence number is increased. For clients targeting standalone mode, it can be helpful to check these values to determine when it is time to request a full key from RRF again. There is an extra value seqs.reply as well which is used notify clients about new messages (see rr_reply). Note that these sequence numbers are not exposed in SBC mode.
