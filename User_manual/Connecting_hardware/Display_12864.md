---
title: Connecting 12864 or other display
description: 
published: true
date: 2023-09-22T09:17:16.299Z
tags: 
editor: markdown
dateCreated: 2021-11-10T15:08:34.637Z
---

# Connecting a 12864 display

Some Duets support 12864 mono graphics LCD displays with a rotary encoder.

There are two types of controller chip commonly used in these controllers: ST7920 and ST7567. Some Duets support one or both of these types - see below for details. Both types use a menu system stored on the SD card, see [Duet 2 Maestro 12864 display menu system](/User_manual/Connecting_hardware/Display_12864_menu).

Certain board versions have different methods of controlling the backlight on these boards. 

## Compatibility matrix

| Duet | ST7920 | ST7567 | Notes |
|:---|:---|
| Duet 3 MB6HC/6XD | N | N |  |
| Duet 3 Mini 5+ | N | Y |  |
| Duet 2 Maestro | Y | Y | ST7567 Requires modified wiring loom |
| Duet 2 WiFi / Ethernet | N | Y | ST7567 Requires modified wiring loom |

## Tabs{.tabset}

### Duet 3 Mini 5+

Duet 3 Mini 5+ provides two 2x5 ribbon cable headers for connecting a compatible ST7567-based controller (see below).

We do not recommend connecting a 12864 display with ST7920 controller directly to the Duet 3 Mini 5+ because the 3.3V signals provided by the Duet 3 Mini do not meet the specifications of the ST7920 controller chip when it is powered from 5V. If you do wish to try it, you will most likely have to reduce the clock frequency (M918 F parameter) to get it working at all, and it may not work reliably. Also, note that when configured for 12864 display with ST7920 controller, RepRapFirwmare provides the CS signal on the pin normally uses for A0 because that more closely matched the pinout of typical 12864/ST7920 displays.

User PCR on the Duet forum has created a level-shifting board that plugs into the Mini 5+, and converts the 3.3V signalling to 5V, allowing ST7920-based displays to be supported. See [this thread on the forum](https://forum.duet3d.com/post/236333){target=_blank}.

### Duet 2 Maestro

The Duet 2 Maestro provides two 2x5 ribbon cable headers for a 12864 display using ST7920 controller. The connector pinout is compatible with the original RepRapDiscount design. There is also more information in [this thread on the forum](https://forum.duet3d.com/topic/7609/){target=_blank}.

RepRapFirmware 3.2 and later also support displays using the ST7567 controller. For these displays, use the standard cable EXCEPT the following two wires need to be connected to the EXPANSION header pins:

| Duet 2 Maestro output | Display input | Function |
|:---|:---|
| Expansion pin 4 (EXP_0) | EXP 1 pin 7 | LcdA0Pin |
| Expansion pin 5 (EXP_1) | EXP 1 pin 8 | LcdCSPin |

#### Neopixel LED backlight support

**RRF 3.5 and later:** Neopixel support may work in RRF 3.5 on the Duet 2 Meastro, however this has not been tested by Duet3D. The maximum number supported would be limited by available RAM.

**RRF v3.4.5 and earlier:** The Duet 2 Maestro does not currently support the M150 command for controlling addressable LEDs/NeoPixel backlights, because Maestro is very short of spare outputs. Displays with a fixed backlight should work, eg Fysetc Mini12864 Panel V1.2 (NOT V2.0 or V2.1), but have not been tested.

### Duet 2 WiFi/Ethernet

RepRapFirmware 3.2 and later support a 12864 display using ST7567 controller. RepRapFirmware 3.3 added support for a short string of Neopixels on Duet WiFi and Ethernet, so boards that use a Neopixel for the backlight should be able to be controlled. See [this thread on the forum](https://forum.duet3d.com/topic/22351/){target=_blank} for more details.

You will need to make up your own wiring loom, connected as follows:

| Duet 2 WiFi/Ethernet output | Display input | Function |
|:---|:---|
| CONN_LCD pin 2 | EXP 1 pin 2 | gnd |
| CONN_LCD pin 3 | EXP 2 pin 6 | EncoderPinB |
| CONN_LCD pin 4 | EXP 2 pin 8 | EncoderPinA |
| CONN_LCD pin 5 | EXP 1 pin 5 | LcdNeopixelPin |
| CONN_LCD pin 6 | EXP 1 pin 6 | LcdResetPin |
| CONN_LCD pin 7 | EXP 1 pin 7 | LcdA0Pin |
| CONN_LCD pin 8 | EXP 1 pin 8 | LcdCSPin |
| CONN_LCD pin 9 | EXP 1 pin 9 | EncoderPinSw |
| CONN_LCD pin 10 | EXP 1 pin 10 | LcdBeepPin |
| CONN_SD pin 1 | EXP 1 pin 1 | +5V |
| CONN_SD pin 2 | EXP 2 pin 2 | gnd |
| CONN_SD pin 3 | EXP 2 pin 7 | SD CS |
| CONN_SD pin 4 | EXP 2 pin 9 | sck |
| CONN_SD pin 5 | EXP 2 pin 5 | mosi |
| CONN_SD pin 6 | EXP 2 pin 10 | miso |

We do not recommend connecting a 12864 display with ST7920 controller because the 3.3V signals provided by the Duet 2 WiFi/Ethernet do not meet the specifications of the ST7920 controller chip when it is powered from 5V. If you do wish to try it, you will most likely have to reduce the clock frequency (M918 F parameter) to get it working at all, and it may not work reliably. Alternatively, use a level-shifter to convert the 3.3V signalling to 5V.

## Compatible displays

## Tabs{.tabset}

### 12864 displays using ST7567 controller chip

* [Fysetc Mini12864 Panel V1.2 or V2.1](https://wiki.fysetc.com/Mini12864_Panel){target=_blank}
Note that this panel comes in a number of different revisions; V1.2 (with fixed backlight), and v2.1 (with NeoPixel backlight) are supported by most Duet boards with the right firmware version. V2.0, which uses 3 x PWM pins for the RGB LEDs, is not supported.
When using a version 2.1 controller, the colours of the three Neopixel LEDs built into the display can be set using the M150 command with LED type parameter X2. 
Note that Duet 2 Maestro does not currently support M150, so only the V1.2 version works (not tested).
* [BigTreeTech Mini12864 V1.0 and V2.0](https://github.com/bigtreetech/MINI-12864/){target=_blank}
V1.0 is effectively a clone of the Fysetc Mini12864 V2.1 panel. V2.0 adds another connector, but should work the same as the V1.0 board.
* Mellow FLY Mini12864 V1.0 Panel (Available on AliExpress)
Again, this is understood to be a clone of the Fysetc Mini12864 V2.1 panel.
* [Makerbase MKS Mini12864 V3.0](https://github.com/makerbase-mks/MKS-MINI12864-V3){target=_blank}
Another clone of the Fysetc Mini12864 V2.1 panel, but the unit tested needed a slightly different configuration.

### 12864 displays using ST7920 controller chip
These displays are typically clones of the [RepRapDiscount Full Graphic Smart Controller](https://reprap.org/wiki/RepRapDiscount_Full_Graphic_Smart_Controller){target=_blank} and look like this. 

![displays_12864_01.jpg](/manual/displays/displays_12864_01.jpg =400x)

The ST7920 controller chip is invariably powered from 5V, which means that the display need 5V input signal levels. The better ones include a contrast adjustment potentiometer.

## Connecting 12864 displays

* Using the supplied ribbon cables, connect the 12864_EXP1 header on the Duet to the EXP1 header on the display. Connect the 12864_EXP2 header one the Duet to the EXP2 header on the display. Usually, both headers and the ribbon cable are keyed so they can only go on one way around.
* Some displays are supplied with the ribbon cable or the EXP1 and EXP2 headers wired 180° rotated.
  * You won't damage the display if it is connected the wrong way around 
  * A quick check is to rotate the encoder on the display. If it resets the Duet, it is wired 180° rotated, as the encoder will be connected to the reset pin on the EXP2 connector.
  * If you need to rotate the cable, instead of cutting the tab off the ribbon cable, you can usually pull the header shroud off the pins on the display. Then put it back on rotated 180°. Do this to both shrouds, on EXP1 and EXP2, then reconnect the ribbon cables.
* The backlight on ST7920-based displays should turn on when powered. The backlight and LEDs on ST7567-based displays will stay off until an M150 command is sent, unless it is the Fysetc Mini12864 Panel V1.2 with fixed backlight.

## Configuring 12864 displays

With the display connected, menu files need to be added to the SD card, or the display will have nothing to show. See [12864 display menu system](/User_manual/Connecting_hardware/Display_12864_menu).

## Tabs {.tabset}

### ST7567

To configure this type of display in RepRapFirmware, use the [M918](/User_manual/Reference/Gcodes/M918) command with display type parameter P2. The contrast setting for these displays is done in software, and the M918 command supports a C parameter for this purpose. It may also be necessary to set a resistor ratio parameter in software, which can be done using the M918 R parameter.

For displays with NeoPixel RGB LED backlights, the configuration depends on which firmware version you are running. RRF 3.5 and later uses M950 to configure the LEDs, and M150 to control them. RRF 3.4 and earlier uses M150 to both configure and control LEDs. These commands will usually be added to the config.g, so the display is enabled at startup. 

#### Tabs {.tabset}

##### RepRapFirmware 3.5 and later

From RRF 3.5.0-beta.4, [M950](/User_manual/Reference/Gcodes/M950) is used to configure displays that support NeoPixel RGB backlights, and [M150](/User_manual/Reference/Gcodes/M150) is used to control the LEDs.

Example for Fysetc Mini12864 V2.1 display with Neopixel LEDs on a Duet 3 Mini 5+ 12864_EXP1 connector:
```
M918 P2 E-4 F2000000           ; Fysetc 12864mini
M950 E1 C"io3.out" T1 U3       ; create a RGB Neopixel LED strip with 3 LEDs on the Duet 3 Mini 5+ 12864_EXP1 header
M150 E1 R0 U0 B255 P255 S1 F1  ; display led blue
M150 E1 R255 U0 B0 P255 S1 F1  ; left encoder led red
M150 E1 R0 U255 B0 P255 S1 F0  ; right encoder led green
```

Example for Makerbase MKS Mini12864 V3.0
```
M918 P2 E-4 C70 R4             ; enable MKS Mini12864
M950 E0 C"io3.out" T1 U3       ; create a RGB Neopixel LED strip with 3 LEDs on the Duet 3 Mini 5+ 12864_EXP1 header
M150 E0 R255 U0 B0 P255 S3 F0  ; GRB LEDs so this shows green
```

Note that some RGB LEDs define their colours in the order Green, Red, Blue, so R parameter will be green, and U parameter will be blue.

##### RepRapFirmware 3.4 and earlier

On displays that support NeoPixel RGB backlights, set these with [M150](/User_manual/Reference/Gcodes/M150). 

Example for Fysetc Mini12864 V2.1
```
M918 P2 E2 F2000000               ; configure direct-connect display
M150 X2 R255 U255 B255 P127 S1 F1 ; display led
M150 X2 R0 U0 B0 P0 S1 F1         ; left encoder led off
M150 X2 R0 U0 B0 P0 S1 F0         ; right encoder led off
```
Example for Makerbase MKS Mini12864 V3.0
```
M918 P2 E-4 C100 R3
M150 X2 R255 U0 B0 P255 S3 F0 ; GRB LED so this shows green
```
Note that some RGB LEDs define their colours in the order Green, Red, Blue, so R parameter will be green, and U parameter will be blue.

### ST7920

To configure this type of display in RepRapFirmware, use the [M918](/User_manual/Reference/Gcodes/M918) command with display type parameter P1, eg:
```
M918 P1 E4 F2000000
```

# Connecting BigTreeTech colour displays

(Instructions from [https://teamgloomy.github.io/tft.html](https://teamgloomy.github.io/tft.html){target=_blank}, thanks!)

## Overview

Recent version of the [standard bigtreetech TFT firmware](https://github.com/bigtreetech/BIGTREETECH-TouchScreenFirmware/tree/master/Copy%20to%20SD%20Card%20root%20directory%20to%20update){target=_blank} has built in support for RepRapFirmware. The pre-built images have this enabled by default.

> As of September 2023, BTT firmware still MOSTLY works with RRF. Heating, movement, monitoring prints etc works, however accessing the Duet filesystem does not. There may be other quirks that break RRF support as the firmware develops. Duet3D is not responsible for the BigTreeTech firmware; please contact BTT to get these fixed. See [this thread on the forum](https://forum.duet3d.com/topic/33572/bigtreetech-graphic-display-on-duet-wifi){target=_blank} for more information.
{.is-info}


## Connection to the board

Use the pins +5V, GND, IO_0_OUT and IO_0_IN on the IO_0 header (Duet 3), or +5V, GND, TX and RX on the PanelDue header (Duet 2). These should be connected to +5V, GND, TX and RX on the TFT, making sure that TX and RX are swapped.

| BTT display | Duet 3 IO_0 header | Duet 2 PanelDue header |
|---|---|
| +5V | +5V | +5V |
| GND | GND | GND |
| TX | IO_0_IN | URXD0 |
| RX | IO_0_OUT | UTXD0 |

## Loading the firmware

There is now an RRF config.ini (on the SD card root). It needs to be renamed from config_rrf.ini to config.ini (replacing the original one for Marlin) for flashing of the firmware.

## Config.g Changes

Add the following line to your config.g to enable the screen
```
M575 P1 S1 B57600
```