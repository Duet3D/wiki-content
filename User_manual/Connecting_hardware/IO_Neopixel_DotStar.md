---
title: Neopixel and DotStar LEDs
description: 
published: true
date: 2021-11-10T16:55:55.667Z
tags: 
editor: markdown
dateCreated: 2021-11-10T16:54:19.555Z
---

# Introduction

Duet 3 MB6HC supports both DotStar and NeoPixel LED strips. Duet 3 Mini supports NeoPixel only. Duet 2 WiFi and Ethernet supports NeoPixel from RRF 3.3. You can also use an Arduino to interface LED strips to a Duet.

## Tabs {.tabset}

### Duet 3 MB6HC - DotStar or NeoPixel 

Connect the LED strips to the 4-pin connector labelled DS_LED.

* Connect the GND pin of the LED strip to the GND pin on the Duet DS_LED connector.
* Connect the +5V pin of the LED strip either to the 5V pin of the DS_LED connector, or to an external +5V power supply.
* For DotStar LED strips, connect the DotStar clock line to the DS_CK pin of the Duet and the DotStar data input line to DS_DO pin of the Duet. It's not always clear which way round these lines are on DotStar LED strips. If you swap the clock and data lines over then the LED strip will not work correctly, but no damage will be done.
* For NeoPixel strips, connect the data input line to the DS_DO pin of the Duet.

In firmware versions prior to 3.3 the maximum supported number of Neopixel LEDs in a strip is 60 RGB LEDs. Firmware 3.3 supports up to 80 RGBW LEDs or 106 RGB LEDs. The firmware limit for DotStar LEDs is several thousand.

**Caution!** LED strips can draw a considerable amount of power, usually up to 60mA per LED (for RGBW Neopixel LEDs, up to 80mA per LED). If you are powering the LED strip from the Duet, make sure that the Duet is able to supply sufficient current (maximum 3A total external load on +5V). In particular, if you are powering a Raspberry Pi or other single board computer from the Duet, then the Duet will not be able to power a LED strip as well.

### Duet 3 Mini 5+ - NeoPixel 

Connect the LED strips to the 3-pin connector labelled NP_LED.

* Connect the GND pin of the LED strip to the GND pin on the Duet NP_LED connector.
* Connect the +5V pin of the LED strip either to the EXT_5V_IN pin of the NP_LED connector, or to an external +5V power supply.
* Connect the data input line of the LED strip to the NEOPIXEL_DO pin of the Duet.

You will need to supply external 5V power to the adjacent 5V_IN connector, unless you connected the 5V line of your LED strip directly to an external 5V supply. The 5V regulator on the Duet 3 Mini is rated at 1A total and cannot be used to power LED strips.

In firmware versions prior to 3.3 the maximum supported number of Neopixel LEDs in a strip is 60 RGB LEDs. Firmware 3.3 supports up to 80 RGBW LEDs or 106 RGB LEDs.

### Duet 2 Wifi/Ethernet - NeoPixel 

Support for NeoPixel strips on Duet 2 WiFi/Ethernet was added in RRF 3.3.

A signal for controlling Neopixel strips can be output on pin 5 of the CONN_LCD connector provided that you do not have external stepper drivers connected to CONN_LCD. The signal level is 3.3V so you need to level shift it to 5V. A non-inverting 74HCT series gate or buffer such as 74HCT08 can be used to do this.

RGB NeoPixels draw up to 60mA per LED. RGBW ones draw up to 80mA per LED. Therefore the Duet cannot provide enough power for an LED strip unless the number of LEDs in the strip is small (for example 16 RGB or 12 RGBW LEDs). For longer strips you must provide external 5V power to the strip.

## Configuring and controlling LED strips

The [M150](/User_manual/Reference/Gcodes/M150) command is used both to configure and control LED strips. The X and Q parameters configure them and the remaining parameters set the colours. If the X and Q parameters are not provided, the last values of those parameters specified will be used again, or default parameters if they have never been specified. You do not normally need to specify the Q parameter, but you must specify the X parameter at least once unless the default is acceptable. On the Duet 3 MB6HC, the default is X0 (DotStar) in firmware 3.1.1 and earlier, and X1 in firmware 3.2 and later. On the Duet 3 Mini the default is always X1 (RGB Neopixel).