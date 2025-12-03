---
title: GCodes not implemented
description:  This page lists GCodes that may be found in other firmwares/CNC control software which are not implemented in RepRapFirmware. 
published: true
date: 2025-12-03T10:25:22.846Z
tags: 
editor: markdown
dateCreated: 2021-12-03T21:45:16.729Z
---

This page lists GCodes that may be found in other firmwares/CNC control software which are not implemented in RepRapFirmware.

For the detail on implemented GCodes see the [GCode](/User_manual/Reference/Gcodes) page. Some will be implemented in future firmware releases (see [this forum category](https://forum.duet3d.com/category/8/firmware-wishlist) for more details). Other features are covered by existing RepRapFirmware GCodes, are obsolete, or deprecated.

Note, in RepRapFirmware 2.03 and later, if you try to execute a G- or M-command that RRF does not implement, it will execute a system macro of that name if it exists. For example, if you send G40 then it will execute /sys/G40.g if it exists; and if you send M48 then it will execute /sys/M48.g if it exists.

## GCodes that can be implemented via macros.
We believe a macro can be written to achieve the same functionality in a more flexible way

* M7: Mist Coolant On (CNC specific) (you can define a macro M7.g to implement this)
* M8: Flood Coolant On (CNC specific) (you can define a macro M8.g to implement this)
* M9: Coolant Off (CNC specific)(you can define a macro M9.g to implement this)
* M10: Vacuum On (CNC specific) (you can define a macro M10.g to implement this)
* M11: Vacuum Off (CNC specific)(you can define a macro M11.g to implement this)
* M40: Eject : define a macro to eject parts.
* M48: Measure Z-Probe repeatability ([use a macro](/User_manual/Connecting_hardware/Z_probe_testing#probe-consistency-test))
* M142: Holding Pressure (for vacuum tables) (you can define a macro M142.g to implement this if a pressure sensor is connected as a linear analog sensor)
* M355: Turn case lights on/off (use a macro that calls M42 or M150, depending on the type of lights used.)
* M126: Open Valve (Can use a servo on any PWM pin and M280, write a macro M126.g if required)
* M127: Close Valve (Can use a servo on any PWM pin and M280, write a macro M127.g if required)
* M146: Set Chamber Humidity (use a macro and M42 to control humidifier, M305 to view the humidity with a DHT sensor)
* M240: Trigger camera (use a macro to call M42 to trigger a pin connected to a camera)
* M240: Start conveyor belt motor / echo off (use a macro to control a conveyor axis/for echo debug use M111)
* M241: Stop conveyor belt motor / echo on (use a macro to control a conveyor axis/for echo debug use M111)
* M540: Enable/Disable "Stop SD Print on Endstop Hit" (A macro can be written than remaps endstops as triggers during a job and remaps them back as endstops during homing. Once mapped as triggers the trigger macros can carryout functionality such as pausing the job in progress.)


## GCodes not yet implemented but may be in the future

When/If these are implemented depends very much on the demand.

* G15/G16: Polar co-ordinates
* G40: Compensation Off (CNC specific)
* G80: Cancel Canned Cycle (CNC specific)
* M33: Stop and Close File and save restart gcode (possibly not using this specific G-Code) Pause already saves the powerfail-resume information.
* M52: Adaptive Feed Control - Forward feed control set with M220, stepping back through the jb file is not supported.
* M85: Set inactivity shut down timer (No demand)
* M123: Tachometer value
* M124: Immediate motor stop  - may be achieved sufficiently when feedhold is implemented.
* M407: Display filament diameter (currently does the same as M404, possibly change to display measured diameter in future)
* M590: Report current tool type and index
* M668: Set Z-offset compensations polynomial (currently only implemented in http://reprap.org/wiki/User:Foxkid dc42-cmm) fork.


## GCodes not implemented which are not planned for implementation

These are unlikely to be implemented due to functionality existing in other RepRapFirmware G-codes or there being no perceived demand (if that is wrong then post in the firmware wishlist on the forum)

* G6: Direct Stepper Move (Use G1 H1 instead)
* G33: Measure/List/Adjust Distortion Matrix (Use G29 or G32 instead)
* G100: Calibrate floor or rod radius (No demand)
* G130: Set digital potentiometer value (Use M906)
* G131: Remove offset
* G132: Calibrate endstop offsets (For Delta see M666, M208 allows endstop positions to be set)
* G133: Measure steps to top
* G161: Home axes to minimum (use G28, if that homes to max then consider a macro to move to 0,0,0 after homing)
* G162: Home axes to maximum (use G28, if that homes to min then consider a macro to move to max,max,max after homing)
* M6: Tool Change - Tool change macros and entering Tn commands allow for greater functionality
* M31: Output time since last M109 or SD card start to serial (No demand)
* M33: Get the long name for an SD card file or folder (Not required, other SD card M commands already support long file names)
* M34: Set SD file sorting options (Not Required as the UI, e.g. DWC or PanelDue is better suited to handling how file lists are displayed)
* M41: Loop (No requirement, conditional GCode is more powerful)
* M43: Stand by on material exhausted (use M591)
* M43: Pin report and debug (much of this functionality is available within M42, M581,M582, M583)
* M53: Feed Stop Control - related to feedhold, once feedhold is implemented as a physical switch for feedhold can be mapped and unmapped using macros
* M93: Send axis_steps_per_unit (use M92)
* M101: Turn extruder 1 on (Forward), Undo Retraction (use standard G1 commands, or G10 for firmware retraction)
* M102: Turn extruder 1 on (Reverse) (use standard G1 commands, or G10 for firmware retraction)
* M103: Turn all extruders off, Extruder Retraction (use standard G1 commands, or G10 for firmware retraction + M18/M84 to turn of specific motors)
* M113: Set Extruder PWM (No requirement)
* M128: Extruder Pressure PWM (use M42)
* M129: Extruder pressure off (use M42)
* M130: Set PID P value (use M301)
* M131: Set PID I value (use M301)
* M132: Set PID D value (use M301)
* M133: Set PID I limit value (use M301)
* M134: Write PID values to EEPROM (use M500)
* M136: Print PID settings to host (use M301)
* M149: Set temperature units (No demand, just use C)
* M155: Automatically send temperatures (hosts should use polling)
* M160: Number of mixed materials ( use M567)
* M163: Set weight of mixed material ( use M567 to define the ratios based on extrusion percentage not weight)
* M164: Store weights (Not required w/o M163)
* M165: Set multiple mix weights (use M567)
* M202: Set max travel acceleration (use M204)
* M207: Calibrate z axis by detecting z max length (Clashed with existing meaning of M207, Use G1 H3 instead)
* M208: Set unretract length (Clashes with M208 Set axis max travel and can be achieved using M207)
* M209: Enable automatic retract (no demand)
* M210: Set homing feedrates (homing feedrates are specified in the home....g macros)
* M211: Disable/Enable software endstops (use M564)
* M212: Set Bed Level Sensor Offset (use G31)
* M218: Set Hotend Offset (use G10)
* M227: Enable Automatic Reverse and Prime (anti ooze strategy is better managed by the slicer)
* M228: Disable Automatic Reverse and Prime (anti ooze strategy is better managed by the slicer)
* M229: Enable Automatic Reverse and Prime (anti ooze strategy is better managed by the slicer)
* M240: Echo off (use M111)
* M241: Echo on (use M111)
* M245: Start cooler (use normal fan control M106 etc)
* M246: Stop cooler (use normal fan control M106 etc)
* M250: Set LCD contrast (better set using the LCD if its a smart LCD)
* M251: Measure Z steps from homing stop (Delta printers) (No demand)
* M306: Set home offset calculated from toolhead position (No demand)
* M320: Activate autolevel (use bed probing, levelling Gcodes G29-G32)
* M321: Deactivate autolevel (use G29)
* M322: Reset autolevel matrix  (use G29)
* M323: Distortion correction on/off
* M340: Control the servos (use M280)
* M360: Report firmware configuration (use M503)
* M360-M364: SCARA calibration codes (not required, use G1 S2 moves commands instead)
* M365 SCARA scaling factor (use M579)
* M371: Move to next calibration position
* M372: Record calibration value, and move to next position
* M373: End bed level calibration mode
* M380: Activate solenoid (use M42)
* M381: Disable all solenoids (use M42)
* M405: Filament Sensor on (This and M406 are a subset of the functionality of [M591](/User_manual/Reference/Gcodes/M591)) 
* M406: Filament Sensor off 
* M420: Set RGB Colors as PWM (use M150)
* M420/M421: Enable/Disable Mesh Leveling (use bed probing, levelling Gcodes G29-G32)
* M460: Define temperature range for thermistor controlled fan (use M106 to set a fan in thermostatic mode)
* M530: Enable printing mode (not required)
* M531: Set print name (not required)
* M532: Set print progress (not required)
* M565: Set Z probe offset (see G31)
* M600: Set line cross section (no demand)
* M605: Set dual x-carriage movement mode (no requirement use M563)
* M700: Level plate (See G32)
* M703: Get Board Type (use M115/M122)
* M710: Erase the EEPROM and reset the board (use M503, M500 and then M112, in a macro if you need to do this odd command)
* M800: Fire start print procedure (use start gcode in slicer)
* M801: Fire end print procedure (use end gcode in slicer)
* M907: Set digital trimpot motor (use M906)
* M908: Control digital trimpot directly (no requirement, use M906)
* M909: Set microstepping (use M350)
* M910: Set decay mode (Detailed driver configuration can be achieved using the "C" Parameter of M569)
* M928: Start SD logging (See M929)