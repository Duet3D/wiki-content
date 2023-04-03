---
title: Mesh bed compensation
description: 
published: true
date: 2022-11-04T11:52:31.958Z
tags: 
editor: markdown
dateCreated: 2021-10-28T15:16:32.300Z
---

# Introduction

Mesh bed compensation (MBC) allows the printer adjust the nozzle height during printing to compensate for an uneven bed or for sag in the gantry.

Mesh bed compensation works like this:

* On power up no mesh bed compensation is in use
* You define a grid using the [M557](/User_manual/Reference/Gcodes/M557) command. Normally you do this in config.g.
* You run [G29 S0](/User_manual/Reference/Gcodes/G29) (or just G29) to probe the bed at the mesh points. If probing is successful, the bed height map is saved in a file on the SD card and is or can be displayed in Duet Web Control.
* You can load a previously-generated height map using command G29 S1.
* You can stop using a height map by sending command G29 S2.

# Prerequisites

* You must be running RepRapFirmware 1.17 or later. Viewing the height map in the web interface requires Duet Web Control 1.14 or later.
* If your printer is Cartesian or CoreXY, get the bed as level as you can by manual or automatic mechanical adjustment. See [Bed levelling using multiple independent Z motors](/User_manual/Connecting_hardware/Z_probe_auto_levelling) and [Using the Manual Bed Levelling Assistant](/User_manual/Connecting_hardware/Z_probe_manual_levelling). 
* Bed compensation increases wear on the Z axis moving parts, so you don't want to be doing more of it than necessary.
* If your printer is a Delta, calibrate it first (see [Calibrating a delta printer](/User_manual/Tuning/Delta_calibration)). Bed compensation is not a good substitute for accurate calibration.
* If you are using a Z probe, make sure it is correctly configured and working. This includes the trigger height and the X and Y offsets of the probe relative to the nozzle. Refer to the Z Probe calibration page. [Test and calibrate a Z probe](/User_manual/Connecting_hardware/Z_probe_testing)
* If you don't have a Z probe, set type P0 in your M558 command for manual probing via jog buttons.

# Configuration

## Checking the trigger height

The Z probe trigger height must not vary significantly with XY position. Measure the trigger height of your Z probe at various XY positions and make sure it is consistent. To measure the trigger height:

1. Make sure there is no filament stuck to the nozzle (you may want to do this test with the nozzle hot)
1. Cancel any existing bed compensation by sending [M561](/User_manual/Reference/Gcodes/M561)
1. Send [M564 S0](/User_manual/Reference/Gcodes/M564) to allow movement lower than where the firmware thinks Z=0 is. This is to ensure that you can lower the nozzle all the way to the bed in step 5.
1. Command the print head to the XY coordinates you want to probe. This can be any place where both the nozzle and the probe are over the bed. Either use the jog buttons in DWC, or send a G1 command (eg G1 X100 Y100) to move to a specific location.
1. Command the print head down in small steps, using the jog buttons in DWC or by sending individual G1 Z[value] commands, until the nozzle just touches the bed or just grips a sheet of paper. You may also wish to create a macro to lower the head by e.g. 0.02mm to get greater accuracy (G91 followed by G1 Z-0.02).
1. Send G92 Z0 to define that height as Z=0
1. Command the nozzle up 5mm (G1 Z5)
1. Send G30 S-1 to probe the bed without resetting the Z=0 position
1. Read off the Z height from the web interface. That is the trigger height.
1. Repeat the previous two steps a couple of times to make sure you are getting a consistent result
1. Repeat steps 4-10 for the other XY positions you want to check.

On a delta printer, it can be quite hard to achieve a consistent trigger height. Z probes that are mounted on the effector are sensitive to any tilting of the effector, because it affects the relative heights of the Z probe and the nozzle. You can reduce this effect by mounting the probe as close to the nozzle as possible. Probing solutions that use the nozzle itself as the probe are usually not affected by effector tilt, but some types are sensitive to the XY position of the nozzle for other reasons.

Once you are satisfied with your trigger height result (should be between 0 and 4mm, depending on your printer), open your config.g file (this can be found in your DuetWebControl), find the G31 command and enter your trigger height in the Z parameter (ex. G31 X0 Y0 Z1.3).

## Define the mesh size and spacing

Use the [M557](/User_manual/Reference/Gcodes/M557) command to define the spacing of the mesh that you want to set up, and either the X and Y limits or (for a delta printer) the probing radius.

Example for a Cartesian or CoreXY printer:

```
M557 X10:190 Y10:190 S20 ; probe from X=10 to 190, Y=10 to 190mm with a mesh spacing of 20mm
```

Example for a delta printer:

```
M557 R130 S20 ; probe within a radius of 130mm from the centre with a mesh spacing of 20mm
```

There is a firmware-dependent limit of 441 on the number probe points allowed. If you exceed that limit, an error message will be returned when you run the M557 command, and you should increase the mesh spacing (S parameter) to reduce the number of probe points.

Using a small number of points (ie. 9) is akin to using the older style simple planar correction.

# Running bed mesh compensation

## Establish a Z=0 datum

Before you probe the bed using G29 S0, you must establish a Z=0 datum using the Z probe. This must be done at the same temperatures that you use when probing the bed. It's normally best to probe with the bed and extruder at printing temperatures. If you are using a nozzle-contact Z probe and the bed surface might be damaged by a nozzle at full printing temperature, use a slightly lower nozzle temperature instead. So preheat the bed and hot end. Once the bed and nozzle are up to temperature, establish a Z=0 datum by one of the following methods:

* If your printer is set up to home Z using the Z probe, you can just home Z.
* Otherwise, do one of the following:
  * Move the nozzle over the centre of the bed and do a single G30 probe (G30 without parameters).
  * Alternatively, if the printer is a delta, run auto calibration.
  * Alternatively, if the printer has multiple independent Z leadscrews and you have set up leadscrew bed levelling, run leadscrew bed levelling (G32)

Note, whichever of these techniques you use, you must do exactly the same any time you want to use G29 S1 to reload the height map.

## Probe the bed

Use the command G29 (or G29 S0) to run the probing sequence. All points in the mesh that are reachable by the probe will be probed. When probing is complete, a message will be returned indicating whether or not it was successful. if it was successful, the height map will also be saved on the SD card.

Multiple heightmaps can be saved with unique names using G29 S3 P"filename.csv". The heightmap can then be loaded at a later time with G29 S1 P"filename.csv". G29 S1 will load the default "heightmap.csv"

## Examine the height map

If you sent the G29 command from Duet Web Control, it should display the height map automatically. Otherwise you can view the height map in Duet Web Control by right-clicking the arrow next to the Auto Bed Compensation or Auto Delta Calibration button and selecting "Show Mesh Grid Heightmap" from the drop-down menu.

The height map file is normally called **/sys/heightmap.csv** but you can specify a different filename in G29, M374 and M375 commands. You can view, edit or download it using the System Editor tab in Duet Web Control.

# Notes

## MBC with delta auto calibration and ABL

Running G32 to auto calibrate a delta printer or to adjust leadscrews to level the bed will clear the local height map info (but not the copy in heightmap.csv). Therefore, if you wish to use both auto calibration/leadscrew bed levelling and mesh bed compensation, you must run G32 **before** you run G29, not after. You can include a G29 S0 or G29 S1 command at the end of your bed.g file if you wish.

## Loading a previously saved height map

* Preheat the printer and establish the Z=0 datum in exactly the same way that you did before you ran bed probing - see *Establish a Z=0 datum* section above.
* Then use command G29 S1 to load the height map from the SD card.

Multiple heightmaps can be saved with unique names using G29 S3 P"filename.csv". The heightmap can then be loaded at a later time with G29 S1 P"filename.csv". G29 S1 will load the default "heightmap.csv"

Do not use G29 S1 within config.g, because no Z=0 datum has been established at that point. If you wish to load the height map automatically, here are some options:

* If you run delta auto calibration of leadscrew bed levelling between powering on the printer and your first print (or before every print), you can add G29 S1 to the end of your bed.g file.
* If you home Z using G30 commands in homez.g and homeall.g, you can use G29 S1 in those files, after the G30 command.

## Compensation Taper

* You can use the [M376](/User_manual/Reference/Gcodes/M376) command to taper off bed compensation over a specified height
* Example: M376 H10 ; Hnnn Height (mm) over which to taper off the bed compensation
* This command specifies that bed compensation should be tapered linearly over the specified height, so that full bed compensation is applied at Z=0 and no bed compensation is applied when Z is at or above that height. If H is zero or negative then no tapering is applied, so compensation is performed throughout the entire print.
* RepRapFirmware does not adjust the extrusion factor to account for the layer height varying when tapered bed compensation is used. Therefore it is recommended that the taper height be set to at least 20x the maximum error in the height map, so that the maximum amount of the resulting over- or under- extrusion is limited to 5%.
* Taper is useful for cases where the bed is curved slightly and will eventually be corrected for by the extrusion as it rises. 
* You should not use taper in cases where the curvature is due to a sagging gantry, as that curvature will persist for the entire duration of the print.

## Summary of gcode commands related to mesh bed compensation

* **[G29](/User_manual/Reference/Gcodes/G29)** Run file sys/mesh.g, or if that file isn't found then do G29 S0
* **G29 S0** Probe the bed and save height map to file
* **G29 S1** Load height map from file
* **G29 S2** Clear height map
* **G29 S3 P"filename"** Save height map to file
* **[G30](/User_manual/Reference/Gcodes/G30)** Probe the bed at a single point (can be used to measure Z probe trigger height)
* **[G31](/User_manual/Reference/Gcodes/G31)** Set Z probe trigger height, threshold and offsets from the print head reference point
* **[G32](/User_manual/Reference/Gcodes/G32)** Run sys/bed.g file. You can put commands in bed.g to perform mesh bed levelling, e.g. M401 followed by G29 S0 followed by M402.
* **[M374](/User_manual/Reference/Gcodes/M374)** Save height map to file
* **[M375](/User_manual/Reference/Gcodes/M375)** Load height map from file (same as G29 S1)
* **[M376](/User_manual/Reference/Gcodes/M376)** Set bed compensation taper height
* **[M401](/User_manual/Reference/Gcodes/M401)** Deploy Z probe (runs sys/deployprobe#.g file where # is the Z probe number, or file sys/deployprobe.g if that file isn't found).
* **[M402](/User_manual/Reference/Gcodes/M402)** Retract Z probe (runs sys/retractprobe#.g file where # is the Z probe number, or sys/retractprobe.g if that file isn't found)
* **[M557](/User_manual/Reference/Gcodes/M557)** Define the probing grid
* **[M558](/User_manual/Reference/Gcodes/M558)** Set Z probe type, dive height, probing speed, travel speed between probe points, and probe recovery time
* **[M561](/User_manual/Reference/Gcodes/M561)** Clear height map (same as G29 S2)

# Troubleshooting

If you get jerky movements when the head moves across the bed at speed with mesh bed levelling enabled, but not with it disabled, this indicates that your Z jerk setting is too low for the travel speed you are using and the amount of compensation needed. Increase the Z parameter in the M566 command in config.g, or use a lower travel speed.

If the heightmap.csv file contains a column of zeros at the start or the end, this indicates that the corresponding X coordinate was not reachable by the probe, taking account of the bed size specified in the M665 command on a delta printer or the M208 commands for other printers, and the probe offset specified by the X and Y parameters in the G31 command. Similarly, if there is a row of zeros then the corresponding Y coordinate was not reachable.

If you are manually editing heightmap.csv note that the firmware uses 0.0 to mean zero height error and 0 to mean that the point was not probed so the height error needs to be extrapolated.