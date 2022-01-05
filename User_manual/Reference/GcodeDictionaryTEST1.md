---
title: Gcode Dictionary TEST 1
description: 
published: true
date: 2022-01-05T11:30:07.640Z
tags: 
editor: markdown
dateCreated: 2021-12-21T23:37:57.258Z
---

# Introduction

GCodes are a widely used machine control language. They are human readable and editable. This page describes the RepRapFirmware supported GCodes. RepRapFirmware follows the philosophy of "GCode everywhere", in essence the users or external program's interaction with the firmware should be through GCodes. There are GCodes for all supported control and configuration inputs along with status and debugging information.

RepRapFirmware GCodes were originally based on the information from the [RepRap wiki GCode page](http://reprap.org/wiki/G-code). There are some GCodes listed on that page that are not implemented in RepRapFirmware. More details can be found on the [GCodes not implemented](/User_manual/Reference/Gcodes_not_implemented) page.

# GCode and RepRapFirmware

A typical piece of GCode sent to a machine running RepRapFirmware might look like this (The meaning of these codes (and more) is explained below on this page.)

div with white-space: pre-line
<br>
<div class=cblock style="white-space: pre-line">G10 P0 S195 R175
  T0 ; this line is meant to be indented 2 spaces
G1 X100 Y100 Z0.3 F3000      ;      really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line 
G1 X100.4 Y99.3 E0.23 F600
...many 1000s more lines...
</div>

div with white-space: pre-wrap
<br>
<div class=cblock style="white-space: pre-wrap">G10 P0 S195 R175
  T0 ; this line is meant to be indented 2 spaces
G1 X100 Y100 Z0.3 F3000      ;      really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line 
G1 X100.4 Y99.3 E0.23 F600
...many 1000s more lines...
</div>

pre with overflow: auto
<br>
<pre class=cblock>
G10 P0 S195 R175
  T0 ; this line is meant to be indented 2 spaces
G1 X100 Y100 Z0.3 F3000      ;      really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line really long line 
G1 X100.4 Y99.3 E0.23 F600
...many 1000s more lines...
</pre>


## GCode Everywhere

A design philosophy of RepRapFirmware is "GCode everywhere" what this means is explained in this sub section

The GCode can originate from a number of sources:

* Sent to over USB (for example from [Pronterface](http://www.pronterface.com/))
* Sent by the [Duet Web Control (DWC)](/User_manual/Reference/Duet_Web_Control_Manual) Interface
* Sent by an external controller such as the [PanelDue](/Duet3D_hardware/Accessories/PanelDue)

In all cases the GCode could

* be entered by user one line at time, for example during configuration or testing
* be sent by the User Interface (Pronterface, Web Interface or PanelDue) in response to the user pressing buttons
* originate from [Macros](/User_manual/Tuning/Macros) that are triggered on startup, on certain events (such as error conditions), or called by the user or UI.
* be from a GCode file which are normally stored on the on-board or external SD card.

A key difference from other 3d printer firmwares is not employing a separate command set (other than GCodes) to configure the printer. To that end RepRapFirmware has a large collection of configuration GCodes that allow the behaviour of the machine to be controlled. For some examples of when these GCodes are employed have a look at these wiki pages:

* [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian)
* [Configuring RepRapFirmware for a Linear Delta printer](/User_manual/Machine_configuration/Configuration_linear_delta)
* [Configuring RepRapFirmware for a CoreXY printer](/User_manual/Machine_configuration/Configuration_coreXY)
* [Configuring RepRapFirmware for an IDEX printer](/User_manual/Machine_configuration/Configuration_IDEX)
* [Tuning the heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning)
* [Setting up automatic probing of the print bed](/User_manual/Connecting_hardware/Z_probe_auto_probing)
* [Connecting hobby servos and DC motors](/User_manual/Connecting_hardware/Motors_servos)
* [Controlling unused IO pins](/User_manual/Connecting_hardware/IO_GPIO)
<!--* [Configuring RepRapFirmware for a SCARA printer **UPDATE LINK**]()
* [Configuring RepRapFirmware for a Polar printer **UPDATE LINK**]()
* [Configuring RepRapFirmware for a Hangprinter printer **UPDATE LINK**]()-->

The advantage of "GCode everywhere" is the same commands can be send from any of the GCode sources, and originate from the user, a UI, macro or file and it will generate the same response from the firmware. This greatly improves the ease and power of firmware configuration and operation.