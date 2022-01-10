---
title: Duet3D Rotating Magnet Filament Monitor
description: 
published: true
date: 2022-01-10T07:01:52.061Z
tags: 
editor: markdown
dateCreated: 2021-08-18T13:55:54.917Z
---

![magnetic_fm_top.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_top.jpg =400x)

# Introduction

The Duet3D filament monitors sense filament movement and so can indicate a that filament has run out, jammed or has been ground away in the extruder. This is the magnetic filament monitor version, so called because it uses a rotating magnet to very accurately detect movement.

# How it works

The filament monitor consists of a PCB which senses the rotation of a diametrically magnetised magnet. That magnet is in turn rotated by the filament motion via a hobbed part. The angle is reported to the Duet (see section on Technical details for how this is done). This diagram shows the general principle.

![magnetic_fm_how_it_works.png](/hardware/magnetic_filament_monitor/magnetic_fm_how_it_works.png =600x)

# Hardware

## Components

### Tabs {.tabset}

#### v2.0 magnetic assembly and housing
![mfm_v2_parts_annotated.jpg](/hardware/magnetic_filament_monitor/mfm_v2_parts_annotated.jpg =600x)
The housing consists of:

* The main part that carries the hobbed assembly, idler, collets and constrains the filament path.
* A lid that mounts the PCB a set distance from the magnet.

The printed parts are not supplied; the housing models are [available on Github here](https://github.com/Duet3D/FilamentMonitors/tree/master/Filament_Monitor_Magnetic/enclosure_v2) and should be printed with a layer height of 0.2mm (including the first layer). This will ensure the correct distance between the magnet and the sensor PCB.

There are two versions of the body stl part, 1 with clearance M3 holes and one with holes sized for the M3 screws to tap into. Print the part that fits your mounting plan.


#### v1.0 magnetic assembly and housing
![magnetic_fm_hardware.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_hardware.jpg =600x)

The v1 housing can be 3d printed using an FDM printer using a fairly high resolution (e.g. 0.1 layer height); however some tweaking will  be required to get the magnet distance just right.

CAD models of the housing are [available on our GitHub](https://github.com/Duet3D/FilamentMonitors/tree/master/Filament_Monitor_Magnetic_v1.7/enclosure).

### Hobbed assembly

This consists of a custom machined hobbed part, a bearing and a 6mm diametrically magnetised magnet. Together with the idler this acts to closely couple the movement of the magnet to the movement of the filament.

The v2 hobbed assembly has a 2mm thick magnet (vs the 1mm thick magnet in the v1 design). 

### Idler assembly

This consists of a bearing carrier in two parts that can be 3d printed at 0.2mm layer height, an idler bearing and a spring.

### Collets and clips

These hold 4mm OD PTFE tubing in both sides of the filament monitor. The tubing ensures the filament runs along the correct path and should be fully inserted into the filament monitor on both sides and locked into place using the collet clips.

### PCB

The filament monitor PCB can be used to detect the movement of any appropriate diametrically magnetised magnet. It can be used in custom extruder designs, the diagram below shows the critical dimensions.

## Dimensions

![magnetic_fm_dimensions.png](/hardware/magnetic_filament_monitor/magnetic_fm_dimensions.png =400x)

## Assembly

## Tabs {.tabset}

### v2.0 magnetic assembly and housing

Before starting ensure the printed parts are free from stringing and that filament passes through the filament path. the filament path may need to be reamed out with a 1.8mm drill bit.

Install the idler carriers, idler bearing and spring.
![mfm_v2_step1_idler.jpg](/hardware/magnetic_filament_monitor/mfm_v2_step1_idler.jpg =400x)

Place the flanged bearing on the magnetic assembly and then fit in the body as shown. Ensure the bearing is seated properly 

![mfm_v2_step2_magnetic_assembly.jpg](/hardware/magnetic_filament_monitor/mfm_v2_step2_magnetic_assembly.jpg =400x)

Place the lid on top. It is usual for the magnet to cause the lid to lift slightly until the PCB is screwed into place. Screw the PCB in place as showing. note the two 12mm long M2.5 screws go to the left as show in this image, the single 6mm M2.5 screw goes to the right.

![mfm_v2_step3_lid&pcb1.jpg](/hardware/magnetic_filament_monitor/mfm_v2_step3_lid&pcb1.jpg =400x)
![mfm_v2_step3_lid&pcb2.jpg](/hardware/magnetic_filament_monitor/mfm_v2_step3_lid&pcb2.jpg =400x)

Insert the collets and collet clips.

![mfm_v2_step4_collets.jpg](/hardware/magnetic_filament_monitor/mfm_v2_step4_collets.jpg =400x)

If the MFM will be mounted directly above an extruder, supported only with the PTFE tube then use M3x 10 screws (not supplied) to complete the assembly. If the MFM will be screwed into a mounting plate then use appropriate length M3 screws for the chosen mounting method.

![mfm_v2_step4_m3.jpg](/hardware/magnetic_filament_monitor/mfm_v2_step4_m3.jpg =400x)


### v1.0 magnetic assembly and housing

Ensure the bearing in the main housing is seated properly and installer the idler carrier, idler bearing and spring.

![magnetic_fm_assembly_01.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_assembly_01.jpg =400x)

Place the hobbed assembly on the bearing and place the lid on top. It is usual for the magnet to cause the lid to lift slightly until the PCB is screwed into place.

![magnetic_fm_assembly_02.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_assembly_02.jpg =400x)

Screw the PCB in place as showing. note the two 12mm long M2.5 screws go to the left as show in this image, the single 6mm M2.5 screw goes to the right.

![magnetic_fm_assembly_03.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_assembly_03.jpg =400x)

Insert the collets and collet clips. Note that they are not secure in the housing until PTFE tube is inserted.

## Mounting

The monitor can be installed either before or after the extruder. It can mount directly onto the PTFE filament guide or bowden tube, held in place by the collets and collet clips. Alternatively there are two M3 holes in the housing to allow it to be screwed onto a flat surface.

*Note: The filament monitor needs to be close to the input or output of the extruder to get accurate results otherwise the flexing of the filament withing the feed/bowden tubes will reduce the accuracy of the readings*

## Tabs {.tabset}

### Before the Extruder

![magnetic_fm_mounting_01.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_mounting_01.jpg =400x)

It is important that the PTFE guide tube does not move during retraction and the filament path is constrained through the monitor so retractions are measured properly.

### After the extruder

On systems that use a bowden tube it is also possible to mount the filament monitor after the extruder. In this case filament out will not be detected until the end of the filament is in the extruder. The second two-pin header on the PCB is connected a separate filament sensing switch if you want to detect filament out before the extruder, but detect movement after the extruder.


# Wiring

## Tabs {.tabset}

### PCB v1.7a, 1.7b

![Wiring for the Rotating Magnet Filament monitor version 1.7a and v1.7b](/hardware/magnetic_filament_monitor/rm_fm_v1.7ab_wiring_d1.0.png =500x)

**Note:** on board revision v1.7a and later, the 'Switch' and 'Output' header is rotated 180 degrees, ie the connector tab is on the outside edge, compared with v1.7.

### PCB v1.7 

![magnetic_fm_v1.7_wiring.png](/hardware/magnetic_filament_monitor/magnetic_fm_v1.7_wiring.png =500x)

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

# Technical details

The filament monitor sends 16-bit words to the Duet. When the data is changing (normally because the filament is moving), one word is sent every 40ms. When the data isn't changing, one word is sent every 500ms.

Bit 15 of each word is 1 if the filament monitor is in an error state, 0 otherwise. Bit 14 is the state of the optional filament present switch: 1 if the input is high (meaning no filament is present) and 0 if it is low. However, the beta filament monitor firmware does not implement bits 14 and 15, they are always sent as 0.

Bits 0-9 represent the magnet angle measured in units of 1/1024 of a complete rotation.

The 16 bits are sent using a self-clocking protocol as follows. The nominal bit time is 1ms but the Duet firmware allows a reasonable tolerance because the filament monitor clock source is the internal R-C oscillator in the microcontroller, so its speed is somewhat variable.

* Idle state: the line must be be at 0 for at least 8 bit times
* Start bits: 1 followed by 0
* Data bits 15, 14, 13, 12
* Stuffing bit (inverse of bit 12)
* Data bits 11,10,9,8
* Stuffing bit (inverse of bit 8)
* Data bits 7,6,5,4
* Stuffing bit (inverse of bit 4)
* Data bits 3,2,1,0
* Stuffing bit (inverse of bit 0)
* After the last stuffing bit, the line returns to 0 until the next start bit.

# Flashing the firmware

The filament monitor will come with firmware flashed and tested. The only reason to flash firmware is to upgrade the firmware or install custom firmware.

The Firmware binaries and source code are [available on Github here](https://github.com/Duet3D/MagneticFilamentMonitor)

All version 1.7 and later monitors use Release 44a

An ISP programmer and pogo pin connections are needed to flash the firmware. There are many options available, e.g:

* [Sparkfun](https://www.sparkfun.com/products/9825)
* [Adafruit](https://learn.adafruit.com/usbtinyisp)

For the pogo pins:

* [Sparkfun](https://www.sparkfun.com/products/11591)
* [Digikey](https://www.digikey.co.uk/product-detail/en/dfrobot/DFR0359/1738-1256-ND/7087153)

The pads are the standard ISP pinout;

![magnetic_fm_v1.7a_firmware.png](/hardware/magnetic_filament_monitor/magnetic_fm_v1.7a_firmware.png =300x)

To write the firmware file to the filament monitor a program called [avrdude](http://download.savannah.gnu.org/releases/avrdude/) is used (there are other avr programming softwares available, for example Atmel Studio). Once installed the command line is used to flash the firmware:

`C:\avrdude\avrdude -p t44 -c usbtiny -U flash:w:"G:\Rotating Magnet\firmware\version_3\FilamentSensor.elf":e`

obviously change the paths to wherever you put AVR dude and the firmware binary.

The fuses should be set correctly on the monitor but if they are not the following command sets them correctly:

`C:\avrdude\avrdude -p t44 -c usbtiny -U lfuse:w:0xe2:m -U hfuse:w:0xdf:m -U efuse:w:0xff:m`

# Revisions

# Tabs {.tabset}

## PCB Revision 1.7b

A minor update with the following changes:

* minor changes to passive component sizes, no functional changes.

## PCB Revision 1.7a

A minor update with the following changes:

* SW header pinout is reversed to reduce the likely-hood of damage if the output connector is accidentally plugged into it.
* the OUT header is fitted the other way around (but the pinout is unchanged).

## PCB Revision 1.7

A redesign with the following changes:

* A custom hobbed part that includes a captive bearing and the magnet swaged on to form a secure a repeatable mounting. 
* PCB has been made smaller and adapted for easier mounting. 
* 2 part housing that can be printed using an an  SLS/resin printing, machined or injection moulded. It is also possible to print it using an FDM process but layer heights should be tuned to achieve the correct Magnet/sensor offset..
* Idler containing a spring and bearing

*Note: the firmware on revision 1.7 is reported by the Duet as version 2.0 for both the magnetic and laser filament monitors*

## Prototype Hardware

This is the original version, only produced in small numbers.

![magnetic_fm_prototype_how_it_works.png](/hardware/magnetic_filament_monitor/magnetic_fm_prototype_how_it_works.png =500x)

### Tabs {.tabset}

#### PCB Dimensions

The **beta** PCBs have the following dimensions, showing the key component locations:

![magnetic_fm_prototype_dimensions.png](/hardware/magnetic_filament_monitor/magnetic_fm_prototype_dimensions.png =400x)

#### 3d printable parts

**Springs**

The design uses 2 3D printable springs, so far good results have been achieved printing these in Ninjaflex but similar flexible filament should also work

**Enclosure**

The current enclosure design is split into two parts, one part holds the mechanical components that cause the moving filament to rotate the magnet. The second part holds the PCB in line with the magnet.

**Mount**

Different mounts will be required to mount the sensor to different extruders, effectors etc. An example for mounting to a big box Idex is shown in the documentation right now.

**Download**

The OpenSCAD source file and STLs are available on Youmagine: [https://www.youmagine.com/designs/duet3d-filament-monitor](https://www.youmagine.com/designs/duet3d-filament-monitor)

#### Hardware Assembly

The following instructions are for the standard hardware design used for the Beta test programme. Alternative housing designs and potentially ways of coupling filament movement to magnet rotation are possible. Please see the forum thread for further discussion.

1. Assemble the hobb, bearings and magnet:

| Component | Quantity | Notes |
|:---|:---|
| M3x16 with magnet fitted | 1 | Supplied assembled |
| 623ZZ bearing | 2 |  |
| Hobbed insert | 1 |  |
| M3 full net | 1 | Held in place with a drop of superglue/thread lock |

![magnetic_fm_prototype_assembly_01.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_prototype_assembly_01.jpg =250x)

Assemble as shown in the picture, the full nut is held in place with a drop of superglue or threadlock.

![magnetic_fm_prototype_assembly_02.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_prototype_assembly_02.jpg =250x)

2. Assemble the idler bearing and springs:

| Component | Quantity | Notes |
|:---|:---|
| M3x16 socket cap screw | 1 |  |
| 623ZZ bearing | 1 |  |
| 3d printed spring | 2 |  |

![magnetic_fm_prototype_assembly_03.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_prototype_assembly_03.jpg =250x)

Assemble as shown in the picture, the 3d printed rubber springs hold the idler bearing in place, no nut is required.

![magnetic_fm_prototype_assembly_04.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_prototype_assembly_04.jpg =250x)

3. Print the housing and mounting parts, gather the remaining parts:

| Component | Quantity | Notes |
|:---|:---|
| Idler Assembly | 1 | previously assembled |
| Hobb Assembly | 1 | previously assembled |
| Mechanical enclosure | 1 | 3d printed |

![magnetic_fm_prototype_assembly_05.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_prototype_assembly_05.jpg =400x)

4. Fit the idler spring and hobb assembly into the mechanical enclosure.

| Component | Quantity | Notes |
|:---|:---|
| Mechanical enclosure | 1 | previously assembled |
| PCB | 1 |  |
| PCB holder | 1 | 3d printed |
| mount | 1 | 3d printed |

The Idler assembly fits in the larger hole in the mechanical enclosure, this should be fitted first and the 3d printed rubber springs will be tight. The off center springs should be oriented the same way, holding the idler bearing towards the hobb.

![magnetic_fm_prototype_assembly_06.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_prototype_assembly_06.jpg =250x)

Check that the hobbed section and the idler pulley align with the filament hole:

![magnetic_fm_prototype_assembly_07.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_prototype_assembly_07.jpg =250x)

5. Complete the assembly with the PCB, PCB holder, mount, M3 SCS and nylock nuts. Note the magnet should sit approximately 0.5mm above the sensor, the PCB holder hole sizes match

![magnetic_fm_prototype_assembly_08.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_prototype_assembly_08.jpg =400x)

6. Mount as required.

Note different mount designs will be required to fit relative to different extruders. This is an example with it mounted on a BigBox Idex.

![magnetic_fm_prototype_assembly_09.jpg](/hardware/magnetic_filament_monitor/magnetic_fm_prototype_assembly_09.jpg =400x)
