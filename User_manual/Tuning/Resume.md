---
title: Setting up to resume a print after a power failure or planned power down
description: RepRapFirmware can be configured to allow you to resume a print after loss of power. This page explains how.
published: true
date: 2022-03-29T12:52:52.195Z
tags: 
editor: markdown
dateCreated: 2021-12-03T16:00:04.074Z
---


**These instructions relate to RepRapFirmware 1.20 and later (including RRF 2.x and 3.x).** The auto-save facility was first introduced in version 1.19, however improvements in 1.20 necessitated changes to the configuration mechanism.

Auto-save is supported in SBC mode from RepRapFirmware v3.4beta2. Earlier versions do not support it, see [here](https://github.com/Duet3D/DuetSoftwareFramework/issues/55).

# Overview

RepRapFirmware can be configured to allow you to resume a print after loss of power. This can be used in two ways:

* To handle unplanned power outages
* You can pause the print, turn off the heaters, wait for the hot end to cool, and turn the printer off (e.g. because you are no longer available to attend it).

# Limitations

## Physical

* You must be able to re-home the printer (possibly using a different homing sequence from normal) with a print on the bed.
* If the print becomes detached from the bed while the power is off due to loss of bed heat, you won't be able to resume the print.
* The head must not drop down onto the print when power is removed.

## Power

* If you want to handle unplanned power outages, you should use 24V power, not 12V. If you use 12V power then it is most unlikely that there will be sufficient power left to lift the print head and save the resume information when power failure is detected. 
* Adding a 10000uF capacitor across the VIN rail can help increase the amount of stored power giving enough time to write to the SD card. However, the PSU may false-detect a short circuit and turn off when the machine is powered on. In this case, we suggest using a diode and parallel resistor, connected between the VIN line and the capacitor. The resistor is for charging the cap. When the power goes off, the capacitor powers VIN via the diode. Make sure the diode is sufficient to handle the bed heater current (if powered from VIN) for at least a few seconds; in the fraction of a second between the mains failing and the VIN voltage dropping low enough for RRF to detect the loss of power, the capacitor will be supplying the bed through the diode.
* For Duet + SBC, a solid external 5V supply is recommended for the Duet + SBC for this feature to work. When power to the Duet + SBC is cut, the SBC may turn off before the Duet can inform the SBC about the content of resurrect.g, or the SBC may lose power while it's trying to write the content of resurrect.g to the microSD card. An external 5V buck regulator may be sufficient to keep a Duet 3 Mini 5+ and SBC on long enough. For MB6HC, the on-board 5V regulator might not endure long enough for resurrect.g to be written to persistent storage if the Duet powers an SBC as well. Hence we recommend using an external 5V PSU if this feature is configured.

# How it works

* Any time you pause a print from SD card, the state of the print is saved to a special file on the SD card, **sys/resurrect.g**
* On the Duet 2, you can also also use the M911 command in config.g to enable automatic pause and state saving when low VIN voltage is detected
* You must set up file **sys/resurrect-prologue.g** to home the printer, taking account of the fact that there will be a print on the bed
* When the power supply voltage drops below the auto-pause threshold voltage, the heaters are turned off to conserve power, the state of the print is saved to file **sys/resurrect.g** on the SD card, the power fail script specified in the M911 command is run, and the print is left in a paused state
* After the power is restored, you can use command M916 to resume the print from where it stopped. This command runs file **sys/resurrect.g** which calls **sys/resurrect-prologue.g** at an appropriate point to home the printer

# Configuring low power automatic pause and save

Use a [M911](/User_manual/Reference/Gcodes/M911) command in your configg file. Example of this for a 24V Cartesian printer:

```
M911 S21.0 R23.0 P"M913 X0 Y0 G91 M83 G1 Z3 E-5 F1000"
```

In this example, the power fail procedure is execute if VIN drops below 21.0V. The quoted string is the command that is executed when low power is detected. In this example it does the following:

* Set X and Y motors to zero current (M913 X0 Y0). This is to make the power last longer. Don't do this on a delta printer. Important: use M913, not M906. This is because M906 won't execute until all queued moves have been completed, whereas M913 will execute immediately.
* Set relative movement mode (G91)
* Set relative extrusion mode (M83)
* Simultaneously lift the head and retract filament (G1 Z3 E-5 F1000)

# Setting up the sys/resurrect-prologue.g file

When you print file **sys/resurrect.g** it performs the following operations:

* Set bed and chamber heater temperatures
* Set all tools and heaters to their saved temperatures and other parameters, and set tools to active or standby as needed (without executing tool change macros)
* Call **sys/resurrect-prologue.g**
* Restore fan speeds, move the head to where it stopped printing, and resume the print

At the very least, your **sys/resurrect-prologue.g** file must do the following:

* Home the printer, without crashing the head into the print. On a delta, or on a Cartesian or CoreXY printer with a Z max homing switch, you can home all axes. On other types of printers you may have to skip homing Z and use a G92 command to tell the printer the current Z coordinate. If you choose not to home an axis, then even if the print head hasn't moved since power down, the motor positions will change by up to 4 full steps compared to their position at power down. **Note**: recent firmware versions write a G92 command containing the coordinates at the time of failure into resurrect.g, just before the call to resurrect-prologue.g. So you may get away without homing axes. But it is still best to home any axes that you can (typically X and Y on a cartesian or CoreXY printer).
* Execute M116 to wait for all heaters to reach operating temperature. You may do this either before or after homing axes.
* If your power fail procedure in the M911 command retracts filament and your printer has a single nozzle, you may wish to undo the retraction.

Example resurrect-prologue.g file for a delta printer:

```
M116 ; wait for temperatures
G28 ; home all towers
M83 ; relative extrusion
G1 E4 F3600 ; undo the retraction that was done in the M911 power fail script
```

Example resurrect-prologue.g file for a Cartesian or CoreXY printer using recent firmware (G92 command included in resurrect.g):

```
M116 ; wait for temperatures
G28 X Y ; home X and Y, hope that Z hasn't moved
M83 ; relative extrusion
G1 E4 F3600 ; undo the retraction that was done in the M911 power fail script
```