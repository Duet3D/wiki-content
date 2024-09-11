---
title: Scanning Z Probe Calibration
description: Setting up and calibrating scanning Z probes
published: true
date: 2024-09-11T15:22:58.099Z
tags: 
editor: markdown
dateCreated: 2023-10-17T16:07:33.512Z
---

# Introduction

Induction-based probes need to have the sensor drive level calibrated and the result saved to config.g before using it as a scanning Z probe (SZP).

# LDC1612-based probes

Duet3D have boards that use the LDC1612 chip, including the [Duet 3 Scanning Z Probe](/Duet3D_hardware/Duet_3_family/Duet_3_Scanning_Z_Probe) and the [Duet 3 Roto Toolboard](/Duet3D_hardware/Duet_3_family/Duet_3_Roto_Toolboard).

## Mounting

* Duet3D supply two coils with the Duet 3 Roto toolboard and Duet 3 Scanning Z Probe, a 12mm coil and a 15mm coil. In theory the 12mm coils are higher resolution than the 15mm coils but we need more testing to confirm which is best, so we provide both.
* Make sure there is no metal in the 30mm ABOVE the coil, or it will pick this up and give false readings
* The bottom of the SZP coil should be around 1 to 2mm above the tip of the nozzle. The G31 Z trigger height can be set to around 2mm, so the coil is 4mm from the bed, to avoid the nozzle contacting the bed while keeping the coil close enough to the bed to get an accurate reading.
* Route the SZP ribbon cable away from the coil, ie make sure it doesn't go over the coil
* Ensure that the ribbon cable is FULLY inserted into the tiny connector, at both ends. Try reseating this if you continue to get erratic readings
* You can use M2.5 countersunk screws (often supplied with smaller fans) to mount the coil, which stick out about 0.7mm. M3 countersunk screws stick out a bit further. There are M3 screws with low profile heads, usually between 0.8mm and 1mm; search for "m3 ultra low profile thin head".

## Configuration

The following Gcodes are used to configure the SZP:
[M558](/User_manual/Reference/Gcodes/M558) - Sets probe number (K parameter), type (P), input pin (C), feed rate (F) and travel speed (T)
[M308](/User_manual/Reference/Gcodes/M308) - Set SZP temperature sensor
[G31](/User_manual/Reference/Gcodes/G31) - Sets probe offsets, adjustments for temperature
[M557](/User_manual/Reference/Gcodes/M557) - Define mesh grid

Add the following to your config.g. Some values will be dependent on your specific setup. See the Calibration section below for determining the M558.2 values.

```
; Scanning Z probe
M558 K1 P11 C"120.i2c.ldc1612" F36000 T36000    ; configure SZP as probe 1, type 11, on CAN address 120
M308 A"SZP coil" S10 Y"thermistor" P"120.temp0" ; thermistor on SZP coil
G31 K1 Z2 Y-17                                  ; define probe 1 offsets and trigger height
M558.2 K1 S15 R101133                           ; set drive current and reading offset
M557 X-140:140 Y-90:80 S10                      ; Define mesh grid for probe 1 (overwrites probe 0 mesh grid)
```

### Notes

* In M558, the K parameter sets the probe number. If you have more than one probe, you need to set the K parameter so each probe is unique. If there is no K parameter then 0 is used. 
* In M558, the C parameter defines the CAN address ("120") and the pin name ("i2c.ldc1612"). You may need to change the CAN address to your specific configuration.
* Measure the G31 offset of the probe from the nozzle in X and Y: [Test and calibrate a Z probe - measuring probe x y offset](/User_manual/Connecting_hardware/Z_probe_testing#measuring-probe-x-y-offset)
* M308 P parameter pin name is `120.temp0` for Duet 3 Scanning Z Probe, and `121.temp2` for Duet 3 Roto Toolboard.
* Set the G31 Z trigger height to the height where the SZP coil is 4-5mm from the bed. The coil should be mounted higher than the nozzle, but not so high that the nozzle hits the bed when scanning.

## Calibration

[M558.1](/User_manual/Reference/Gcodes/M558_1) - Calibrate height vs reading of scanning Z probe 
[M558.2](/User_manual/Reference/Gcodes/M558_2) - Calibrate or set drive level and reading offset for scanning Z probe

### Drive level and reading offset

To calibrate drive level and reading offset: 
* Position the sensor above the bed at the minimum height that you expect to use it. 
  For example, if the trigger height is set to 2mm (G31 Z2), and the bed error is expected to be not more than 1mm, the minimum height would be 2mm. 
* Run `M558.2 K1 S-1` to calibrate the drive level. If it is successful then it should report the resulting drive level. 
  * If it is unsuccessful, send the command again a few more times, until you get a positive result
  * If it is continuously unsuccessful, check the ribbon cable is seated properly in the connectors, the ribbon cable is away from the coil, and there's no metal above the probe (see 'Mounting' above)
* You can also use `M558.2 K1` with no S parameter to report the current drive level (S parameter) and reading offset (R parameter). 
* Add an M558.2 command in config.g to set that drive level and reading offset. E.g. if the probe number is #1,and the reported drive level after calibration was 15 and reading offset is 101133, then use `M558.2 K1 S15 R101133`. Add this later in config.g than the M558 K1 command that configures the probe.
* Also check that the Z probe reading is sensible when the sensor is a long way above the bed. The aim is to get sensible readings (i.e. not 999999) from the minimum height to "infinite" height.
* A typical drive level is around 15 for the 12mm coil. Higher or lower drive level settings may cause sporadic 999999 readings. If you get these, try adjusting the Z height you do the `M558.2 K1 S-1` calibration at.

### Height vs reading

The height vs. reading then needs to be calibrated, using [M558.1](/User_manual/Reference/Gcodes/M558_1). If you have another way of determining Z=0 (e.g. another Z probe, or touch the nozzle to the build plate and set G92 Z0) then it's best to do this immediately before scanning rather than try to save the calibration. An example of this is in the mesh.g file shown below. 

To calibrate manually:
* Move the probe so it is over your normal Z datum reference point, ie when you home Z.
* Send an appropriate M558.1 command. The K parameter chooses the probe number. The S parameter defines the range either side of the trigger height that the probe will scan, and use for calibration.
* For example, a trigger height of 2mm (set by `G31 K1 Z2`) means the nozzle is 2mm from the bed, and the coil is 4mm from the bed, when triggered. Setting an S parameter of less than 2, eg 1.7, will calibrate the probe in the range 3.7mm to 0.3mm, ie 2+1.7 to 2-1.7. Send `M558.1 K1 S1.7`.
* After calibrating, send `M558.1 K1` to see the results. It should report something like:
  ```
  M558.1 K1
  Scanning probe coefficients [1.840e-1, -2.865e-4, 2.598e-10, 2.937e-17]
  ```
  This can be saved to config.g, but note that SZP coil readings change with temperature, so this calibration may not be suitable at all temperatures. That is why it's worth doing each time you do a bed scan.
  
## Example mesh.g file

An example mesh.g file, which is run when a [G29](/User_manual/Reference/Gcodes/G29) command is sent (to do a bed mesh scan), might be:

```
T-1
M208 S0 Y100
G29 S2
G1 X0 Y0 F30000
G30
if exists(param.K) & param.K=1
  G1 Z6  ; to avoid backlash
  M558.1 K1 S1.7
G29 S0 K{exists(param.K) ? param.K : 0}
G1 X0 Y-150 F18000
M208 S0 Y49
```

Note that in the above example:
* the scanning Z probe is K1. Probe K0 is used to home Z before doing this scan. 
* The T-1 is there because this is a tool changer and the scanning probe is on the pickup head. 
* The M208 commands are likewise specific to a tool changer. 
* The G1 X0 Y0 command sends the probe to the centre of the bed (on a machine with the X0 Y0 origin in the centre). 
* The M558.1 does the height vs reading calibration. In this case, the S1.7 causes the probe to move 1.7mm either side of the trigger height. If the trigger height is 2mm, it will go from 3.7mm down to 0.3mm.

A simplified mesh.g file might be:

```
M557 X-135:145 Y-145:145 P10 ; Define grid for mesh bed compensation, origin in bed centre
G29 S2                       ; Disable mesh bed compensation
G28 Z                        ; Home Z
G1 Z6                        ; To avoid backlash move to point higher than start of calibration
M558.1 K1 S1.7               ; Calibrate probe
G1 Z6                        ; Move up at end of calibration
```

# Using the SZP to set Z height

A scanning Z probe is designed to do just that; scan the bed to create a bed mesh. With careful calibration it should be possible to use it as a probe to set the Z height. However, inductive probes are susceptible to temperature, and will give different readings at different temperatures at the same height.

Testing is currently ongoing to produce a good method to calibrate the SZP for different temperatures. These instructions will be updated when testing is complete, and recommendations can be made.

For now, we recommend using another method to set the Z height.

# Other analog probes

To come.