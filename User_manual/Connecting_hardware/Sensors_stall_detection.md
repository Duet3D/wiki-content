---
title:  Stall detection and sensorless homing
description: 
published: true
date: 2021-12-15T22:27:33.206Z
tags: 
editor: markdown
dateCreated: 2021-10-22T13:05:41.274Z
---

# Scope

**This document is relevant to:** Duet 2 WiFi / Ethernet, Duet 3 MB6HC and Duet 3 Mini 5+
**Firmware versions:** RepRapFirmware 1.2 and later (Duet 2 only), RepRapFirmware 3.1.1 and later

# Overview

The **TMC5160/2160** drivers (Duet 3 MB6HC and Duet 3 EB3HC), **TMC2209** drivers (Duet 3 Mini 5+) and Trinamic **TMC2660** drivers (Duet 2 WiFi / Ethernet) support the stallGuard^TM^ feature. This feature allows the driver to detect motor stalls under some circumstances. Stall detection may be useful for detecting when a motor has skipped steps due to the nozzle hitting an obstruction, and for homing the printer without using endstop switches. RepRapFirmware 1.20 and later provides facilities for configuring and using stall detection.

**The TMC2224 drivers on the Duet Maestro do not support stall detection.**

Currently (RRF 3.2), stall detection on Duet 3 tool boards (Duet 3 TOOL1LC) and expansion boards (Duet 3 EB3HC) are not yet reported and cannot be used for homing. See [Duet 3 with CAN expansion firmware configuration limitations](/User_manual/RepRapFirmware/CAN_limitations).

## Table of features

| Board | Driver chip and Datasheet link | Microstep Interpolation | stealthChop | spreadCycle | stallGuard | coolStep |
|:---|:---|
| Duet 3 MB6HC / Duet 3 EB3HC | [TMC2160](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC2160A-datasheet_Rev1.06.pdf) or [TMC5160](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC5160A_Datasheet_Rev1.14.pdf) | Y | Y (SC2) | Y | Y (SG2) | Y |
| Duet 3 Mini 5+ | [TMC2209](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC2209_Datasheet_V105.pdf) | Y | Y (SC2) | Y | Y (SG4) | Y |
| Duet 3 Toolboard | [TMC2209](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC2209_Datasheet_V105.pdf) | Y | Y (SC2) | Y | Y (SG4) | Y |
| Duet 2 WiFi  and Ethernet | [TMC2660](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC2660_datasheet_Rev1.07.pdf) | 16x only | N | Y | Y (SG2) | Y |
| Duet 2 Maestro | [TMC2224](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC220x_TMC2224_Datasheet_Rev1.10.pdf) | Y | Y (SC2) | Y | N | N |

# Limitations of stall detection

* The stall detection threshold has to be tuned to the particular stepper motors you use.
* The stepper drivers only update the stall detection state every 1 or 4 full steps, depending on configuration. So the actual position of the stall is uncertain to either +/- half a full step or +/- 2 full steps. This means that when using stall detection to replace endstop switches, the position defined by the stall is much less accurate than with typical endstop switches.
* Stall detection is not reliable at low motor speeds. It would typically give false stall indications at low speeds. To avoid this, the firmware allows you to set minimum speeds (in motor full steps per second) below which stall indications will be ignored. These values may need to be tuned to suit your requirements. It is easier to get reliable stall detection at higher motor speeds.
* Stall detection is not reliable at motor speeds that are too high for the drivers to be able to supply sufficient voltage to maintain motor current. Therefore if you wish to use stall detection to detect skipped steps during printing, you must limit the motor speed to a value that does not require a higher voltage than your power supply provides (maximum 25V). See [Choosing Stepper Motors: Working out the power supply requirements](/User_manual/Connecting_hardware/Motors_choosing#working-out-the-power-supply-requirements).
* The stall detection threshold varies a little with motor temperature, therefore the motor temperatures must not be allowed to vary widely.
* Stall detection works by detecting an increase in the motor load. You may need to reduce acceleration and/or jerk to avoid false stall detection.
* It is very likely that stall detection works better on low current high inductance motors than on  high current low inductance motors, although we have not verified this experimentally. Unfortunately, high inductance motors suffer from torque reduction and increased noise at lower speeds than low inductance motors do, so they are not recommended if you want high speed motion.

# Minimum recommended speed for stall detection

A rough figure for the minimum speed in full steps per second (M915 H parameter) for which you should be able to get reliable stall detection is given by this formula:

* Hmin = full_steps_per_rev * rated_current * actual_current/(sqrt(2) * pi * rated_holding_torque)

where full_steps_per_rev is 200 or 400 (for 1.8 or 0.9deg motors),  currents are in amps, and holding torque is in Nm. From this it can be seen that stall detection at low speeds is easier at lower motor currents. This is one reason why motor current is often reduced during stall-detect homing.

Attempt to keep the speed above 40mm/s.

# Background on stall detection

Stall detection is configured using the [M915](/User_manual/Reference/Gcodes/M915) command, The parameters are:

* **Pnnn:nnn:...** Drive number(s) to configure
* **X,Y,Z,U,V,W,A,B,C** Axes to configure (alternative to using the P parameter)
* **Snnn** Stall detection threshold (-64 to +63, values below -10 not recommended). Higher values reduce the sensitivity; lower values make false stall detection more likely.
* **Fn** Stall detection filter mode, 1 = filtered (one reading per 4 full steps), 0 = unfiltered (default, 1 reading per full step)
* **Hnnn** (optional) Minimum motor full steps per second for stall detection to be considered reliable, default 200 (try 400 for 0.9deg motors)
* **Rn** Action to take on detecting a stall from any of these drivers: 0 = no action (default), 1 = just report it, 2 = pause print, 3 = pause print, execute /sys/rehome/.g, and resume print

Additionally, the **TMC2209** stepper driver used in Duet 3 Mini 5+ (and Duet 3 Tool board TOOL1LC once stallGuard is implemented in firmware), features stallGuard 4. This is optimised for operation with stealthChop, while its predecessor stallGuard 2 (TMC5160 and TMC2260) works with spreadCycle. You will need to adjust the speed at which stealthChop changes over to spreadCycle. This is set by [M569](/User_manual/Reference/Gcodes/M569) V parameter. The default is 2000.

* Send M569 P[driver_number] to see current setting in mm/sec
* Reducing the V parameter increases the speed at which the driver changes from stealthChop to spreadCycle. Make sure that any stall detection happens while the driver is in stealthChop mode.

## Stall detection sensitivity (S, F and H parameters)

* Using F1 should reduce the likelihood of getting false stall reports, and it also allows stall detection to work at higher speeds. In the worst case (Duet + Duex 5) the stall status of each driver is checked every 100us. So if you use F0, the time between full steps must be at least 100us to guarantee that the stall is detected. With F1 this is reduced to 25us. However, F1 delays stall detection slightly, so you may wish to use F0 when using stall detection for sensorless homing.
* Set the S parameter to the lowest value at which you do not get false stall reports. Typical values for nema 17 motors are between 1 and 3. False stall reports are most likely during fast travel moves, because the acceleration increases the load on the motor. False stall reports are also more likely when the motors are hot.
* If you have difficulty finding a value for S that detects stalls reliably without also giving rise to false stall reports, try the following:
  * Increase the minimum full steps/second for stall detection (H parameter) and use corresponding higher movement speeds
  * Reduce acceleration (M201) and/or jerk (M566)
  * If you are using stall detection for homing, reduce motor  current while homing (see the M913 command)

## Action to take on detecting a stall (R parameter)

* Use R0 if you are configuring stall detection for sensorless homing only.
* Use R1 when experimenting with the other parameters, so that you can see what effect they have during a print. If logging is enabled, stalls will be logged as well as reported.
* Use R2 when you have tuned stall detection and you want to pause the print automatically when a stall is detected.
* Use R3  when you have tuned stall detection and you want to re-home and continue the print automatically. **You must have a rehome.g file in /sys on the SD card**, to instruct the printer on how you'd like to rehome. On a Cartesian or CoreXY printer, typically you would enable stall detection on just the X and Y motors, and re-home just the X and Y axes in rehome.g.

# Sensorless Homing

Sensorless homing means not using endstop switches, but instead detecting the stall when the axis reaches the limit of its travel. Configuring sensorless homing requires some trial and error, as there are multiple variables to adjust and tune in. Prior to beginning this, open the [Gcodes](/User_manual/Reference/Gcodes) to better understand the commands being used.

## Configuring Sensorless Homing

### 1. Enable sensorless homing

* Change your endstop type to "sensorless" in config.g. To change your endstop, you must change the [M574](/User_manual/Reference/Gcodes/M574) command in config.g. For example, `M574 X1 S3` and `M574 Y1 S3` sets the X and Y endstops to sensorless homing at the axes low end. This is required. 
* Include the following [M915](/User_manual/Reference/Gcodes/M915) command in config.g. `M915 X Y R0 F0`. This sets up the last main command needed for sensorless homing.  Save your changes. Do not press "yes" for restart. There are other changes that must still be made.

### 2. Change your homing files' structures

The next step is to change your homing files for X and Y ("homex.g" and "homey.g", and portions of "home all" if X and Y of your ""homeall.g" is configured in such a manner. Sensorless homing requires the use of the [M913](/User_manual/Reference/Gcodes/M913) command. M913 allows one to control the miliamps (mA) from the maximum (100%) of the [M906](/User_manual/Reference/Gcodes/M906) command found in config.g. M906 controls the motor currents, while M913 controls the percent of that motor current that is running.

Your homex.g should look something like this:
```
M400 ; Wait for current moves to finish
M913 X70 Y70 ; drop motor current to 70%
M400 
G91 ; relative positioning
G1 H2 Z10 F12000    ; lift Z relative to current position
G1 H1 X-320.5 F10000 ; move quickly to X axis endstop and stop there (first pass)
G1 H2 X5 F12000     ; go back a few mm
G1 H1 X-320.5 F7000 ; move slowly to X axis endstop once more (second pass)
G1 H2 Z-10 F6000   ; lower Z again
G90 ; absolute positioning
M400
M913 X100 Y100 ; return current to 100%
M400
```

Your "homey.g" should look the same as this, just with Y variables instead of X. For "homeall.g" be sure to include the M400 M913 M400 at the beginning and end of the X and Y portions of "homeall.g."

For another example, M913 X40 Y70 would command 40% and 70% of the total power found in the M906 command in config.g respective of X and Y.

### 3: Adjust G1 feedrate parameters in all X and Y homing related commands

In the example in step 2, the F parameters of the G1 commands for X and Y are quite high. This is because for sensorless homing, your printer must obtain a minimum required speed. Keep F above 3600 (3600mm/min or 60mm/sec). Save your changes.

### 4: Configure M913 in homing files

Motor stalls are more easily identified by the firmware and quieter if the motor current is low. Using your existing motor current setting ([M906](/User_manual/Reference/Gcodes/M906) in config.g), configure your motor current reduction using the first [M913](/User_manual/Reference/Gcodes/M913) in the homing files. This is done through editing X and Y values which set the motor current to use during homing as a percentage of the M906 setting. Try to use as low a current as possible, such that the axis still moves with other settings (eg acceleration, jerk) set normally.  For example, if your X and Y motors were set to 1000mA in M906, try M913 X40 Y40 for 400mA on each axis. Save your changes.

At this point, restart your Duet board.

### 5: Test your changed homing files. 

The homing files that you change should be X and Y or "home all" and X and Y

At this point if you are getting a "crash" you should dial down the F parameter in the G1 commands or reduce the percentage of current that is used for sensorless homing (M913). Test and retest until satisfied.

# Stall Detection

Stall detection works in a very similar manner to Sensorless Homing, though tuning stall detection to work at normal motor current is much harder than stall detection at the reduced current used for sensorless homing. 

## Using Sensorless Homing AND Stall Detection

If you are going to use sensorless homing AND stall detection, move the M915 command that you use for sensorless homing to the start of each homing macro, and add a second M915 command at the end of each homing macro that reverts the setting to stall detection. Put the M915 command for stall detection in config.g, as outlined below

## Configuring Stall Detection

This include the necessary changes for config.g, rehome.g, and your slicer of choice

### 1: Enable your "rehome file"

[M915](/User_manual/Reference/Gcodes/M915) is a powerful command. It controls a dynamic system with many parameters. It is imperative to understand it. For a testing baseline, set your rehome.g M915 command to M915 X Y S3 R1 F1. This parameter combination sets up the M915 command to report the stall detections and to do nothing.  Save changes.

### 2: Enable M915 in your slicer

This step requires the same M915 command that is in your rehome.g file be at the END of your STARTING gcode script in your slicer of choice. Place it at the end of your start script gcode commands.

### 3: Include M915 X Y S3 R0 F0 at the BEGINNING of your ENDING gcode script in slicer of choice

This step is really finishing the work in Step 2. This tells the slicer and gcode file to set the M915 command back to sensorless homing instead of using stall detection. This is important because otherwise your printer will crash into the axes limits without detecting a stall when X and/or Y home at the end of a print.

### 4: Prepare for test prints

This step requires that you make quite a few test prints.  I recommend something like the benchy. Make sure your print speed is set high enough, or stall detection will not work. Try and stay above 40mm/s. Keep first layer slow for adhesion purposes.

### 5: Test prints for stall detection sensitivity

This is where it gets a little difficult. Stall detection can be a little tricky to calibrate.

This is not a one and done procedure, as each printer is a little different. There are five different variables to be concerned about at this point: 
1) belt tightness/slack; 
1) motor current ([M906](/User_manual/Reference/Gcodes/M906)); 
1) acceleration ([M201](/User_manual/Reference/Gcodes/M201)); 
1) Max speed change ([M566](/User_manual/Reference/Gcodes/M566)); and 
1) [M915](/User_manual/Reference/Gcodes/M915).

A baseline range for these commands (all found in your config.g) can be found below.:

M906 X300-500 Y300-500

M201 X300-500 Y300-500

M566 X300-800 Y300-800

M915 S1-6

These commands may also be changed from the terminal, which I highly recommend you do during your test prints. I have found my best results with stall detection with the above ranges.

Step 4 starts by making sure your M915 command in your rehome.g and slicer is M915 X Y S3 R1 F1. Start off by making sure the above listed commands are at the highend of the range. Once your print is going and stall detection is flashing in the console, start lowering each variable one at a time by 100. If you reach the bottom of the range and your stall detection is still triggering frequently, you need to lower your sensitivity.

Sensitivity is lowered by adjusted the M915 S parameter. A bigger number means less sensitivity. I recommend jumping from M915 S3 to M915 S7. When changing M915, remember, you must change it in your rehome.g file and your slicer.

NOTE 1: If you are at the bottom of the above range and you are still getting lots of false stall reports in the Duet Web Control's console, you need to decrease the sensitivity. Remember, a larger M915 Sn in your slicer and rehome.g file are required to test the change in sensitivity.

NOTE 2: When checking the console for false stall reports, motors will be listed as numbers, not traditional axis letters. This means Drive 0 is X, Drive 1 is Y, Drive 2 is Z, Drive 3 is E0, Drive 4 is E1.

### 6: Repeat Step 4 as many times as needed until dialed in

### 7: Once you are not getting any more false stalls, try and increase the parameters listed in Step 4.

Do not change your belt tension if it is not too tight. Once you have increased these parameters, run the last test print with the same sensitivity settings in M915 Sn. This will help fine tune your stall detection. If you are not getting any false stalls at this point, increase your sensitivity by one unit (remember, that means M915 Sn is smaller) and run a new test print with this new sensitivity. If you get no false stalls,  continue decreasing the sensitivity until you trigger false stalls, and begin with Step 4 again.

### 8: When you are satisfied that you are truly dialed in, you need to attempt a hard stall

A hard stall is when the print carriage or the print bed "crashes" or "jolts," resulting in a the print continuing, just in the wrong place. To mimick a hard stall, use a gloved hand (be super careful to not burn yourself! always safety) to jerk the print bed in another direction. If your print is moving fast enough, and your stall detection settings are dialed in, you will see a stall detection report regarding the axis you mimicked a hard stall on.

If you have no detection of the hard stall, be sure to go back to step 4, and also make sure to increase your print speed at this point. If 40mm/s is too slow for you, jump to 50mm/s.

### 9: Repeat these steps until you are confident with your results

Once you are satisfied, there is one more change to make. You must now change the Rn parameter of M915. M915 R1 must be changed to R2 or R3. M915 Sn R2 will pause the print and wait for you to press resume. M915 R3 will pause your print, rehome, and then resume. This must be changed in rehome.g and in your beginning slicer gcode script.

### 10: Enjoy your newly dialed in stall detection!

# Using extruder stall detection during filament loading

In firmware 1.20 and later you can use extruder motor stall detection to feed filament into the hot end until resistance is encountered. Here's how.

Procedure

### 1: Find a motor current that causes the motor to skip steps (not grind the filament)

If you try to push filament too fast through the hot end, or when the hot end is cold. Your normal extruder motor current may already be suitable, but you may want to use a lower one.

### 2: Using that motor current, find a stall detection threshold

Find the speed that causes motor stall to be reported when you extrude fast and resistance is encountered, but that is not triggered during normal acceleration at the start of a filament feeding move.

It may be helpful to use M122 to look at the SG min or SG max value when you send a large extrusion move and resistance is not encountered, and when resistance is encountered and the motor skips steps.  The lower the reported SG value, the higher the load; and vice versa. The relationship between the SG value and the M913 S parameter depends on the driver chip:

* For TMC2660, TMC5160 and TMS2160 drivers, the M915 S parameter (range -64..+63) is added to the internally-calculated SG value to give the reported SG value. A stall is reported when SG falls to zero.
* For TMC2209 drivers, the reported internally-calculated SG value is reported directly. The M915 S parameter (range -128..+127) is subtracted from 128 to put it in the range 0..255. A stall is reported when the SG value falls below this value.

### 3: Configure stall detection on your extruder motor

Use M915 and the stall detection threshold you found.

### 4: Create a "Load filament" macro file that does the following:

* Preheat the hot end, if desired (or send M302 P1 if you intend to use the macro when the hot end is cold)
* Use M913 Ennn to reduce the motor current, if necessary (see #1 above)
* Use M83 to select relative extruder motion, in case the macro is called when absolute extrusion is in effect
* If on a delta printer, send G91 to select relative movement (bug workaround for firmware 1.21 and earlier, see later)
* In case the motor is already stalled, retract a small amount of filament (e.g. 1mm) to ensure that it is not flagged as stalled.
* Send a G1 H1 Ennn Fnnn command with appropriate E and F parameters. The E parameter should be long enough for the filament to reach the hot end, and the F parameter should be fast enough to activate stall detection (see the H parameter in the M915 command). The H1 parameter means "stop at the endstop" as usual, and for an extruder the endstop is always stall detection.
* Use M913 E100 to restore the extruder motor current to 100%
* If your macro is intended to be used with the hot end hot, do whatever priming you want to do.

Here is an example file:

```
; Load T0 with PLA
G10 P0 S200 R160    ; set temperatures for PLA
T0                  ; select tool
M116                ; wait for temperatures to be reached
G1 E-0.3 F300       ; retract a little filament
M400                ; wait for move to finish before changing current
M915 E0 S3          ; set stall sensitivity
M913 E30            ; motor current to 30%
M83                 ; relative extrusion
G1 H1 E800 F6000    ; feed up to 800mm until stall
M913 E100           ; restore normal motor current
G1 E50 F120         ; extrude 50mm @ 2mm/sec
```

Known bug in firmware 1.20 and 1.21: if you send a G1 H1 E command on a delta machine and the printer is in absolute XYZ coordinate mode, it will fail with error "G0/G1: attempt to move delta motors to absolute positions". This is fixed in firmware version 2.0 and later. The workaround is to use G91 to set relative motion before doing the G1 H1 E command. Don't forget to use G90 to set it back to absolute afterwards if your macro subsequently does any XYZ movement commands!