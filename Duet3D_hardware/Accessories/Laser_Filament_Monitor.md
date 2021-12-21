---
title: Duet3D Laser Filament Monitor
description: 
published: true
date: 2021-12-13T13:25:47.264Z
tags: 
editor: markdown
dateCreated: 2021-08-18T10:48:43.726Z
---

![laser_fm_top.jpg](/hardware/laser_filament_monitor/laser_fm_top.jpg =x400)

# Introduction

The Duet3D filament monitors sense filament movement and so can indicate that filament has run out, jammed or has been ground away in the extruder. This version uses a laser sensor to detect movement. There is also a version using  a rotating magnet.

# How it works

* Filament moving through the filament monitor is detected by the laser optical sensor. The microcontroller in the filament monitor reads the amount and direction of motion and periodically sends the accumulated amount of motion to the Duet.
* When net extruder movement greater than the number of mm configured in the E parameter (for example 3mm) has been commanded, the extrusion measured by the filament monitor is compared with the amount of extrusion commanded.
* If the difference between what has been commanded and what has been measured by the filament monitor is greater than the configured tolerance (configured using the R parameter), the print is paused and an error is reported.

## Note on filament compatibility

Different filament materials and colours are detected with varying degrees of accuracy by the laser sensor. For most filament types this can be handled through setting an appropriate trigger value for the sensor. The sensor is accurate enough in most tested filaments to allow the print to be paused for filament run-out, jam or tangle, those filament types the sensor is less sensitive to may have 1-2mm of error in movement detection. Depending on where in the print the extruder jam or filament strip occurs will determine how much of an impact 1-2mm of unfed filament will have on the print. The sensor is proving to be inaccurate with certain PETG filament, especially from ESUN (Black tested so far) and is not recommended for this.

# Hardware

The filament monitor consists of a PCB which carries a laser optical sensor, a 2-part printed housing, and short PTFE inlet and outlet tubes to guide the filament.

Both revisions have the same Laser sensor position, PCB dimensions and mounting holes. Some of the components are in different locations which can affect the housing which is why there are two different housing desings.

## Tabs {.tabset}

### Revision 2.0

#### Dimensions

![laser_fm_v2.0_dimensions.png](/hardware/laser_filament_monitor/laser_fm_v2.0_dimensions.png =400x)

#### 3D printable parts

![laser_fm_hardware_01.png](/hardware/laser_filament_monitor/laser_fm_hardware_01.png =400x)

The Laser Filament Monitor uses two printed parts to constrain the filament so that it passes over the centre of the the optical sensor at the correct height. Inlet and outlet PTFE tubes are clamped between these two parts. They must be printed in **black filament**.

OpenSCAD source and STLs from DC42's reference housing design [can be found here on GitHub](https://github.com/T3P3/FilamentMonitors/tree/master/Filament_Monitor_Laser_v2.0).

### Revision 1.7

#### Dimensions

![laser_fm_v1.7_dimensions.png](/hardware/laser_filament_monitor/laser_fm_v1.7_dimensions.png =400x)

#### 3D printable parts

The Laser Filament Monitor uses two printed parts to constrain the filament so that it passes over the centre of the the optical sensor at the correct height. Inlet and outlet PTFE tubes are clamped between these two parts. They must be printed in **black filament**.

The OpenSCAD source file and STL for these parts is available [on Thingiverse](https://www.thingiverse.com/thing:2746340).

**Base plate** / **Filament Block**

This part fits on top of the PCB.

**Top plate** / **Sensor Block**

This fits on top of the base plate, The PTFE inlet and outlet tubes are clamped between the two plates.

![laser_fm_hardware_02.jpg](/hardware/laser_filament_monitor/laser_fm_hardware_02.jpg =400x)

## Open Source

The hardware source files are [on Github here](https://github.com/Duet3D/FilamentMonitors).

## Other parts

| Component | Quantity | Notes |
|:---|:---|
| M3x25 screw | 2 | The screw length depends on the housing design, also longer screws if you wish to secure the filament monitor to something else |
| M3 nut | 2 | Use Nyloc nuts or thread lock for security |
| M3 nylon washers | 2 | Optional |
| 4mm OD x 1.8mm ID PTFE tubing | 2 pieces | Preferably Capricorn or similar quality. One length is for the filament inlet and need only be a few cm long, but can be longer if you need to guide the filament from the spool. The other is for the filament outlet and must be long enough to reach the extruder drive, but short enough for the filament monitor to sense retraction accurately. |

## Temperature Limits

Max recommended operating temperature specified by the manufacturer of the laser sensor device is 60C. All other electronic parts are rated to 85C or more.

## Assembly

1. Remove any stringing in the rectangular aperture in the top plate.
1. Put the two screws through the top of the top plate. Turn the top plate with screws in it upside down so that the grooves for the PTFE tube are uppermost. Lay the base plate on top, with the grooves on the underside and the PCB cutout on top. Place the PCB over the screws, with the connectors uppermost, so that the PCB fits in the cutout for it. Add the optional washers and nuts and secure them loosely.
1. Insert the two pieces of PTFE tubing  into the two holes formed by the grooves in the two plates. **They must not protrude at all into the rectangular opening**. Tighten the nuts to secure them in place.

![laser_fm_mounting.png](/hardware/laser_filament_monitor/laser_fm_mounting.png =x400) ![laser_fm_mounting_01.jpg](/hardware/laser_filament_monitor/laser_fm_mounting_01.jpg =x400) 


## Mounting

The Laser Filament Monitor should be mounted in such a way as to avoid dust settling on the optical sensor, which is on the component side of the PCB (the opposite side to the connectors). Do not mount it with the optical sensor facing up and connectors facing down. If you mount it with the filament path horizontal, mount it with the connectors uppermost if possible. If you mount it with the filament path vertical, orient it with the connectors at the bottom so that closed end of the housing is at the top.

**Caution! Check that the printed part that the sensor PCB will sit in has no blobs or other blemishes that would cause a bending force to be applied to the PCB when the screws are tightened.**

It does not matter which end of the filament monitor you use as the filament inlet and which end as the outlet, direction is set as part of configuration and tuning of the monitor.

The distance between the filament monitor outlet and the  extruder drive inlet should be kept short and the filament must be constrained by PTFE tubing over this distance, so that when the filament reverses its movement direction due to retraction, the retracted filament is forced to reverse through the filament monitor.

If you need to place anything close to or on top of the rectangular aperture in the top plate of the filament monitor, it must be matt black. One option is to replace the top plate (see the Hardware section) by a deeper version that is closed at the top.

# Wiring

![laser_fm_v1.7_wiring.png](/hardware/laser_filament_monitor/laser_fm_v1.7_wiring.png =600x)

## Warnings

* The optical sensor on the filament monitor PCB is delicate! Take great care not to damage it or to touch it. Do not put the filament monitor PCB component-side down on any surface. Damage to the optical sensor is not covered by the warranty.
* The Duet3D Laser Filament Monitor is designed to be powered with 3.3v only. If you supply 5V power to it, you will destroy the optical sensor.

![laser_fm_class_1_warning.jpg](/hardware/laser_filament_monitor/laser_fm_class_1_warning.jpg =300x)

* The sensor is a Class 1 Laser device. Under normal conditions, the laser beam is always below the IEC/EN 60825-1:2014 maximum permitted output level of 716uW according to the manufacturer of the optical sensor; nevertheless we advise against looking into the beam when the filament monitor is powered. **You should most definitely not look at the sensor through magnification when it is operating.** The laser beam is invisible infra red, therefore your eye blink reflex will not protect you!

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

# Housing variations

The laser sensor shows different performance based on the type of filament being used, and potentially some other variables such as the material the housing is printed from and ambient infrared light.  See [this forum thread](https://forum.duet3d.com/topic/6092/laser-filament-monitor-test-results) for examples from testing.

Other than determining which filaments work well and which ones work less well, areas for experimentation to determine what improvements can be made relate to housing design. Some of these options have been added to an extended version of the housing design referenced above, [available here](https://www.youmagine.com/designs/duet3d-laser-filament-monitor-housing-v2).

# Tabs {.tabset}

## Filament Block

![laser_fm_filament_block_01.png](/hardware/laser_filament_monitor/laser_fm_filament_block_01.png =400x)

This part holds the filament guide PTFE tubing against the matching part on the sensor.  Potential improvements:

* The laser light that does not reflect off the filament reflects off parts of both blocks so it might work better if printed with something that absorbs IR radiation of ~850nm 
* Having a divergent light path to reduce stray reflections back to the sensor might help, See the "divergeAmount" variable in the OpenSCAD desing on youmagine:

![laser_fm_filament_block_02.png](/hardware/laser_filament_monitor/laser_fm_filament_block_02.png =400x)

*This image shows the divergence exaggerated to show the effect*

* Closing the back of the filament block could prevent ambient IR from interfering with the sensor, alternatively it could increase unwanted reflections. Use the "closedTop" option in the OpenSCAD file to change this. A conical or pyramid-shaped closure might work even better, by containing light from the laser within the closure instead of reflecting it towards the sensor.

![laser_fm_filament_block_03.png](/hardware/laser_filament_monitor/laser_fm_filament_block_03.png =400x)

## Sensor Block

![laser_fm_sensor_block_01.png](/hardware/laser_filament_monitor/laser_fm_sensor_block_01.png =400x)

This part holds the filament path guides, along with the filament block. it also holds the sensor PCB. Potential areas for improvement:

* The laser light that does not reflect off the filament reflects off parts of both blocks so it might work better if printed with something that absorbs IR.
* Having a divergent light path to reduce stray reflections back to the sensor might help, See the "divergeAmount" variable in the OpenSCAD design on youmagine. The flight path starts in the sensor block and the ends in the filament block so this variable affects both piece.
* Changing the distance between the filament and the sensor changes the error level in the reading. Preliminary resting showed that ~9mm worked well for quite a few filaments. It may be that different filaments work better with different distances. To change this use the "filamentHeight" variable in the OpenSCAD file:

![laser_fm_sensor_block_02png](/hardware/laser_filament_monitor/laser_fm_sensor_block_02.png =600x)

## Adapting for 2.85mm filament

For 2.85mm filament, we suggest you use 3mm ID PTFE tubing (**not** 1/8") and adjust the groove sizes in the base and top plates to accommodate the larger tubing. Also increase the thickness of the base plate by 0.6mm to keep the distance between the optical sensor and the outside of the filament the same. 

## Indirect reading

Some people have had success using the laser filament monitor to read a bearing or steel ball which the filament is turning. [See this form thread](https://forum.duet3d.com/topic/10042/indirect-bearing-laser-filament-monitor-concept/) for an example.

# Technical details

## Communications Protocol

The filament monitor sends 16-bit words to the Duet. When the filament position is changing, a filament position word is sent every 40ms. When the data isn't changing, alternating filament position words and status words are sent every 500ms.

The 16 bits are sent using a self-clocking protocol as follows. The nominal bit time is 1ms but the Duet firmware allows a reasonable tolerance because the filament monitor clock source is the internal R-C oscillator in the microcontroller, so its speed is somewhat variable.

```
Idle state: the line must be be at 0 for at least 8 bit times
Start bits: 1 followed by 0
Data bits 15, 14, 13, 12
Stuffing bit (inverse of bit 12)
Data bits 11,10,9,8
Stuffing bit (inverse of bit 8)
Data bits 7,6,5,4
Stuffing bit (inverse of bit 4)
Data bits 3,2,1,0
Stuffing bit (inverse of bit 0)
After the last stuffing bit, the line returns to 0 until the next start bit.
```

The meanings of the 16-bit words is as follows. P is the parity bit, adjusted so that the total number of set bits in the word is even,

| Word type | Format | Notes |
|:---|:---|
| v1 Data word | P00S 00pp pppp pppp | S = switch open, pppppppppp = 10-bit filament position (50 counts/mm) |
| v2 Data word | P00S 1ppp pppp pppp | S = switch open, ppppppppppp = 11-bit filament position (100 counts/mm) |
| v1 Error word | P010 0000 0000 0000 |  |
| v2 Error word | P010 0000 0000 eeee | eeee = error code, will not be zero |
| v1 Quality word | P10s ssss bbbb bbbb | sssss = shutter, bbbbbbbb = brightness |
| v2 Version word | P110 0000 vvvv vvvv | vvvvvvvv = sensor/firmware version, at least 2 |
| v2 Image quality wor | P110 0001 qqqq qqqq | qqqqqqqq = image quality |
| v2 Brightness word | P110 0010 bbbb bbbb | bbbbbbbb = brightness |
| v2 Shutter word | P110 0011 ssss ssss | ssssssss = shutter |

# Revision history

# Tabs {.tabset}

## Revision 2.0

This uses the PAT 9130 laser sensor and has some minor firmware tweaks to improve the communication protocol and distinguish between the revisions.

The housing is slightly different for this board, although they are the same size. See the housing section for links to the new housing.

Firmware 2.03 or later is required to use the version 2.0 sensor and report the correct values.

## Revision 1.7

This uses the PAT 9125 laser sensor.
