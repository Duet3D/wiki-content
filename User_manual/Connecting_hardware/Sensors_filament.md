---
title: Connecting and configuring filament-out sensors
description: If your printer knows when it has run out of filament, it can abort the job, or it can pause while you load new filament.
published: true
date: 2022-01-14T15:13:45.072Z
tags: 
editor: markdown
dateCreated: 2021-10-26T13:10:27.693Z
---

# Introduction

If your printer knows when it has run out of filament, it can abort the job, or it can pause while you load new filament. To do this the printer needs to have a filament-out sensor attached.

The filament sensor may be a simple switch that detects the presence of filament, or a sensor that measures filament motion (e.g. laser, magnetic or pulsed filament monitor), or both.

There are two Duet3D products for precise motion detection with direction:

* [Duet3D Rotating Magnet Filament Monitor](/Duet3D_hardware/Accessories/Rotating_Magnet_Filament_Monitor)
* [Duet3D Laser Filament Monitor](/Duet3D_hardware/Accessories/Laser_Filament_Monitor)

# How it works

Configure a filament monitor using [M591](/User_manual/Reference/Gcodes/M591). The action on a filament error depends on the version of RepRapFirmware your Duet is running.

In **RRF 3.4 and later**, the action on a filament error is to:
* run filament-error#.g if available, where # is the extruder number
* failing that run filament-error.g if available
* failing that the Duet enters the Pausing state, shows a message on all available targets with the type of filament error, and invokes system macro pause.g. The job is paused and will need manual intervention to resume the print.
* Note if you use a filament-error macro, there is no pause unless you put an [M25](/User_manual/Reference/Gcodes/M25) in the macro. If the job is paused, it will require manual intervention, or [M24](/User_manual/Reference/Gcodes/M24) in the macro, to resume the print.

**RRF 3.2 and 3.3** is the same as RRF 3.4 and later, except the print is paused first. filament-error#.g or filament-error.g is run instead of pause.g if one of them exists. However, as the job is paused, you will need manual intervention to resume the print (M24 cannot be called from within any pause macro, which is what the filament-error macros are).

**RRF 1.19 to 3.1.1** does not support filament-error macros. The action on a filament error is to enter the Pausing state, show a message on all available targets with the type of filament error, and invoke system macro pause.g. The job is paused and will need manual intervention to resume the print.

For all firmware versions, **note** that filament monitoring in RRF is only active when printing from SD card.

# Connecting a filament sensor

## Connecting to the Duet

## Tabs {.tabset}

### Duet 3

**Important!** If you are using a Duet 3 or 3 Mini with tool or expansion boards, then **the filament monitor must be connected to the same board as the motor** for the extruder that it is monitoring. Filament monitors connected to tool and expansion boards are supported in RepRapFirmware 3.2beta4 and later.

Connect the filament monitor to the +3.3V, GND and IN pins of one of the IO_ connectors on the Duet 3 main board or on a tool or expansion board.

| Duet 3 | Filament monitor |
|:---|:---|
| 3.3V | 3.3V |
| io[x].in | OUT |
| GND | GND |
| io[x].out | - |
| 5V_EXT | - |

### Duet 2

Connect the filament monitor to an endstop connection on the Duet 2 mainboard (typically E0 or E1, but you can also use any of X, Y or Z that are free).

**Note:** The Filament monitor must be connected to an endstop on the Duet 2 Wifi, Duet 2 Ethernet or Duet 2 Maestro. It does not work connected to an endstop on a Duex expansion board.

| Duet 2 | Filament monitor |
|:---|:---|
| E0_STOP | OUT |
| 3.3V | 3.3V |
| GND | GND |

* For filament monitor version 1.7a and later use a cross over  3 wire cable with a molex KK on either end. (i.e. pin 1 to pin 3, pin 2 to pin 2, pin 3 to pin 1).
* For filament monitor version 1.7 use a straight through over  3 wire cable with a molex KK on either end. (i.e. pin 1 to pin 1, pin 2 to pin 2, pin 3 to pin 3).

## Optional filament presence switch

You can connect a micro-switch to the 2-pin "SW" header arranged so that the switch contacts are closed when filament is present and open when it is not.

## 5V Operation

It is possible to modify the Filament monitor PCB to connect to a controller that uses 5V logic. To do this use a small drill bit to carefully remove the connection between the two pads that is made with the ring of the throuhg hole between the pads. **Test that the pads are actually disconnected by checking that there is no continuity between them with a voltmeter.**

![Diagram showing the locaiton of the 3.3V/5V drillable jumper on the rotating magnet PCB version 1.7b](/hardware/magnetic_filament_monitor/rm_fm_v1.7ab_5v_d1.0.png =600x)

This image shows PCB v1.7b, the process is the same for 1.7 and 1.7a



## Connecting to the Duet

## Tabs {.tabset}

### Duet 3

**Important!** If you are using a Duet 3 or 3 Mini with tool or expansion boards, then **the filament monitor must be connected to the same board as the motor** for the extruder that it is monitoring. Filament monitors connected to tool and expansion boards are supported in RepRapFirmware 3.2beta4 and later.

Connect the filament monitor to the +3.3V, GND and IN pins of one of the IO connectors on the Duet 3 main board or on a tool or expansion board.

| Duet 3 | Filament monitor |
|:---|:---|
| 3.3V | 3.3V |
| io[x].in | OUT |
| GND | GND |
| io[x].out | - |
| 5V_EXT | - |

### Duet 2

Connect the filament monitor to an **endstop** connection on the Duet 2 **main board**, using a 3 wire straight-through cable with a Molex KK cable socket on either end. The cable does not need to be shielded.

**Note:** The Filament monitor must be connected to an endstop (X, Y, Z, E0, E1) on the main Duet board, or to Stop 10 (connlcd.encb) or Stop 11 (connlcd.enca) on the CONN_LCD connector. It does not work connected to an endstop on a DueX expansion board.

| Duet 2 | Filament monitor |
|:---|:---|
| E0_STOP | OUT |
| 3.3V | 3.3V |
| GND | GND |

## Optional filament presence switch

You may connect a micro-switch to the 2-pin "SW" header arranged so that the switch contacts are closed when filament is present and open when it is not. However, this is not normally necessary, because the filament monitor will detect that there is no filament moving through just a few mm after the end of the filament has passed over the sensor, which will normally be well before the end of the filament reaches the extruder drive.

*Note this switch header is not populated in  all versions of the monitor but it can be added if desired. It is a standard  a standard Molex KK 2 pin header*




# Rotating magnet filament sensor


# Firmware configuration

## Firmware requirements

**Note**: RRF v2.03 or later is required to support the Magnetic Filament Monitor, version 2.04RC4 or later is recommended.

## Tabs {.tabset}

### RepRapFirmware 3.x


The filament monitor is configured using the [M591](/User_manual/Reference/Gcodes/M591) command. Here are some examples of that command:

```
M591 D0 P3 C"e0_stop" S1  ; filament monitor connected to E0_stop

M591 D0 ; display filament sensor parameters for extruder drive 0
```

Brief explanation of parameters:

* Dnn Extruder drive number (0, 1, 2...),
* Pnn Type of sensor: 0=none, 1=simple sensor (high signal when filament present), 2=simple sensor (low signal when filament present), 3=Duet3D rotating magnet sensor, 4=Duet3D rotating magnet sensor with microswitch, 5 = Duet3D laser sensor, 6 = Duet3D laser sensor with microswitch, 7 = pulse-generating sensor
* C"nn" - Where "nn" is the pin name the filament sensor is connected to (RRF 3 only)
* Sn 0 = disable filament monitoring (default), 1 = enable filament monitoring when printing from SD card. Filament monitors accumulate calibration data (where applicable) even when filament monitoring is disabled.
* Raa:bb Allow the filament movement reported by the sensor to be between aa% and bb% of the commanded values; if it is outside these values and filament monitoring is enabled, the print will be paused
* Enn minimum extrusion length before a commanded/measured comparison is done, default 3mm
* An (firmware 2.03 and later) 1 = check All extruder motion, 0 = only check extruder motion of printing moves (moves with both movement and forward extrusion)
* Lnn Filament movement per complete rotation of the sense wheel, in mm

RRF will provide defaults for the R, E and L parameters. Use the output of the calibration to tweak these.


### RepRapFirmware 2.x

The filament monitor is configured using the [M591](/User_manual/Reference/Gcodes/M591) command. Here are some examples of that command:

```
M591 D0 P3 C3 S1 R70:130 L24.8 E3.0 ; Duet3D rotating magnet sensor for extruder drive 0 is connected to E0 endstop input, enabled, sensitivity 24.8mm.rev, 70% to 130% tolerance, 3mm detection length

M591 D0 ; display filament sensor parameters for extruder drive 0
```

Brief explanation of parameters:

* Dnn Extruder drive number (0, 1, 2...),
* Pnn Type of sensor: 0=none, 1=simple sensor (high signal when filament present), 2=simple sensor (low signal when filament present), 3=Duet3D rotating magnet sensor, 4=Duet3D rotating magnet sensor with microswitch, 5 = Duet3D laser sensor, 6 = Duet3D laser sensor with microswitch, 7 = pulse-generating sensor
* Cnn Which input the filament sensor is connected to. On Duet electronics: 0=X endstop input, 1=Y endstop input, 2=Z endstop input, 3=E0 endstop input etc. If you have a Duex 2 or Duex 5 in your system, note that C5 thru C9 (the endstop inputs on the DueX) cannot be used for filament monitors, but C10 and C11 (the endstop inputs on the CONN_LCD connector) can.
* Sn 0 = disable filament monitoring (default), 1 = enable filament monitoring when printing from SD card. Filament monitors accumulate calibration data (where applicable) even when filament monitoring is disabled.
* Raa:bb Allow the filament movement reported by the sensor to be between aa% and bb% of the commanded values; if it is outside these values and filament monitoring is enabled, the print will be paused
* Enn minimum extrusion length before a commanded/measured comparison is done, default 3mm
* An (firmware 2.03 and later) 1 = check All extruder motion, 0 = only check extruder motion of printing moves (moves with both movement and forward extrusion)
* Lnn Filament movement per complete rotation of the sense wheel, in mm

Initially you will not know the exact "L" parameter to use so start with 24.8 and use the output of the calibration to tweak this.

# Commissioning

## Power on test

Once power is turned on, the filament monitor will flash the green LED 3 times to indicate the magnet is detected. The green LED will then flash periodically indicating communication to the Duet. If the magnet is rotating the LED will flash more frequently. For other LED flashing codes see the Troubleshooting section below

## Calibration

1. If you haven't done so already, send the M591 command with the correct parameters to tell the firmware about the sensor.
1. Run M591 D# where # is the extruder number and check that the sensor angle is reported, to confirm that communication from the filament monitor to the Duet is working.
1. Start a print.
1. During and after the print, as soon as sufficient filament has been extruded you can use M591 D# (where # is the extruder number) to report the measured mm/rev averaged over the print thus far, and its variation.
1. If you pause and then resume the print, calibration will be re-started and the values accumulated from before you paused will be discarded.
1. The mm/rev value goes into the L parameter of the M591 command. Use a positive or negative sign as reported by M591. Set the R (tolerance) parameter to somewhat more than the reported variation.

## How it works

* While a file is being printed the Duet Firmware reads the angle from the Filament monitor.
* The Duet firmware uses the parameters configured in M591 to determine the angle change that relates to a specific filament movement distance.
* Once net extruder movement greater than the number of mm configured in the E parameter (for example 3mm) has been commanded, the extrusion measured by the filament monitor is compared with the amount of extrusion commanded.
* If there is a difference between what has been commanded and what has been measured by the filament monitor that is greater than the configured tolerance (configured using the R parameter) the print is paused and an error is reported.

# Troubleshooting

## Status indication LEDs

In normal operation, during initial power up, the filament monitor will flash the green LED 3 times to indicate the magnet is detected. The green LED will then flash periodically red and green indicating communication to the Duet (the green flashes are position reports, the red ones are status reports). If the magnet is rotating the green LED will flash more frequently (can look like almost solid green)

If the filament monitor fails to initialise after power up, then instead of flashing the green LED 3 times it will flash the red LED to indicate an error. It will continue to try to initialise and flash the red LED until successful initialisation.

The number of flashes of the red LED indicates the nature of the error, as follows:

* 4 flashes: I2C communications error
* 5 flashes: I2C channel is in an incorrect state
* 6 flashes: Magnet not detected.
* 7 flashes: Magnet too weak
* 8 flashes: Magnet too strong

Typically errors 6 & 7 means that the magnet is too far away from the sensor chip. See the description of "agc" below on how to solve this.

## Errors during operation

The rotating magnetic sensor should be mounted as close as possible to the extruder, to minimise filament hysteresis between them. If a large distance between the sensor and extruder is unavoidable, you may need to increase the M591 'E' parameter (minimum extrusion length before a commanded/measured comparison is done) to something much larger than the default 3mm, perhaps 10 to 20 mm, to account for filament hysteresis.

If you get sudden drops in the sensitivity, check that sensor is solidly mounted, and that any PTFE tubes between the sensor and extruder are firmly held, with no movement.

To make sure the sensor is measuring correctly, check the 'mag' and 'agc' values in the M591 report for the magnetic sensor. 'agc' is automatic gain control, used to bring the signal magnitude from the Hall sensor in range. The maximum value available is 128. 'mag' is the magnitude of the signal from the Hall sensor element, after applying agc. Under normal conditions, 'agc' should be in the range 50 to 105 and 'mag' won't change much. So 'mag' is less useful than 'agc'.

For example, sending M591 D0 (assuming magnetic sensor is connected to your first extruder) might report:

```
M591 D0
Duet3D magnetic filament monitor on input 3, enabled, sensitivity 24.80mm/rev, allow 70% to 130%, check every 3.0mm, version 3, mag 126 agc 128, measured sensitivity 24.78mm/rev, min 97% max 102% over 109.0mm
```

If your sensor does not report mag and agc, check that you are running RepRapFirmware 2.05.1 or RepRapFirmware 3 on the Duet.

If the sensor reports error 6 or 7 that has the same meaning as the red flashes documented above.

Values for 'agc' of 50 to 105 are considered normal. If the value is higher, up to 128, the distance between the magnet and the sensor is likely to be too large (target is 0.25mm). Check that the screws holding the PCB down are not loose (caution: they are screwed into the plastic, so don't over-tighten them). If they are already tight, you could remove the 2 long screws to separate the two plastic parts and very slightly sand down the bottom surface of the top one (the one with the PCB attached) to move the sensor closer to the magnet.

If the above doesn’t help, and you are still getting spurious readings, please contact Duet3D support by [posting on the forum](https://forum.duet3d.com/).


# Laser filament sensor


# Firmware configuration

## Recommended RepRapFirmware versions

For **Revision 2.0** we recommend you use **RepRapFirmware 2.03 or later** in order to get the correct readings.

For **Revision 1.7** you must be using **RepRapFirmware 1.21 or later**.

## Common configuration to both revisions

## Tabs {.tabset}

### RepRapFirmware 3.x

The filament monitor is configured using the M591 command, see [M591](/User_manual/Reference/Gcodes/M591). Here is an example of that command:

`M591 D0 P5 C"e0_stop" R40:120 E3.0 S0 ; Duet3D laser sensor for extruder drive 0, connected to endstop input 3 (E0), tolerance 40 to 120%, 3mm comparison length, disabled`

You can also use M591 from the console to retrieve the current data:

`M591 D0 ; display filament sensor parameters for extruder drive 0`

Brief explanation of parameters:

* D =  extruder drive number
* P = filament sensor type. P5 = Duet3D laser filament monitor **without** additional filament presence detection switch, P6 = Duet3D laser filament monitor with switch.
* E = minimum extrusion length before a commanded/measured comparison is done, default 3mm
* C = Pin name of the pin the filament sensor is connected to, eg 'io[x].in' (Duet 3) or 'e0stop' (Duet 2)
* Raa:bb = minimum and maximum permitted measured movement as a percentage of commanded movement
* S = 0 to disable comparison of commanded vs. measured output, 1 to enable comparison and pause the print if a discrepancy is detected
* An (firmware 2.03 and later) 1 = check All extruder motion, 0 (default) = only check extruder motion of printing moves (moves with both movement and forward extrusion)

Initially you will not know some of the correct parameters for the M591 command, so put in the correct D, C and P parameters and set S to 0.

### RepRapFirmware 2.x

The filament monitor is configured using the M591 command, see [M591](/User_manual/Reference/Gcodes/M591). Here is an example of that command:

`M591 D0 P5 C3 R40:120 E3.0 S0 ; Duet3D laser sensor for extruder drive 0, connected to endstop input 3 (E0), tolerance 40 to 120%, 3mm comparison length, disabled`

You can also use M591 from the console to retrieve the current data:

`M591 D0 ; display filament sensor parameters for extruder drive 0`

Brief explanation of parameters:

* D =  extruder drive number
* P = filament sensor type. P5 = Duet3D laser filament monitor **without** additional filament presence detection switch, P6 = Duet3D laser filament monitor with switch.
* E = minimum extrusion length before a commanded/measured comparison is done, default 3mm
* C = endstop number (0 = X, 1 = Y, 2 = Z, 3 = E0 etc.). Endstop inputs 5-9 which are on the Duex 2 or Duex 5 expansion boards cannot be used. Endstop inputs 10 and 11 on the CONN_LCD connector can be used. 
* Raa:bb = minimum and maximum permitted measured movement as a percentage of commanded movement
* S = 0 to disable comparison of commanded vs. measured output, 1 to enable comparison and pause the print if a discrepancy is detected
* An (firmware 2.03 and later) 1 = check All extruder motion, 0 (default) = only check extruder motion of printing moves (moves with both movement and forward extrusion)

Initially you will not know some of the correct parameters for the M591 command, so put in the correct D, C and P parameters and set S to 0.

# Commissioning

## Self test

On startup, after a few seconds the green LED on the filament monitor will flash 3 times if initialisation and self-test are successful.  The green and red LEDs will then flash periodically indicating communication to the Duet. If the filament is moving, the green LED will flash more frequently.

If initialisation or self test fail, the red LED will flash an error code (currently this is always 5 flashes). It will retry every few seconds.

## Calibration

1. Make sure you have calibrated your extruder steps/mm correctly
1. If you haven't done so already, send the M591 command with the correct D, P and C parameters and S=0 to tell the firmware about the sensor.
1. Run M591 D# where # is the extruder number and check that the sensor angle is reported, to confirm that communication from the filament monitor to the Duet is working.
1. Start a print.
1. During and after the print, as soon as sufficient filament has been extruded you can use M591 D# (where # is the extruder number) to report the minimum and maximum measured movement as a percentage of commanded movement.
1. If you pause and then resume the print, on resuming the calibration will be re-started and the values accumulated from before you paused will be discarded.
1. Set the R (tolerance) parameter of the M591 command to give a somewhat wider tolerance than the calibration reports. For example, if calibration reports 43% to 115% then you might choose R30:130.
1. It is worth testing with a wide variety of filaments that you typically use before setting the minimum and maximum trigger values as the laser sensor is sensitive to filament material. Alternatively you can use a different bracket of minimum and maximum for different filament types by setting the M591 setting within the print start gcode, through a macro or through the [filament management system](/User_manual/Reference/DWC_filaments).

# Troubleshooting

## LED diagnosis

If the filament monitor fails to initialise after power up, then instead of flashing the green LED 3 times it will flash the red LED five times to indicate an error. It will continue to try to initialise and flash the red LED until successful initialisation.

## Reported Information

Three pieces of information are reported by the filament monitor that describe how well the sensor chip is reading the moving surface of the filament (or other surface for an indirect measurement).

* **quality** - Reports the number of features detected by the PAT9130 sensor. The higher the quality reported the better the surface is tracked. (theoretical range 0-255)
* **brightness** - The average brightness of all pixels in the PAT9130 sensor. Brightness and shutter are related (the sensor alters the shutter setting to keep the brightness within an acceptable range). (theoretical range 0-255)
* **shutter** - It is controlled by the PAT9130 sensor auto-exposure algorithm. lower numbers for shutter indicate a "better" surface. (theoretical range 0-255)


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

