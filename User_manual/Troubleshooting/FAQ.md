---
title: Troubleshooting FAQ
description: Frequently asked questions
published: true
date: 2022-01-18T15:17:59.590Z
tags: 
editor: markdown
dateCreated: 2021-12-05T22:36:20.508Z
---

<!--

[summary_image|355]

*image from the awesome [Flowalistik](https://www.thingiverse.com/thing:2239254)*-->

## What is the quickest way to update firmware on a working system from RRF 2 to RRF 3

If you still have access to DWC, upload these 3 zip files, one at a time in the system tab. Don't extract them. Reboot after each. Use M115 to verify the firmware has been applied.

[RepRapFirmware 2.05.1 update](https://github.com/Duet3D/RepRapFirmware/releases/download/2.05.1/Duet2Firmware-2.05.1.zip)
[RepRapFirmware 3.0 update](https://github.com/Duet3D/RepRapFirmware/releases/download/3.0/Duet2and3Firmware-3.0.zip)
[RepRapFirmware 3.3 update](https://github.com/Duet3D/RepRapFirmware/releases/download/3.3/Duet2and3Firmware-3.3.zip)

That will get your firmware and DWC up to date as of firmware 3.3.

You can see the [change logs here](https://github.com/Duet3D/RepRapFirmware/wiki/Changelog-RRF-3.x) on the Duet3D Github repository.

For your config, might be a good idea to run through the [RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/Start) and generate a fresh set for RRF3.

Backup your existing config files in the sys folder in case you want to switch back to RRF2. It is easy to switch back and forth, just upload the zip file for the version you want and then upload your config files.

These documents will come in handy during the conversion.
[Migrating from RRF2 to RRF3](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3)
[GCode dictionary](/User_manual/Reference/Gcodes)

## Trying to update firmware and get a warning about a firmware folder

Starting in version 3.3 there is a firmware folder in the root of the SD card. During a firmware update initiated by uploading a firmware file to the DWC the files will now go into this firmware folder to keep the system folder a bit tidier. Normally it would get created automatically, but if that doesn't happen and you get an error that the folder is missing, please create it manually in the root of the SD card using a PC. All firmware .BIN files can be moved into this folder.

## How can I do a diagnostics check?

The two main ways to get some diagnostic information is to send M122 and M98 P”config.g” to check for problems. M122 provides a look at basic and advanced system information and M98 P"config.g" will execute your config files as a macro and will show any of the syntax errors that may otherwise get missed during startup.

## My Z axis won't home, it crashes into the bed (or endstop)

Read [Configuring RepRapFirmware for a Cartesian printer: Homing Z](/User_manual/Machine_configuration/Configuration_cartesian#homing-z)

## I get this error message: "Error: G0/G1: insufficient axes homed"

Recent firmware versions do not allow axes to be moved before they have been homed. The only movements allows are homing moves (G1 moves with S1 or H1 parameter) and individual motor moves (G1 moves with S2 or H2 parameter). So any Z movements that your homing files make before Z is homed should use the S2 parameter. Alternatively, add M564 H0 to config.g to allow axis movement before homing.

## What do I need to know to convert an existing printer to use the Duet?

See here: [Adapting an existing printer to Duet](/User_manual/Overview/Adapting)

## I'm having trouble with the BLTouch Z Probe.

See the [BLTouch Troubleshooting](/User_manual/Troubleshooting/BLTouch_troubleshooting) page.

## I can control fans 0 and 2, but not fan 1

Fan 1 output is configured in thermostatic mode by default in RRF 2.x, and also turns on automatically when the processor is started up or reset. This is for added safety when you use it to control the hot end fan. See [Connecting and configuring fans: Thermostatically controlled fans](/User_manual/Connecting_hardware/Fans_connecting#thermostatically-controlled-fans) and also the following section on that page.

## I upgraded from firmware 1.18.2 or earlier to firmware 1.19 or later, and now when I try to probe the bed using G32, G29 or G30, it makes unexpected movements

Delete the deployprobe.g and retractprobe.g files in the /sys folder of the SD card, unless you have set them up specifically to handle a type of Z probe that needs to be deployed (e.g. BLTouch).

## When my print finishes, it retracts all the filament out of the printer

You have configured your slicer to use absolute extrusion coordinates, then you have used a relative extrusion command in your slicer end gcode script to retract a little filament, without switching to relative extrusion mode first.

* We recommend you set your slicer to generate relative extruder coordinates. Include the [M83](/User_manual/Reference/Gcodes/M83) command in your start gcode.
* If your slicer doesn't support generating relative extrusion coordinates, place either a M83 command or a G92 E0 command before the G1 E-xxx in your end gcode that you want to retract a little filament.

## When I try to home my printer, sometimes the carriage or carriages move only part of the way towards the homing switches, and I have to command it to home again to make it go all the way

If your printer is a delta, open the homedelta.g file in the System Editor of the web interface. Look for the first G1 S1 line in that file. The X, Y and Z parameters on that line are the maximum distances that the carriages will move up when homing. Increase them to be a little larger than the maximum distance that the carriages can ever be below the homing switches.

If your printer is a Cartesian, CoreXY etc. the solution is similar. For example, if the X axis sometimes fails to home all the way, look for the first G1 S1 X command in homex.g and increase the X parameter to at least the length of the axis. Do the same in homeall.g.

## My CoreXY doesn't move in the correct direction or prints are mirrored.

CoreXY motion will require some configuration to ensure a correct right handed coordinate system. Please follow the CoreXY setup guide here, specifically the testing motor movement section: [Configuring RepRapFirmware for CoreXY printer](/User_manual/Machine_configuration/Configuration_coreXY)

## Some Features of Duet Web Control not working

This is often caused by using an incompatible browser. Chrome and Firefox work well with Duet Web Control. Microsoft Internet Explorer and older versions of Edge and Safari may have problems ([see here for details](/User_manual/Reference/Duet_Web_Control_Manual#browser-compatibility)).

DWC file editor works in Safari 10.0.1 and later. (Won't work in any earlier versions), from the thread linked above.

If this is not the case ensure you are running the [latest stable versions of the firmware](/User_manual/RepRapFirmware/RepRapFirmware_overview#checking-firmware-versions) and Duet Web Control

## My prints come out mirrored

3D printers must be configured to use a right-hand coordinate system. If you have configured your printer to use a left-hand coordinate system, your prints will come out mirrored.

Looking at the front of the printer. the most common arrangement using a right-hand coordinate system is:

* +X moves the print head to the right
* +Y moves wither the print head away from you (towards the rear of the printer), or the bed towards you (towards the front of the printer)

A few printers (e.g. the Ormerod) have +X moving the head towards you, and +Y moving either the head to the right or the bed to the left.

On a delta printer, the X tower should be on the left, the Y tower on the right, and the Z tower at the back.

## My Duet won't respond

See [What to do if your Duet won't respond](/User_manual/Troubleshooting/Duet_wont_respond).

## I can't connect over wifi, or get disconnected from DuetWebControl.

See [WiFi troubleshooting](/User_manual/Troubleshooting/WiFi_troubleshooting).

## Cannot start wifi after a firmware update

Occasionally after a firmware update the wifi module will fail to start correctly. This is usually caused by a failed wifi module firmware update. Reflashing the module by sending `M997 S1` over USB terminal should solve the problem. If it happens while updating from RRF 3.2 or earlier to RRF 3.3, send `M997 S1 P"0:/sys/DuetWiFiServer.bin"`

## My temperature shows as -273c or 2000c

A disconnected (open circuit) thermistor shows -273C in RRF. Other temperature sensor faults give a reading of 2000C, which triggers thermal protection as a safety. In general, check your wiring first and then ensure thermistor configuration is correct and uses the right syntax for your firmware version. i.e. RRF2 vs RRF3. 

## Missing IAP file error during firmware update

When doing a firmware update you may receive a missing IAP file error if the correct IAP file isn't present on the SD card in the /sys folder along with the firmware. For example, iap4e.bin. IAP stands for In Application Programming and it is the file responsible for facilitating the firmware flashing process. Over time the IAP files have changed to allow for newer and larger firmware bin files to be flashed. The correct IAP BIN files are generally included with each major release requiring them. You're unlikely to see this error if you have been upgrading your firmware along with their release. It's more likely to occur when jumping from an older firmware to the newest and skipping over many releases. To correct the problem, you can likely find the IAP file in question in the last major release.

## M307: Heater 1 appears to be over-powered. If left on at full power, its temperature is predicted to reach 521C

You may receive this warning after running a PID tuning when the tuned PID values are loaded at startup. It is an informational warning telling you what temperature the firmware algorithm suspects the heater is capable of reaching in a full power runaway heater situation. It is meant to encourage you to either choose a less powerful heater that is physically unable to reach such a temperature, or to take precautions to mitigate the effects of an uncontrolled heater situation, such as adding a thermal cut out spec'ed appropriately to cut power to the heater if a temperature is exceeded. At the very least, do not leave your printer unattended, have a smoke detector present, and have a fire extinguisher handy.

## Can I print over a USB connection?

Yes, but with limitations. See here: [Firmware Overview: Printing over USB connections](/User_manual/RepRapFirmware/RepRapFirmware_overview#printing-over-usb-connection-octoprint-cura-simplify3d-etc)

## G0/G1: target position not reachable from current position

Usually this means you have a delta printer and your homedelta has a move to position the print head at center. However, it can't reach that point because it would require moving one of the carriages up further than it physically could. The solution is to lower the print head first in Z and then command it to center in X Y.

## Job Layer not showing anymore in firmware 3.3 and beyond

RepRapFirmware no longer tries to work out what layer number is printing, and no longer provides an estimate of print completion time based on layer progress. The mechanism to work out the layer number failed in many cases, for example when the slicer used variable layer heights or printed multiple objects one at a time. The removal of these 200 lines of hard-to-maintain code has made more space available for other features on the older Duets that are tight on memory space. A consequence of this change is that DWC will no longer produce a layer chart if the GCode file being printer does not include layer number comments. For slicers that do not normally generate these comments (e.g. PrusaSlicer) it is usually possible to add a layer change script to generate them.

See [this forum thread](https://forum.duet3d.com/topic/23826/layer-number-always-null) for a work around: 