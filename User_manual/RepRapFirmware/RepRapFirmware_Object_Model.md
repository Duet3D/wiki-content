---
title: RepRapFirmware Object Model
description: All Duet software projects share the same object model to store configuration and sensor data.
published: true
date: 2023-02-20T14:49:10.535Z
tags: 
editor: markdown
dateCreated: 2023-02-20T14:49:10.535Z
---

# Introduction

All Duet software projects (RepRapFirmware, Duet Software Framework) provide a central 'Object Model' (OM) that replicates the entire machine state, storing configuration and sensor data. This data model is synchronized with Duet Web Control as well as stored in its backend, and can be accessed by Gcode and meta Gcode commands, macros and external data requests. This allows for powerful control and feedback systems to be built.

# The object model

For information about the object model structure, check out the [DSF code documentation](https://duet3d.github.io/DuetSoftwareFramework/api/DuetAPI.ObjectModel.ObjectModel.html).
For a list of the currently-supported fields, see [Object Model documentation](https://github.com/Duet3D/RepRapFirmware/wiki/Object-Model-Documentation)

# Viewing and using the Object Model

## DWC

Use the Object Model plugin to browse the Object Model tree.

## Gcode

Examples of Gcode

## External applications

Querying the OM from external applications


# Notes on using the Object Model

Some fields may not be available in standalone mode because some fields are only maintained by DSF and/or DWC. It is advised to consider this when developing applications that address Duets in standalone and SBC mode.

Certain fields have class names in braces (...) appended to the object model path. These class names are present for items where different item types may be configured. If a class inherits values from a base type, the inheritance is marked using a colon (:) followed by the base class name. So, for example, LaserFilamentMonitor : FilamentMonitor means that a LaserFilamentMonitor inherits all the properties from the FilamentMonitor base class.

In standalone mode, each main key (like boards or heat) has its own sequence number in the seqs key which is not documented here. Whenever a non-live field is updated (see M409 F"f"), this sequence number is increased. For clients targeting standalone mode, it can be helpful to check these values to determine when it is time to request a full key from RRF again. There is an extra value seqs.reply as well which is used notify clients about new messages (see rr_reply). Note that these sequence numbers are not exposed in SBC mode.
