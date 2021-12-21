---
title: Connecting and configuring filament-out sensors
description: If your printer knows when it has run out of filament, it can abort the job, or it can pause while you load new filament.
published: true
date: 2021-11-22T13:08:45.024Z
tags: 
editor: markdown
dateCreated: 2021-10-26T13:10:27.693Z
---

# Introduction

If your printer knows when it has run out of filament, it can abort the job, or it can pause while you load new filament. To do this the printer needs to have a filament-out sensor attached.

The sensor may be a simple filament presence detector (eg a microswitch), or a device that measures movement of filament, or both. 

if you configure a filament monitor using M591, when it reports no filament RRF will try to run filament-error#.g where # is the extruder number, or failing that filament-error.g. If neither of those is found then it runs pause.g. After that the print is paused and a notification is sent advise you that there has been a filament error

There are two Duet3D products for precise motion detection with direction:

* [Duet3D Rotating Magnet Filament Monitor](/Duet3D_hardware/Accessories/Rotating_Magnet_Filament_Monitor)
* [Duet3D Laser Filament Monitor](/Duet3D_hardware/Accessories/Laser_Filament_Monitor)

# Connecting a filament-out sensor

Connect a filament sensor to an available IO header or endstop. 

**Duet 3 with CAN expansion boards:** Filament monitors on CAN expansion boards are supported from RRF 3.2. Filament monitors must be connected to the same board as the corresponding extruder motor. This is so that the firmware can correlate the measured filament movement and the commanded extruder movement in real time.

**Duet 2 WiFi/Ethernet:** If you have a DueX2 or DueX5 in your system, note that C5 thru C9 (the endstop inputs on the DueX) cannot be used for filament monitors, but C10 and C11 (the endstop inputs on the CONN_LCD connector) can.

# Configuring a filament-out sensor

RepRapFirmware supports  a variety of different filament presence and motion sensors using the [M591](/User_manual/Reference/Gcodes/M591) Gcode, where the 'Pnn' parameter is the type of sensor:

* 0 = none
* 1 = simple sensor (high signal when filament present)
* 2 = simple sensor (low signal when filament present)
* 3 = Duet3D rotating magnet sensor
* 4 = Duet3D rotating magnet sensor with microswitch
* 5 = Duet3D laser sensor
* 6 = Duet3D laser sensor with microswitch
* 7 = pulse-generating sensor

Map the filament sensor to the extruder drive number ('Dnn' parameter) using the 'Cnn' parameter. In RRF 3.x, this is the pin name the filament monitor is connected to. In RRF 2.x, this is the logical pin number. Example:

```
;RepRapFirmware 3.x, Duet 3
M591 D0 P3 C"io4.in" S1 ; rotating filament monitor connected to IO_4 for drive 0, enabled

;RepRapFirmware 3.x, Duet 2
M591 D0 P3 C"e0stop" S1 ; rotating filament monitor connected to E0 endstop for drive 0, enabled

;RepRapFirmware 2.x, Duet 2
M591 D0 P3 C S1 ; rotating filament monitor connected to E0 endstop for drive 0, enabled
```
To report the current settings, send:
```
M591 D0 ; display filament sensor parameters for extruder drive 0
```

**Note that filament monitoring in RRF is only active when printing from SD card.**

The action on a filament error is to:
1. Run filament-error#.g (RRF 3.2 and later, where # is the extruder number)
2. Failing that run filament-error.g (RRF 3.2 and later)
3. Failing that run pause.g (RRF 1.19 and later) to pause the print and advise you that there has been a filament error. 

