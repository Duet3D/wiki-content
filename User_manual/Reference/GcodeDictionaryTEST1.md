---
title: Gcode Dictionary TEST 1
description: 
published: true
date: 2021-12-22T00:57:18.320Z
tags: 
editor: markdown
dateCreated: 2021-12-21T23:37:57.258Z
---

# Introduction

GCodes are a widely used machine control language. They are human readable and editable. This page describes the RepRapFirmware supported GCodes. RepRapFirmware follows the philosophy of "GCode everywhere", in essence the users or external program's interaction with the firmware should be through GCodes. There are GCodes for all supported control and configuration inputs along with status and debugging information.

RepRapFirmware GCodes were originally based on the information from the [RepRap wiki GCode page](http://reprap.org/wiki/G-code). There are some GCodes listed on that page that are not implemented in RepRapFirmware. More details can be found on the [GCodes not implemented](/User_manual/Reference/Gcodes_not_implemented) page.

# GCode and RepRapFirmware

A typical piece of GCode sent to a machine running RepRapFirmware might look like this (The meaning of these codes (and more) is explained below on this page.)

```gcode
G10 P0 S195 R175
T0
G1 X100 Y100 Z0.3 F3000
G1 X100.4 Y99.3 E0.23 F600
...many 1000 more lines...
```

| General Info ||
| Header | Description |
|--------|-------------|
| Tracking # | Tracking number. Clicking on the number will take you to Canada Posts tracking page for that number. |
| Mailed On | The date the tracking number was created or mailed on. |
| Delivery Standard | The date that Canada Post said it will be delivered. |
| Delivery Date | The date the tracking number was delivered. |
