---
title: Configuring the on-board MCU and stepper driver temperature sensors
description: 
published: true
date: 2022-09-13T17:19:14.137Z
tags: 
editor: markdown
dateCreated: 2021-09-14T15:59:19.157Z
---


# Scope

**This document is relevant to:** all Duet boards
**Firmware versions:** all firmware versions

# Introduction

All Duet mainboards are able to monitor the MCU (microcontroller) and the stepper driver temperatures. Duet Web Control 1.14 and later display the Duet current microcontroller temperature, and the user will be alerted if stepper drivers are overheating. However,these temperatures are not recorded or graphed. You can create a temperature sensor for each of these, and display it in the 'Extra' view of the 'Tools' panel in DWC. The temperature sensors can also be used to control fans to cool the Duet board.

**MCU-temp** - is reported in degrees Celsius. Note that the MCU temperature must be calibrated to get an accurate temperature reading. See the Calibrating the MCU temperature section below.

**Stepper driver temperature** - The Trinamic drivers used on Duets do not report temperature, rather they report one of: temperature OK, temperature overheat warning, and temperature overheat error. RRF translates these three states into readings of 0C, 100C and 130C.

# Firmware configuration

# Tabs {.tabset}

## RepRapFirmware 3.x

To show the MCU and stepper driver temperature in RepRapFirmware 3.x and later, use [M308](/User_manual/Reference/Gcodes/M308) to define the sensor, which will be listed in the 'Extra' view of the 'Tools' panel.

```
M308 S10 Y"mcu-temp" A"MCU" ; defines sensor 10 as MCU temperature sensor
M308 S11 Y"drivers" A"Duet stepper drivers" ; defines sensor 11 as stepper driver temperature sensor
M308 S12 Y"drivers-duex" A"Duex stepper drivers" ; for Duet 2 WiFi/Ethernet with DueX2/5, defines sensor 12 as DueX2/5 stepper driver temps 
```
To use temperature sensors to drive a fan to cool the board, define the sensor with [M308](/User_manual/Reference/Gcodes/M308), the fan with [M950](/User_manual/Reference/Gcodes/M950), then configure the fan with [M106](/User_manual/Reference/Gcodes/M106), for example:
```
M308 S3 Y"mcu-temp" A"MCU" ; configure sensor 3 as on-chip MCU temperature sensor
M950 F2 C"fan2" Q100 ; create fan 2 on pin fan2 and set its frequency                        
M106 P2 H3 T40:70 ; set fan 2 value
```

## RepRapFirmware 2.x

To show the MCU and stepper driver temperature in RepRapFirmware 2.x and earlier, use [M305](/User_manual/Reference/Gcodes/M305) to define the sensor, which will be listed in the 'Extra' view of the 'Tools' panel.

```
M305 P100 S"MCU" 
M305 P101 S"Duet drivers"
M305 P102 S"Duex drivers"
```
To set the temperature sensor to drive a fan to cool the board, use [M106](/User_manual/Reference/Gcodes/M106):
```
M106 P2 T45:65 H100
```

# MCU temp in Duet 3 Mini 5+

The SAME54P20A chip used in the Duet 3 Mini 5+ does not have a functioning temperature sensor. In theory it does have an on-chip temperature sensor, but the errata document for the chip says it doesn't work.

However, experimental support for the Duet 3 Mini 5+ on-chip MCU temperature sensor has been added in RepRapFirmware 3.3 beta 3. As the chip manufacturer advises that it is not supported and should not be used, we can't promise that it will give useful readings on all boards. It will be removed if it causes significant support issues. Please report any issues in the [Duet3D support forum](https://forum.duet3d.com/).

If you are using a version of RepRapFirmware earlier than v3.3 beta 3, you will be unable to monitor the MCU temp in order to control fans. The driver sensors do work for fan control, but only return flags of warning and overtemp, so fine grained control of fan speed would not be possible with a separate thermistor.

# Calibrating the MCU temperature

Duet Web Control 1.14 and later display the Duet microcontroller temperature. However, the on-chip temperature sensor may not be intrinsically accurate and needs to be calibrated to produce accurate results.

The uncalibrated microcontroller temperature on the Duet 2 (WiFi and Ethernet) should be within 13C of the true temperature. For the Duet 3 MB6HC it could be up to 39C out.

To calibrate the temperature sensor:

1. The whole of your printer must be at room temperature. So do this after leaving the printer turned off for a long time, e.g. overnight.
1. Turn your printer on
1. As quickly as possible (before the Duet has time to heat up) and before energising any motors, connect to it using Duet Web Control and read the temperature from the **CPU Temp** box on the Machine Control page. Call this Tc.
1. To determine the current room temperature, read off the extruder heater and bed temperatures and take an average. If you have a PT100 sensor on the extruder, use just the extruder temperature because it is likely to be more accurate. Alternatively, use a thermometer to measure room temperature. Call this Tr.
1. Send command M912 P0 S# where # is (Tr - Tc). It may be negative. Assuming you did not already have a M912 command in config.g, after a few seconds the CPU temperature should then read about the same as room temperature, or a little higher because of heat generated by the microcontroller.
1. Add that M912 command to your config.g file. It doesn't matter where.

# MCU temp as proxy for driver temp

The MCU (AKA CPU) can generally be used as a good indicator of the driver temps. This has the benefit of giving a real time temperature reading to use for thermostatic control of a chassis fan.

# Tabs {.tabset}

## RepRapFirmware 3.x

```
M308 S2 Y"drivers" A"DRIVERS"  ; configure sensor 2 as temperature warning and overheat flags on the TMC2660 on Duet
M308 S3 Y"mcu-temp" A"MCU" ; configure sensor 3 as on-chip MCU temperature sensor
M950 F2 C"fan2" Q100 ; create fan 2 on pin fan2 and set its frequency                        
M106 P2 H2:3 L0.15 X1 B0.3 T40:70 ; set fan 2 value
```

Creates sensors for the driver overtemp flag and the MCU temp reading using M308 as S2 and S3. Creates a fan port for Fan2 with M950. Then creates a fan command to link those sensors to fan2 with a temp range of 40-70 using M106.

## RepRapFirmware 2.x

```
M106 P2 T45:65 H100:101:102
```

Sets up an electronics cooling fan that starts to turn on when the MCU temperature (virtual heater 100) reaches 45C and reaches full speed when the MCU temperature reaches 65C or if any TMC2660 drivers (virtual heaters 101 and 102) report that they are over-temperature.
