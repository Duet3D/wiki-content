---
title: Configuring RepRapFirmware for a DLP printer controlled by nanoDLP
description: RRF can be used to control a DLP printer, accepting commands via the USB port from nanoDLP software
published: true
date: 2022-01-26T11:13:00.533Z
tags: 
editor: markdown
dateCreated: 2022-01-26T11:12:57.195Z
---

# Overview

RepRapFirmware 2.02 and later can be used to control the motion components of a DLP printer, accepting commands via the USB port from nanoDLP software running on a PC, Raspberry Pi or similar via the USB port.

# Configuring RepRapFirmware for nanoDLP

Use the following command in config.g to set nanoDLP compatibility:

```
M555 P6
```

This instructs RepRapFirmware to return Marlin-compatible responses, and also to delay returning an OK response to G0 and G1 commands until the movement is complete. See [M555](/User_manual/Reference/Gcodes/M555) in the GCode dictionary.

Most commands sent by nanoDLP are either configurable in nanoDLP or are standard G- and M-codes. However, nanoDLP also uses two poorly-documented commands. These are [M650](/User_manual/Reference/Gcodes/M650) (set peel move parameters), and [M651](/User_manual/Reference/Gcodes/M651) (execute peel move). A peel move is a typically a tilting movement of the resin tank that separates the freshly-exposed film of resin from the bottom of the tank.

The handling of these commands in RepRapFirmware depends on the firmware version:

* In 2.02 the M650 command is silently ignored, and M651 caused macro sys/peel-move.g to be executed. You must create this macro file and populate it with the commands that execute the peel motion.
* In 2.03beta3 and later, command M650 causes macro sys/M650.g to be executed, and command M651 causes macro sys/M651.g to be executed. Typically you would create an empty sys/M650.g file, and create a sys/M651.g file containing the commands to execute the peel motion.