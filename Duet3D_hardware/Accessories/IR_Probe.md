---
title: Duet3D IR Probe
description: 
published: true
date: 2024-06-10T12:26:49.179Z
tags: 
editor: markdown
dateCreated: 2021-08-30T15:22:57.157Z
---

![ir_probe_top.jpg](/hardware/ir_probe/ir_probe_top.jpg =400x)

# Introduction

A differential modulated IR height sensor. For the advantages and disadvantages of various Z probes types see [Choosing a Z probe](/User_manual/Connecting_hardware/Z_probe_choosing).

# How it works

The sensor detects the target height by looking for the reflected light from two separate LEDs to be equal. The two LEDs are positioned such that they reflect light into the sensor at slightly different heights. This allows the sensor to be used on most bed surfaces.

It connects to the Duet family of controllers through a dedicated probe header.

# Hardware

The IR sensor comes with:

* 1x 3 way Molex SL type header
* 6x Molex SL crimps (3 spares)
* 2x M2.5-0.45mm pitch x 8mm Countersunk PZ screw

![ir_probe_kit.jpg](/hardware/ir_probe/ir_probe_kit.jpg =400x)

### 3d Model
Here is a [STEP format model of the Duet3D IR_Probe](https://github.com/Duet3D/Duet-2-Hardware/blob/master/Duet_Expansion/MiniLedSensor.stp){target=_blank}.

# Wiring, configuration and commissioning

For details on connecting the IR Sensor see [Connecting a Z probe](/User_manual/Connecting_hardware/Z_probe_connecting).

For the full detail on DC42's IR sensor see his [IR sensor web page](https://miscsolutions.wordpress.com/mini-height-sensor-board){target=_blank}.

# Bed surface

For best results, the sensor needs to sense the reflection from the top surface of the bed. There is a potential problem when the sensor is used with a transparent bed material that reflects infrared light weakly and there is a surface below the transparent material that reflects IR much more strongly. 

If you need to paint the top of an aluminium heat spreader or the underside of a PEI sheet matt black, then I recommend using spray-on barbecue & stove paint. It needs to be cured at an elevated temperature to harden. I have found 2 hours at 170C in a domestic electric fan oven works well. Caution: the temperature in a domestic oven without a fan will vary greatly in different parts of the oven.

Here is a guide to using the sensor with different print surfaces:

# Tabs {.tabset}

## Glass

With or without coatings such as hairspray, PVA or Kapton tape: works as-is if placed directly on a PCB bed heater or other surface that does not reflect strongly. If there is an aluminium heat spreader or bed plate underneath the glass, then either paint the aluminium surface matt black, or put a sheet of matt black paper between the glass and the aluminium. Coatings on the glass affect the trigger height slightly.

## PEI

This is highly transparent to IR. Paint the underside matt black (see below) before using adhesive sheet to attach it to the bed plate. Changes to the surface finish affect the trigger height slightly. We have a report that as an alternative to painting the underside black you can sand the top surface with very fine grit sandpaper until it has a dull matt appearance, but we have not confirmed this.

## BuildTak

The dark grey variant works well with the sensor. I have not tested the white variant, but it should work too.

## PrintBite

Early samples were found to be opaque to IR, but more recent samples are transparent to IR. This means that it needs to be painted black on the underside in order to work well with the IR sensor. However, this is not practical if the PrintBite sheet has the adhesive already attached.

## Anodised aluminium

With or without a thin PEI coating: suitable if the finish is matt or semi-matt

## Bright aluminium

not suitable

## Mirror

not suitable