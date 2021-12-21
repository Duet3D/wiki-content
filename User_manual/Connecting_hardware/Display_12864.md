---
title: Connecting 12864 or other display
description: 
published: true
date: 2021-11-12T16:45:39.027Z
tags: 
editor: markdown
dateCreated: 2021-11-10T15:08:34.637Z
---

# Connecting a 12864 display

Some Duets can now support 12864 mono graphics LCD displays with a rotary encoder.

There are two types of controller chip commonly used in these controllers: ST7920 and ST7567. Some Duets support one or both of these types - see below for details. Both types use a menu system stored on the SD card, see [Duet 2 Maestro 12864 display menu system](/User_manual/Connecting_hardware/Display_12864_menu).

Certain boards versions have different methods of controlling the backlight on these boards. 

## Compatibility matrix

| Duet | ST7920 | ST7567 | Notes |
|:---|:---|
| Duet 3 MB6HC | N | N |  |
| Duet 3 Mini 5+ | N | Y |  |
| Duet 2 Maestro | Y | Y | ST7567 Requires modified wiring loom |
| Duet 2 WiFi / Ethernet | N | Y | ST7567 Requires modified wiring loom |

## 12864 display using ST7920 controller chip

![displays_12864_01.jpg](/manual/displays/displays_12864_01.jpg =400x)

These displays are typically clones of the [RepRapDiscount Full Graphic Smart Controller](https://reprap.org/wiki/RepRapDiscount_Full_Graphic_Smart_Controller) and look like this. The better ones include a contrast adjustment potentiometer. Unfortunately some manufacturers of other displays using the same controller chip reverse the pinouts on the two ribbon cable connectors. The ST7920 controller chip is invariably powered from 5V, which means that the display need 5V input signal levels.

To configure this type of display in RepRapFirmware, use the [M918](/User_manual/Reference/Gcodes/M918) command with display type parameter P1.

## 12864 display using ST7567 controller chip

An example of this is the [Fysetc Mini 12864 Panel](https://wiki.fysetc.com/Mini12864_Panel). The controller chip is run from 3.3V, so these displays normally include level shifters which tolerate a wide range of input voltages. Note that this panel comes in a number of different revisions; V1.2 (with fixed backlight), and v2.1 (with NeoPixel backlight) are supported by most Duet boards with the right firmware version. V2.0, which uses 3 x PWM pins for the RGB LEDs, is not supported. 

To configure this type of display in RepRapFirmware, use the [M918](/User_manual/Reference/Gcodes/M918) command with display type parameter P2.

The contrast setting for these displays is done in software. the M918 command supports a C parameter for this purpose. It is also necessary to set a resistor ratio parameter in software, which can be done using the M918 R parameter.

## Duet 3 Mini 5+ support for 12864 displays

Duet 3 Mini provides two 2x5 ribbon cable headers for connecting a Fysetc 12864 Mini Panel version 1.2 or 2.1 (**not 2.0**, which uses PWM control of the backlight, and is not supported) or compatible ST7567-based controller. When using a version 2.1 controller, the colours of the three Neopixel LEDs built into the display can be set using the M150 command with LED type parameter X2.

We do not recommend connecting a 12864 display with ST7920 controller to the Duet 3 Mini because the 3.3V signals provided by the Duet 3 Mini do not meet the specifications of the ST7920 controller chip when it is powered from 5V. If you do wish to try it, you will most likely have to reduce the clock frequency (M918 F parameter) to get it working at all, and it may not work reliably. Also, note that when configured for 12864 display with ST7920 controller, RRF provides the CS signal on the pin normally uses for A0 because that more closely matched the pinout of typical 12864/ST7920 displays.

## Duet 2 Maestro support for 12864 displays

The Duet 2 Maestro provides two 2x5 ribbon cable headers for a 12864 display using ST7920 controller. The connector pinout is compatible with the original RepRapDiscount design. There is also more information in [this thread on the forum](https://forum.duet3d.com/topic/7609/).

RepRapFirmware 3.2 and later also support displays using the ST7567 controller. For these displays, use the standard cable EXCEPT the following two wires need to be connected to the EXPANSION header pins:

| Duet 2 Maestro output | Display input | Function |
|:---|:---|
| Expansion pin 4 (EXP_0) | EXP 1 pin 7 | LcdA0Pin |
| Expansion pin 5 (EXP_1) | EXP 1 pin 8 | LcdCSPin |

## Duet 2 WiFi/Ethernet support for 12864 displays

RepRapFirmware 3.2 and later support a 12864 display using ST7567 controller. RepRapFirmware 3.3 added support for a short string of Neopixels on Duet WiFi and Ethernet, so boards that use a Neopixel for the backlight should be able to be controlled. See [this thread on the forum](https://forum.duet3d.com/topic/22351/) for more details.

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

We do not recommend connecting a 12864 display with ST7920 controller because the 3.3V signals provided by the Duet 2 WiFi/Ethernet do not meet the specifications of the ST7920 controller chip when it is powered from 5V. If you do wish to try it, you will most likely have to reduce the clock frequency (M918 F parameter) to get it working at all, and it may not work reliably.

# Connecting BigTreeTech colour displays

(Instructions from [https://teamgloomy.github.io/tft.html](https://teamgloomy.github.io/tft.html), thanks!)

## Overview

The most recent version of the [standard bigtreetech TFT firmware](https://github.com/bigtreetech/BIGTREETECH-TouchScreenFirmware/tree/master/Copy%20to%20SD%20Card%20root%20directory%20to%20update) has built in support for RepRapFirmware. The pre-built images have this enabled by default.

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