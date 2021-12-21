---
title: Adapting an existing printer to Duet
description: What mechanical and electrical details are needed to configure RepRapFirmware?  What information is needed to configure an existing 3D printer to work with a Duet control board? Going from Marlin on Arduino to RepRapFirmware on Duet
published: true
date: 2021-12-13T14:41:05.923Z
tags: 
editor: markdown
dateCreated: 2021-11-29T14:53:55.818Z
---

<!--- Migrated not edited! -->

# What information is needed to configure an existing 3D printer to work with a Duet control board?

Every 3D printer is different. And while mechanics are commonly shared, the specifics will vary greatly. Changing over an existing commercial 3D printer to use Duet electronics requires some basic mechanical and electrical knowledge of the printer. This information can usually be obtained from manufacturer documentation, or a quick google search. Some firmwares will report the current configuration by sending `M501`. In a pinch you can measure. The details needed, and common values for them are found below.

<!---## Example conversion
[guide|37]-->

## What mechanical details are needed to configure RepRapFirmware?

* **Steps per MM**
  * Motor resolution
  * 0.9° or 1.8° rotation per step
  * 400 or 200 steps per revolution
  * Drive pulley tooth count
  * 16T, 20T, 30T, 40T, 60T are common
  * Belt tooth profile and pitch
  * Timing Profile Example: GT2, GT3, MXL, HTD
  * Belt Pitch Example: 2mm, 3mm, 2.08mm, etc
  * Driver microstepping
  * Commonly 16 steps (A4988) or 32 steps (DRV8825)
  * Duet can use up to 256
  * Lead screw pitch and lead
  * Pitch = distance between threads
  * Lead = travel in mm for one revolution
    * TR8*8 are most common with a pitch of 2mm and a lead of 8mm
  * Gear Ratios
  * Extruder Gearing
    * Example: E3D Titan and BondTech extruders use 3:1 gearing.
  * Z Axis Gearing
    * Example: 20T drive gear and 40T gear on lead screw would be 2:1 gearing
  * Formulas
  * [Online Steps Per MM Calculator (Google Sheets)](https://docs.google.com/spreadsheets/d/1ts5fz73JQoQ4G0Z95nzfPDSS6HYEyczyZyccDZ5M6B4/edit?usp=sharing)
  * X and Y Axis
    * **xy_steps_per_mm = (motor_steps_per_rev * driver_microstep) / (belt_pitch_mm * pulley_number_of_teeth)**
  * Z Axis
    * **Z_steps_per_mm = (motor_steps_per_rev * driver_microstep) / thread_pitch **
  * Extruder Axis
    * **e_steps_per_mm = (motor_steps_per_rev * driver_microstep) * (big_gear_teeth / small_gear_teeth) / (hob_effective_diameter * pi)**
* **Build Volume**
  * Axis travel maximums
  * Print surface size
  * Z Probe to Nozzle XYZ offset
  * Origin location (0,0) AKA home position.

## What electrical details are needed to configure RepRapFirmware?

* **Voltages**
  * Power Supply
  * Commonly 12v or 24v
  * Fan Voltages
  * Most commonly 12v, but also available in 24v and 5v
  * Heater cartridge and bed heater voltage
  * 12v or 24v common for heater cartridges and PCB heat beds. 120v or 240v for AC powered heat beds using an SSR
  * Max rated motor current in amps
  * Check the motor spec sheet if available, may need to find model number sticker on the motor
  * May be found in configuration.h or LCD menu of the printer
  * For optimal performance on the Duet target to set the current at 70-85% of max rated
* **Thermistor details**
  * Type of thermistor
  * Thermistor, PT100, PT1000, Type-K Thermocouple
  * Thermistor table values if known
  * Max temp for bed and hotend
* **Endstop details**
  * Endstop type: microswitch, optical, hall effect
  * Switch type: Active Low (Normally open), or Active High (normally closed)
  * Endstop position: High end or low end of travel
* **Z Probe details**
  * Probe voltage
  * Probe trigger height
  * Probe voltage

## What firmware details are needed to configure RepRapFirmware?

* **Speed settings**
  * Acceleration and Jerk values
    * Can be found in LCD menu or in configuration.h
  * Extrusion multiplier
  * Z offset
  * Fan speeds

# Going from Marlin on Arduino to RepRapFirmware on Duet

Once you have these printer details you can use the [RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/) to build yourself a starting config file set.

If you're not sure what these values are for your printer, check with the manufacturer, check labels, check google, check the forums to see if someone else has already converted your model of printer, etc.

If you're coming from Marlin and have access to the configuration.h files you check there for some of the values.

Note: Marlin uses MM/s for feed rates, RepRapFirmware uses MM/Min. To convert from one to the other you must either multiply or divide by 60. Example: 100mm/s = 6000mm/min.