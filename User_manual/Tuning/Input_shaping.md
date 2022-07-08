---
title: Input shaping
description: This page describes the reasons for using input shaping and the support for input shaping in RepRapFirmware. 
published: true
date: 2022-07-08T12:51:54.656Z
tags: 
editor: markdown
dateCreated: 2021-12-03T19:33:14.384Z
---

# Introduction

This page describes the reasons for using input shaping and the support for input shaping in RepRapFirmware. Note, as at firmware version 3.4, input shaping is only applied to axis motors driven directly from the Duet3 main board, not to axis motors driven from CAN-connected expansion boards.

# What is ringing?

Motion systems typically involve a component having significant mass (e.g. a tool head) driven by a system having significant elasticity. In a 3D printer the most obvious sources of elasticity are the belts; however the stepper motor driving the belts or leadscrews also have elasticity. The combination of a mass driven or supported by an elastic medium  forms a simple harmonic oscillator. The greater the mass and the more elastic the medium, the lower will be the resonant frequency.

When the mass is accelerated, this resonance will be excited. After reaching the commanded position, the mass (e.g. tool head) will oscillate slightly about the commanded position. This phenomenon is called ringing. The oscillations will subside over a period of time.

In a 3D printer the most obvious effect of ringing is ghosting.  Where there are sharp corners, there are small ripples (ghosts of the corner) in what should be smooth surfaces, at regular intervals from the corner.

Typically, the lower the ringing frequency and the higher the acceleration used, the worse the ghosting is.

# What is input shaping?

Each time the acceleration of the tool head changes, any resonances are excited. Input shaping adjusts the period of acceleration and/or breaks the acceleration up into a number of steps of different sizes, so that later acceleration changes induce ringing equal and opposite to the ringing induces by earlier acceleration changes.

# How do i use it?

Install and configure your accelerometer and capture some data as described here: [Accelerometers](/User_manual/Connecting_hardware/Sensors_Accelerometer)

There is an DWC plugin that will help signifcantly in tuning input shaping to match your specific mahcine, see: [Input shaping plugin](/User_manual/Tuning/Input_shaping_plugin)

Configure the input shaper using the M593 gcode command and do some test prints that elicit ringing patterns. M593 details and usage: [M593 Configure Input Shaping](/User_manual/Reference/Gcodes/M593)

A ringing test print works well and you can change the shaper being used every several layers on the fly by sending a new M593. A good test print STL can be [found here](https://www.klipper3d.org/prints/ringing_tower.stl),