---
title: Connecting an accelerometer
description: This is a description of the experimental accelerometer support in RRF 3.3 and later.
published: true
date: 2023-05-23T11:30:22.389Z
tags: 
editor: markdown
dateCreated: 2021-11-15T14:50:57.165Z
---

![accelerometer_03.jpg](/manual/sensors/accelerometer_03.jpg =800x)
# Introduction

RepRapFirmware 3.3 and later include support for connecting accelerometers. The primary purpose is to identify the ringing frequencies of the mechanics so that DAA and in future other forms of input shaping can be used to reducing ringing.

# Supported hardware

## Duet mainboards

Duet 3 mainboards, Duet 2 WiFi/Ethernet and Duet 2 Maestro support a directly-connected accelerometer.

Duet 3 mainboards also support CAN-bus connected boards with either built-in or wired accelerometers. Currently this is [Duet 3 Toolboard 1LC](/Duet3D_hardware/Duet_3_family/Duet_3_Toolboard_1LC) v1.1 and later (LIS3DH accelerometer built in) and [Sammy-C21](/Duet3D_hardware/Duet_3_family/Using_the_Sammy-C21_development_board_with_Duet_3) with a wired accelerometer.

## Supported accelerometers

RRF 3.3 and 3.4beta1 support one type of accelerometer, the LIS3DH. We chose this chip because of its low cost and because it provides a mechanism to read all the data stored in its FIFO in a single block command.

RRF 3.4beta2 and later support the LIS3DH and the LIS3DSH (note the extra S). Connection via SPI and configuration are exactly the same as for the LIS3DH. Compared to the LIS3DH, the LIS3DSH has higher resolution, more convenient sampling rates, and produces a cleaner signal.

Breakout boards using the LIS3DH or LIS3DSH are readily available from eBay, Amazon and other retailers such as [SparkFun](https://www.sparkfun.com/){target=_blank}, [Adafruit](https://www.adafruit.com/){target=_blank}, [Digikey](https://www.digikey.com/) [The Pi Hut (UK)](https://thepihut.com/){target=_blank}, [HobbyTronics (UK)](https://www.hobbytronics.co.uk/){target=_blank} and [Pimoroni (UK)](https://shop.pimoroni.com/){target=_blank}. The Adafruit and SparkFun boards are also available from Digikey. There are two different versions of the Adafruit LIS3DH board; either can be used.

Accelerometer boards using the LIS3DSH are readily available via eBay, Amazon and other retailers.

# Limitations

* In RRF 3.3, data collection from accelerometers is currently only available when running the Duet in standalone mode. In RRF 3.4beta2 and later, data can be collected when running in SBC mode too.
* Use of an accelerometer as a Z probe is not currently supported.

# Wiring
  
## Tabs {.tabset}

### Direct SPI connection to a Duet mainboard

To use a direct connection, connect the accelerometer SDA (also called MOSI), SDO (also called MISO), SCL and GND pins to the corresponding pins on the SPI/temperature daughterboard connector. You can also pick up +3.3V on the daughterboard connector to feed to the VCC pin of the accelerometer.

Two further signals must be connected on the accelerometer breakout board: CS and INT1. The INT1 pin must be connected to a Duet pin with interrupt capability. **The CS pin should be separated from other signal wires if possible because it is sensitive to interference, especially when using a Duet 3 Mini or Duet 3 MB6HC** (see note below).

![accelerometer_01.jpg](/manual/sensors/accelerometer_01.jpg =x400){target=_blank} ![accelerometer_02.jpg](/manual/sensors/accelerometer_02.jpg =x400){target=_blank}

The photo on the left shows a sample cable that connects the Adafruit LIS3DH board to the temperature daughterboard connector on a Duet 2 WiFi, Ethernet, Maestro or Duet 3 MB6HC if no temperature daughterboards are in use. The photo on the right shows a sample cable that connects a LIS3DH or LIS3DSH board to a Duet 3 Mini 5+ or Duet 3 MB6HC. This wiring scheme has been designed to keep the wire carrying CS away from the other signal wires. 

Both cables are wired as follows:

| Wire# | Accelerometer signal | Duet signal |
|:---|:---|
| 1 | not connected | not connected |
| 2 | GND | GND |
| 3 | not connected | not connected |
| 4 | SCL | SPI_SCK |
| 5 | SDA | SPI_MOSI |
| 6 | SDO | SPI_MISO |
| 7 | INT1 | IO_3.IN (Duet 3 Mini)|
| ^^ | ^^ | SPI.CS2 (MB6HC)|
| ^^ | ^^ | SPI.CS3 (Duet 2, MB6XD)|
| ^^ | ^^ | TWCK0 (Maestro)|
| 8 | 3V3 or VCC | +3V3 |
| 9 | CS | IO_3.OUT (Duet 3 Mini) |
| ^^ | ^^ | SPI.CS3 (MB6HC)|
| ^^ | ^^ | SPI.CS4 (Duet 2, MB6XD)|
| ^^ | ^^ | TWD0 (Maestro)|
| 10 | not connected | not connected |

#### Tabs {.tabset}

##### Duet 3 MB6HC and 6XD

On **Duet 3 MB6HC** and **Duet 3 MBXD** you can use either of the wiring schemes, ie connect all wires to the temperature daughterboard connector, or connect CS and INT1 to IO_n.out and IO_n.in. 

##### Duet 3 Mini 5+

On **Duet 3 Mini 5+** only the IO_n.IN pins have interrupt capability, so choose one of the IO_n ports with both input and output connections, for example IO_3. Connect the accelerometer CS pin to IO_3.OUT and the accelerometer INT1 pin to IO_3.IN.

##### Duet 2 WiFi and Ethernet

On **Duet 2 WiFi and Ethernet** all the SPI CS pins have interrupt capability, so you can use any two spare CS pins on the temperature daughterboard connector.  For example, on a Duet 2 WiFi or Ethernet you could connect the accelerometer CS pin to SPI.CS4, and the INT1 pin to SPI.CS3. 

**Note:** if you already have one temperature daughterboard plugged into the main board and you wish to connect to SPI.CS4 and SPI.CS3 on the top of that daughter board, the daughter board passes SPI.CS4 and SPI.CS3 to the pin positions directly above the SPI.CS2 and SPI.CS1 pins on the main board. This is so that a second daughter board stacked on top automatically uses SPI.CS4 and SPI.CS3 instead of SPI.CS2 and SPI.CS1.

##### Duet 2 Maestro

On the **Duet 2 Maestro** use the same connections as for Duet 2 WiFi/Ethernet except that pins spi.cs4 and spi.cs3 on the daughter board connector are replaced by twd0 and twck0. 

#### Wiring recommendations

TO DO: Wire length/quality recommendations here. Some users on the forum have suggested using USB3 cable for longer runs.

**Note:** some users have found it difficult to get SPI-connected accelerometers to work with the Duet 3 Mini. We have found that one reason for this is that transitions on SDO are capacitively coupled into CS in the cable, especially if these signals use adjacent conductors. This causes a glitch on CS of a few nanoseconds, which is sufficient to cause the accelerometer to stop transmitting. This is why we recommend that you keep th CS signal away from other signal wires. Where this has not been done and the CS wire runs next to the SDO wire, a resistor with value between 100 ohms and 1K in series with SDO at the accelerometer end of the cable has solved the problem for some users.


### Duet 3 CAN-bus connected boards

#### Tabs {.tabset}

##### Duet 3 Toolboard 1LC v1.1

The accelerometer is built-in to the Toolboard, so no wiring (beyond connecting the Toolboard to the Duet 3 mainboard via Can bus) is necessary. Check that the accelerometer is recognised by sending `M122 B[board_CAN_address]` (eg `M122 B121`) and look for a line that reads "Accelerometer detected: yes, status: 00". 

##### Sammy-C21

Version 3.3beta3 of the sample Sammy-C21 firmware is configured to support a LIS3DH connected via I2C at address 0x18. You need to supply 3.3V power and ground to the LIS3DH, which you can obtain from the Sammy-C21 if you have not converted it to 5V operation. The SDA and SCL pins of the accelerometer must be connected to pins PA22 and PA23 of the Sammy-C21 respectively. The INT1 accelerometer pin must be connected to PA13.

The accelerometer must be configured at address 0x18. The Adafruit breakout board defaults to this address. The Sparkfun board can be configured at this address by bridging two pads on the underside.

# Configuration

Use command [M955](/User_manual/Reference/Gcodes/M955) to create an accelerometer; this will normally go in your config.g. 

To check current settings, send `M955 P[n]`, where [n] is the device-number. For an accelerometer connected locally via SPI, this will be P0. For an accelerometer on a CAN-connected board, use the form P[board-address.device-number], for example P121.0.

## Tabs {.tabset}

The C parameter of M955 is used in the following manner:

`C"aaa+bbb" Pins to use for CS and INT (in that order) when connecting the accelerometer via SPI`


### Duet 3 MB6HC and MB6XD

Depending on how you have wired it, use one of these commands to tell RRF about the accelerometer:

`M955 P0 C"spi.cs3+spi.cs2" ; all wires connected to temp DB connector`
`M955 P0 C"io3.out+io3.in" ; CS and INT1 connected to IO3`

### Duet 3 Mini 5+

Use this command to tell RRF about the accelerometer:

`M955 P0 C"io3.out+io3.in"`

### Duet 2 WiFi and Ethernet

Use this command to tell RRF about the accelerometer:

`M955 P0 C"spi.cs4+spi.cs3"`

### Duet 2 Maestro

Use this command to tell RRF about the accelerometer:

`M955 P0 C"twd0+twck0"`


### Duet 3 Toolboard 1LC v1.1 and later

You do not need to tell RRF about the accelerometer with M955; it will use the default M955 settings. However, you can use M955 if necessary to set accelerometer orientation, sampling rate or resolution. The P parameter is the CAN address of the Toolboard and accelerometer device number, eg P121.0. For example:

`M955 P121.0 I10 ; specify orientation of accelerometer on Toolboard 1LC with CAN address 121`

### Sammy-C21

You do not need to tell RRF about the accelerometer with M955; it will use the default M955 settings. However, you can use M955 if necessary to set accelerometer orientation, sampling rate or resolution. The P parameter is the CAN address of the Sammy-C21 and accelerometer device number, eg P124.0. For example:

`M955 P124.0 I10 ; specify orientation of accelerometer on Sammy-C21 with CAN address 124`

## Orientation

Accelerometer boards usually have an XYZ arrow to aid orientation. The Z axis is generally in the direction of the top face of the board/chip. The default alignment is to align the axes on the board with the axes of your machine.

![accelerometer_duet3_tb_1lc_v1.3_top.jpg](/manual/sensors/accelerometer_duet3_tb_1lc_v1.3_top.jpg =225x){target=_blank} ![accelerometer_adafruit_lis3dh.jpg](/manual/sensors/accelerometer_adafruit_lis3dh.jpg =225x){target=_blank} ![accelerometer_sparkfun_lis3dh.jpg](/manual/sensors/accelerometer_sparkfun_lis3dh.jpg =225x){target=_blank}

You can add parameter I (uppercase 'i') to the M955 command if you need to change the default orientation.

The I (orientation) parameter tells the firmware which of the 24 possible orientations the accelerometer chip is in relative to the printer axes. It is expressed as a 2-digit number. The first digit specifies which machine direction the Z axis of the accelerometer chip (usually the top face of the chip) faces, as follows: 0 = +X, 1 = +Y, 2 = +Z, 4 = -X, 5 = -Y, 6 = -Z. The second digit expresses which direction the X axis of the accelerometer chip faces, using the same code. If the accelerometer chip axes line up with the machine axis, the orientation is 20. This is the default orientation if no orientation has been specified.

Forum user [Nuramori](https://forum.duet3d.com/user/nuramori){target=_blank} has produced a [graphical guide to help illustrate the orientation options](https://www.dropbox.com/s/hu2w5mk57l4zqpg/Accelerometer%20Orientation.pdf){target=_blank} with a Duet 3 ToolBoard 1LC. His images use a Toolboard 1LC rev 1.1, however the same orientations apply all toolboards with onboard accelerometer: v1.1,v1.2 and v1.3). 

![accelerometer_i20_full.jpg](/accelerometer_i20_full.jpg){target=_blank}

The table below uses his work to show all of the possible orientations of a Duet 3 Toolboard 1LC, with the appropriate M955 command.

Match the image to your Duet 3 Toolboard 1LC orientation, and note the I value associated with the image. The OUT_0 connector on the board, a screw down terminal shown in blue below, is used for indexing, and is highlighted with a red circle if hidden. OUT_0 is normally used for the extruder heater.

| Accelerometer axis to machine axis |||||||
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| | +X to +X | +X to +Y | +X to +Z | +X to -X | +X to -Y | +X to -Z |
| +Z to +X | NA | ![accelerometer_i01.jpg](/manual/sensors/accelerometer_i01.jpg){target=_blank}<br>`M955 P[##] I01` | ![accelerometer_i02.jpg](/manual/sensors/accelerometer_i02.jpg){target=_blank}<br>`M955 P[##] I02`| NA | ![accelerometer_i05.jpg](/manual/sensors/accelerometer_i05.jpg){target=_blank}<br>`M955 P[##] I05` | ![accelerometer_i06.jpg](/manual/sensors/accelerometer_i06.jpg){target=_blank}<br>`M955 P[##] I06` |
| +Z to +Y | ![accelerometer_i10.jpg](/manual/sensors/accelerometer_i10.jpg){target=_blank}<br>`M955 P[##] I10` | NA | ![accelerometer_i12.jpg](/manual/sensors/accelerometer_i12.jpg){target=_blank}<br>`M955 P[##] I12` | ![accelerometer_i14.jpg](/manual/sensors/accelerometer_i14.jpg){target=_blank}<br>`M955 P[##] I14` | NA | ![accelerometer_i16.jpg](/manual/sensors/accelerometer_i16.jpg){target=_blank}<br>`M955 P[##] I16` |
| +Z to +Z | ![accelerometer_i20.jpg](/manual/sensors/accelerometer_i20.jpg){target=_blank}<br>`M955 P[##] I20` | ![accelerometer_i21.jpg](/manual/sensors/accelerometer_i21.jpg){target=_blank}<br>`M955 P[##] I21` | NA | ![accelerometer_i24.jpg](/manual/sensors/accelerometer_i24.jpg){target=_blank}<br>`M955 P[##] I24` | ![accelerometer_i25.jpg](/manual/sensors/accelerometer_i25.jpg){target=_blank}<br>`M955 P[##] I25` | NA |
| +Z to -X | NA | ![accelerometer_i41.jpg](/manual/sensors/accelerometer_i41.jpg){target=_blank}<br>`M955 P[##] I41` | ![accelerometer_i42.jpg](/manual/sensors/accelerometer_i42.jpg){target=_blank}<br>`M955 P[##] I42` | NA | ![accelerometer_i45.jpg](/manual/sensors/accelerometer_i45.jpg){target=_blank}<br>`M955 P[##] I45` | ![accelerometer_i46.jpg](/manual/sensors/accelerometer_i46.jpg){target=_blank}<br>`M955 P[##] I46` |
| +Z to -Y | ![accelerometer_i50.jpg](/manual/sensors/accelerometer_i50.jpg){target=_blank}<br>`M955 P[##] I50` | NA | ![accelerometer_i52.jpg](/manual/sensors/accelerometer_i52.jpg){target=_blank}<br>`M955 P[##] I52` | ![accelerometer_i54.jpg](/manual/sensors/accelerometer_i54.jpg){target=_blank}<br>`M955 P[##] I54` | NA | ![accelerometer_i56.jpg](/manual/sensors/accelerometer_i56.jpg){target=_blank}<br>`M955 P[##] I56` |
| +Z to -Z | ![accelerometer_i60.jpg](/manual/sensors/accelerometer_i60.jpg){target=_blank}<br>`M955 P[##] I60` | ![accelerometer_i61.jpg](/manual/sensors/accelerometer_i61.jpg){target=_blank}<br>`M955 P[##] I61` | NA | ![accelerometer_i64.jpg](/manual/sensors/accelerometer_i64.jpg){target=_blank}<br>`M955 P[##] I64` | ![accelerometer_i65.jpg](/manual/sensors/accelerometer_i65.jpg){target=_blank}<br>`M955 P[##] I65` | NA |

## Sampling rate and resolution

For most purposes the default sampling rate and resolution should suffice. The default is 1344Hz with 10 bit resolution for the LIS3DH, or 1600Hz with 16-bit resolution for the LIS3DSH. You can add R and S parameters to M955 if you want to change them. See [M955](/User_manual/Reference/Gcodes/M955) for details.

# Using accelerometers

Prior to making measurements with the accelerometer, disable any input shaping (including DAA if you are using it), unless of course you are trying to establish the effect of using input shaping.

Use command [M956](/User_manual/Reference/Gcodes/M956) to collect accelerometer data. RRF 3.3 only supports capture mode A0 (immediate capture), but the A parameter must be present. Here is a typical command to capture data for a travel move in the X direction:

```
; Duet 3 and 2, accelerometer connected to mainboard (M956 P0)
G1 X-50 G4 S2 M956 P0 S1000 A0 G4 P10 G1 X50 F20000

; Duet 3 Toolboard 1LC at CAN address 121 (M956 P121.0)
G1 X-50 G4 S2 M956 P121.0 S1000 A0 G4 P10 G1 X50 F20000 
```

This commands the X axis to position -50 (G1 X-50) and then waits for two seconds to let any ringing subside (G4 S2). Then it requests the accelerometer at the device-number (M956 P[n]) to collect 1000 samples (S1000) immediately (A0), followed by a pause of 10ms (G4 P10) to let the accelerometer start and provide a baseline. It then commands a travel move to X=+50 at 20000 mm/min (G1 X50 F20000).

Acceleration measurements captured during movement typically show higher frequency components caused by the motors and belts. For this reason, you may get results that are easier to interpret if you collect data immediately after a sharp stop, using a command such as this:

```
; Duet 3 and 2, accelerometer connected to mainboard (M956 P0)
G1 X-50 G4 S2 G1 X50 F20000 M400 M956 P0 S1000 A0

; Duet 3 Toolboard 1LC at CAN address 121 (M956 P121.0)
G1 X-50 G4 S2 G1 X50 F20000 M400 M956 P121.0 S1000 A0
```

RRF 3.4beta1 and later allow you to name the file that data is written to using an additional F parameter. The name should end in .csv so that the Accelerometer plugin for Duet Web Control recognises it.

## Retrieving the data

A successful M956 command causes a new file to be created in /sys/accelerometers. The filename includes the CAN address of the board to which the accelerometer is connected, and the date and time that the data was captured. It is in comma-separated-variable format so it can be imported into a spreadsheet. When browsing these files in DWC you may need to click the Refresh button to make DWC aware of newly-created files.

The last line of the file will report the average data rate and the number of overflows recorded. If the number of overflows is nonzero then data samples have been lost and the file should be discarded. Overflows are rare when using CAN-connected sensors, but more common with direct-connect sensors because a single task is used both to collect the data and to write it to file. If you find that you get overflows every time you collect data, try the following:

* Do not use an external SD card while collecting data
* Try a new SD card
* Try a faster SD card
* Reduce the number of samples requested, or the number of axes sampled
* Use a lower sampling rate

## Analysing the data

Duet Web Control 3.3 includes an Accelerometer plugin that can be used to display the accelerometer data and its Fourier transform. To enable it, first go to Settings/General/Built-in Plugins and start it. Then go to Settings/Machine Specific/Accelerometer.

[![accelerometer_03.jpg](/manual/sensors/accelerometer_03.jpg =800x)](/manual/sensors/accelerometer_03.jpg){target=_blank}

## Configure the Input Shaper

[Input shaping](/User_manual/Tuning/Input_shaping)