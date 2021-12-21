---
title: Logging
description: Logging of errors and other important events is an optional feature of RepRapFirmware 1.20 and later.
published: true
date: 2021-12-06T00:35:13.207Z
tags: 
editor: markdown
dateCreated: 2021-12-05T23:25:21.515Z
---

<!-- Migrated, not updated (Update for RRF3 and SBC, combine into larger troubleshooing doc) -->

When something goes wrong, particularly in the middle of a long print, it can be useful to have a log of what happened (at least as much as the electronics know). Logging of errors and other important events is an optional feature of RepRapFirmware 1.20 and later. Use the [M929](/User_manual/Reference/Gcodes/M929) to enable and disable logging and to specify the log file name. By default, logging is disabled. You can use a [M929](/User_manual/Reference/Gcodes/M929) command in config.g to enable it.

Some other way of obtaining diagnostics information are:

* There is a built-in setting in the web interface to request that the Duet log every G-code and its response, but during a print this can be overwhelming. You can obtain a little more information by having your slicer embed additional G-code into the print, for example adding  [M114](/User_manual/Reference/Gcodes/M114) (report position) to the code at every layer change.
* The firmware can be put in debugging mode with [M111](/User_manual/Reference/Gcodes/M111), which causes many debug messages, to be sent to the USB port. You can specify debugging messages only from certain submodules; to get a list, send M111 S1 P15. Debug output goes only to USB.
* It's not a log, but the [M122](/User_manual/Reference/Gcodes/M122) command will have the firmware report its status, including (for example) information about under- and over-voltage events. The output goes into the G-code console.