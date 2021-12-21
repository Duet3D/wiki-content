---
title: Dual Stepper Driver Expansion Module
description: 
published: true
date: 2021-12-13T12:42:40.961Z
tags: 
editor: markdown
dateCreated: 2021-08-10T14:12:26.053Z
---

![dual_stepper_driver_expansion_top.jpg](/duet_boards/duet_2_expansion/dual_stepper_driver_expansion_top.jpg =400x)

# Introduction

This is an expansion module specifically designed to connect to the two external stepper headers on the Duet 2 Maestro. It has two TMC2224 drivers on board, bringing the total drivers on the Maestro to 7.

# Features

The Duet 2 Maestro has two external stepper driver ports and this module is design to connect to them and provides:

* 2 x Super quiet TMC2224 stepper drivers: SPI controlled and capable of up to 256 microstepping with optional interpolation from any lower microstepping setting to x256
* Configuration of microstepping, power, stepping mode from the Duet 2 Maestro - the same as the on board drivers.
* These drivers can be used as two additional extruders or remapped using [M584](/User_manual/Reference/Gcodes/M584) to another axis. For example this allows the Maestro to be used for a IDEX style printer or a 4 colour mixing hotend printer.

# Mounting

The 2 sets of 8 pins mate with the matching headers on the Duet 2 Maestro:

![dual_stepper_driver_expansion_mounting_01.jpg](/duet_boards/duet_2_expansion/dual_stepper_driver_expansion_mounting_01.jpg =400x)

![dual_stepper_driver_expansion_mounting_02.jpg](/duet_boards/duet_2_expansion/dual_stepper_driver_expansion_mounting_02.jpg =400x)

![dual_stepper_driver_expansion_mounting_03.png](/duet_boards/duet_2_expansion/dual_stepper_driver_expansion_mounting_03.png =400x)


Optionally a plastic stand off can be used to support the module through the matching hole in the Duet 2 Maestro.

# Wiring/ Pinout

One or two motors can be connected to the expansion module. They are numbered Drive 5 (E2) and Drive 6 (E3) within RepRapFirmware.

![dual_stepper_driver_expansion_v1.0_wiring.png](/duet_boards/duet_2_expansion/dual_stepper_driver_expansion_v1.0_wiring.png =400x)

# Errata

A PCB layout error (this board was not designed by Duet3D) has been discovered that causes Drive 5 (E2) to function poorly at high speeds. See [this thread on the forum](https://forum.duet3d.com/topic/16124/) for how to fix this.