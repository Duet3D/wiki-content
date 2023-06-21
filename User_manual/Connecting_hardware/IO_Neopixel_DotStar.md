---
title: Neopixel and DotStar LEDs
description: 
published: true
date: 2023-06-21T14:51:54.420Z
tags: 
editor: markdown
dateCreated: 2021-11-10T16:54:19.555Z
---

# Introduction

Duet 3 Mainboard 6HC and 6XD support both DotStar and NeoPixel LED strips. Duet 3 Mini 5+ supports NeoPixel only. Duet 2 WiFi and Ethernet supports NeoPixel from RRF 3.3. You can also use an Arduino to interface LED strips to a Duet.

# LED support in RepRapFirmware

The maximum number of Neopixel LEDs supported per strip depends on the firmware version:

| | RRF 3.5 || RRF 3.4 || RRF 3.3 || RRF 3.2 and earlier ||
|---|---|
| | RGBW | RGB | RGBW | RGB | RGBW | RGB | RGBW | RGB |
| Duet 3 6HC/6XD | 240 | 320 | 240 | 320 | 80 | 106 | - | 60 |
| Duet 3 Mini 5+ | Unlimited^*^ ||  80 | 106 | 80 | 106 | - | 60 |
| Duet 3 Expansion & Tool boards | Unlimited^*^ || - | - | - | - | - | - |
| Duet 2 WiFi/Ethernet | Unlimited^*^ || 60 | 80 | - | 60 | - | - |
| Duet 2 Maestro | See below^**^ || - | - | - | - | - | - |

^*^Limited only by available RAM
^**^Neopixel support may work in RRF 3.5 on the Duet 2 Meastro, however this has not been tested by Duet3D. The maximum number supported would be limited by available RAM.

The maximum number of DotStar LEDs supported per strip is several thousand, for all firmware versions (Duet 3 6HC and 6XD only).

## Tabs {.tabset}

### RRF 3.5 and later

* Multiple LED strips are supported, up to 5 in a system (mainboard + expansion boards). 
* LED strips can be connected to the dedicated LED connector (Duet 3, 5V signalling) and/or any pin that can be used as a digital output, on mainboards or expansion boards (these pins will need level shifting from 3.3V to 5V, see Connections section below). 

### RRF 3.4 and earlier

* Only one LED strip is supported, connected to the dedicated LED connector on the main board (except as noted below for the Duet 3 Mini 5+):
  * Duet 3: DOTSTAR/NEOPIXEL pins, 5V signalling
  * Duet 2 WiFi/Ethernet: (RRF 3.3 and later) CONNLCD pin 5, 3.3V signalling (will need level shifting from 3.3V to 5V, see Connections section below). 
* On the Duet 3 Mini 5+, an additional RGB LED output is available on the 12864_EXP1 header, to support the backlight on LCD screens. See [Connecting 12864 or other display](/User_manual/Connecting_hardware/Display_12864).

# Connections

# Tabs {.tabset}

## Dedicated LED connector

#### Tabs {.tabset}

##### Duet 3 MB6HC - DotStar or NeoPixel 

Connect the LED strips to the 4-pin connector labelled DS_LED.

* Connect the GND pin of the LED strip to the GND pin on the Duet DS_LED connector.
* Connect the +5V pin of the LED strip either to the 5V pin of the DS_LED connector, or to an external +5V power supply.
* For DotStar LED strips, connect the DotStar clock line to the DS_CK pin of the Duet and the DotStar data input line to DS_DO pin of the Duet. It's not always clear which way round these lines are on DotStar LED strips. If you swap the clock and data lines over then the LED strip will not work correctly, but no damage will be done.
* For NeoPixel strips, connect the data input line to the DS_DO pin of the Duet.

**Caution!** LED strips can draw a considerable amount of power, usually up to 60mA per LED (for RGBW Neopixel LEDs, up to 80mA per LED). If you are powering the LED strip from the Duet, make sure that the Duet is able to supply sufficient current (maximum 0.8A total external load on +5V for MB6HC version 1.02 or later, 3A total for other MB6HC/MB6XD boards).

##### Duet 3 Mini 5+ - NeoPixel 

Connect the LED strips to the 3-pin connector labelled NP_LED.

* Connect the GND pin of the LED strip to the GND pin on the Duet NP_LED connector.
* Connect the +5V pin of the LED strip either to the EXT_5V_IN pin of the NP_LED connector, or to an external +5V power supply.
* Connect the data input line of the LED strip to the NEOPIXEL_DO pin of the Duet.

You will need to supply external 5V power to the adjacent 5V_IN connector, unless you connected the 5V line of your LED strip directly to an external 5V supply. The 5V regulator on the Duet 3 Mini is rated at 1A total and can only power a very small number of LEDs.

##### Duet 2 Wifi/Ethernet - NeoPixel 

Support for NeoPixel strips on Duet 2 WiFi/Ethernet was added in RRF 3.3.

A signal for controlling Neopixel strips can be output on pin 5 of the CONN_LCD connector provided that you do not have external stepper drivers connected to CONN_LCD. The signal level is 3.3V so you need to level shift it to 5V. A non-inverting 74HCT series gate or buffer such as 74HCT08 can be used to do this.

RGB NeoPixels draw up to 60mA per LED. RGBW ones draw up to 80mA per LED. Therefore the Duet cannot provide enough power for an LED strip unless the number of LEDs in the strip is small (for example 16 RGB or 12 RGBW LEDs). For longer strips you must provide external 5V power to the strip.

## Using other outputs (RRF 3.5 and later)

In RRF 3.5 and later, Neopixel LED strips can also be controlled by any pin that can be used as a low voltage digital output, on mainboards or expansion boards; for example an IO_OUT port on a Duet 3 series board.

Connect the LED strips as follows:
* Connect the GND pin of the LED strip to an available GND pin on the Duet, or to a GND shared with the Duet GND.
* Connect the +5V pin of the LED strip to an available 5V pin on the Duet (note current limitations below), or to an external +5V power supply.
* Connect the data input line of the LED strip to the chosen output pin.

RGB NeoPixels draw up to 60mA per LED. RGBW ones draw up to 80mA per LED. Check how many LEDs your Duet can safely provide power for. Short strips may be able to be powered by the Duet. For longer strips you must provide external 5V power to the strip.

If you use any pins apart from the ones that are designed for that purpose (i.e. the dedicated ones on the Duet 3 boards), then movement will be suspended any time M150 is used to update those LEDs. So OK at the start/end of a print, or the end of heating up, but not a good idea during a print. This is because if the port the LEDs are connected to doesn't have hardware support for LEDs, the CPU has to stop all other activity including step pulse generation in order to generate the correct pulses. Exception: addressing the first or only LED strip configured on a RP2040-based expansion board will not cause movement to be suspended.

If you specify too many LEDs then you may run out of RAM, especially on Duet 2, TOOL1LC and EXP1XD.

The M950 Q parameter is only used when the port is the dedicated LED port on a Duet 3 series board. Otherwise it is ignored.

When using general purpose output pins (i.e. not the LED ports on Duet 3 series boards) to control LEDs, the signalling level will be 3.3V; whereas almost all types pf Neopixel LED require at least 3.5V signals for reliable operation. You should to do one of the following:
* Level shift the signal to 5V. A non-inverting 74HCT series gate or buffer such as 74HCT08 can be used to do this.
* Use Neopixels that accept 3.3V signals (they exist now but are not common)
* Reduce the supply voltage to the LED strip below 5V. You could probably get away with using a single 1N400x diode to drop the 5V power voltage to the entire strip, or see [this Hackaday article](https://hackaday.com/2017/01/20/cheating-at-5v-ws2812-control-to-use-a-3-3v-data-line/)

# Configuring and controlling LED strips

## Tabs {.tabset}

### RRF 3.5 and later

From RRF 3.5.0-beta.4, [M950](/User_manual/Reference/Gcodes/M950) is used to configure the LED strip, and [M150](/User_manual/Reference/Gcodes/M150) is used to control the strip. Multiple strips can be configured in M950.

### RRF 3.4 and earlier

In RRF 3.4 and earlier, the [M150](/User_manual/Reference/Gcodes/M150) command is used both to configure and control LED strips. 

The X and Q parameters configure them and the remaining parameters set the colours. If the X and Q parameters are not provided, the last values of those parameters specified will be used again, or default parameters if they have never been specified. You do not normally need to specify the Q parameter, but you must specify the X parameter at least once unless the default is acceptable. On the Duet 3 MB6HC the default is X0 (DotStar) in firmware 3.1.1 and earlier, and X1 (RGB Neopixel) in firmware 3.2 and later. On other boards the default is always X1.

If the S parameter is omitted then as many LEDs as can be set in a single chunk will be addressed which depends on the board (e.g. 60 RGBW neopixels on Duet2, many more on Duet 3). We recommend users always explicitly set the number of LEDs to address, rather than rely on this behaviour as the number of LEDs addressed in a single chunk may change in the future.
