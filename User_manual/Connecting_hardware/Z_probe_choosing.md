---
title: Choosing a Z probe
description: 
published: true
date: 2024-10-28T16:34:13.553Z
tags: 
editor: markdown
dateCreated: 2021-10-26T13:24:45.655Z
---

The type of Z probe used can have a significant effect on the quality of first layer achieved. For delta printers using an appropriate Z probe is important for accurate auto calibration.

There are many types of Z probe available. This table may help you to choose one suitable for your printer.

| Type | Size | Weight | Compatible bed surfaces | Precision | Remarks |
|:---|:---|
| Duet3D Smart Effector | Medium | Light | Any | Very high | Intended for delta printers only, but has been used on at least one CoreXY printer. See [Duet3D Smart Effector](/Duet3D_hardware/Accessories/Smart_Effector) |
| DC42 IR Sensor | Small | Light | Most | Usually high when used with opaque bed surfaces, medium or low with transparent surfaces | Transparent surfaces (glass, PEI etc.) must be backed with a matt black surface and must not be coated with glue, hairspray etc. See [dc42's blog](https://miscsolutions.wordpress.com/mini-height-sensor-board/). |
| Duet3D Scanning Z Probe | Medium/Small | Light | Steel, or aluminium without glass on top (thin PEI is OK) | high but requires calibration | Due to temperature variation of inductive sensing, requires a calibration step and/or temperature compensation. When combined with a Duet 3 Roto toolboard, the sensor coil is small and light. If the scanning Z probe control board is required then the light sensor coil is combined with a small additioal control board making the overall size larger, although the control board can be mounted further away from the bed on the effector assembly. See [Duet 3 Scanning Z Probe](/Duet3D_hardware/Duet_3_family/Duet_3_Scanning_Z_Probe) |
| Hot end mounted piezo | Medium | Light | Any | Very high | See [precisionpiezo.co.uk](https://www.precisionpiezo.co.uk/shop). |
| Under-bed FSRs or piezos | Small | Light | Any | High or medium | For FSRs use JohnSL interface board. Mainly used with delta printers. Trigger height may vary a little with probing position. |
| Inductive | Large | Heavy | Steel, or aluminium without glass on top (thin PEI is OK) | Medium | Most types need 12V or 24V power. Trigger height may vary with supply voltage and temperature. |
| Capacitive | Very large | Heavy | Any | Low | Trigger height is affected by temperature and humidity |
| BLTouch | Medium | Medium | Any | Medium | Needs an expansion pin assigned for probe control. |
| Servo deployed switch | Medium | Medium | Any | Medium | Needs an expansion pin assigned for probe control. See [http://reprap.org/wiki/Z_probe#Microswitch](http://reprap.org/wiki/Z_probe#Microswitch). |
| Magnetic/Other Mechanism dock/undock Switch | Medium | Medium | Any | Medium | Uses a docking mechanism with magnets/slot or some other way of pickup up and putting down the switch repeatedly and reliably (e.g. [Elucid Probe](https://euclidprobe.github.io/)). See [http://reprap.org/wiki/Z_probe#Microswitch](http://reprap.org/wiki/Z_probe#Microswitch). ||

**Important note!**

If your Z probe is separate from the nozzle (i.e. all the above types except the Smart Effector, piezo mounted in the hot end, or FSRs/piezos under the bed), then if the print head tilts as it moves in the XY plane then the relative heights of the nozzle and the Z probe will vary with the tilt. This will cause apparent variations in the trigger height with XY position. This effect can be minimised by mounting the Z probe as close to the nozzle as possible. The DC42 IR sensor is designed to be small enough to fit below the heatsink of a E3Dv6 hot end.

Tilt is not usually a serious problem with Cartesian and CoreXY printers, but it is hard to eliminate it completely on delta printers. Therefore, nozzle contact probes are recommended for use on delta printers.

More information on Z probes:

* Information on [Connecting a Z probe](/User_manual/Connecting_hardware/Z_probe_connecting)
* How to [Test and calibrate a Z probe](/User_manual/Connecting_hardware/Z_probe_testing)
