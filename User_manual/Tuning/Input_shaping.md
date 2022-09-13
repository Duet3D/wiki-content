---
title: Input shaping
description: This page describes the reasons for using input shaping and the support for input shaping in RepRapFirmware. 
published: true
date: 2022-09-13T16:24:17.144Z
tags: 
editor: markdown
dateCreated: 2021-12-03T19:33:14.384Z
---

# Introduction

This page describes the reasons for using input shaping and the support for input shaping in RepRapFirmware. Note, as at firmware version 3.4, input shaping is only applied to axis motors driven directly from the Duet3 main board, not to axis motors driven from CAN-connected expansion boards.

# What is ringing?

Motion systems typically involve a component having significant mass (e.g. a tool head) driven by a system having significant elasticity. In a 3D printer the most obvious sources of elasticity are the belts; however the stepper motors driving the belts or leadscrews also have elasticity. The combination of a mass driven or supported by an elastic medium  forms a simple harmonic oscillator. The greater the mass and the more elastic the medium, the lower will be the resonant frequency.

When the mass is accelerated, this resonance will be excited. After reaching the commanded position, the mass (e.g. tool head) will oscillate slightly about the commanded position. This phenomenon is called ringing. The oscillations will subside over a period of time.

In a 3D printer the most obvious effect of ringing is ghosting.  Where there are sharp corners, there are small ripples (ghosts of the corner) in what should be smooth surfaces, at regular intervals from the corner.

Typically, the lower the ringing frequency and the higher the acceleration used, the worse the ghosting is.

# What is input shaping?

Each time the acceleration of the tool head changes, any resonances are excited. Input shaping adjusts the period of acceleration and/or breaks the acceleration up into a number of steps of different sizes, so that later acceleration changes induce ringing equal and opposite to the ringing induces by earlier acceleration changes.

For the technically minded, here are some references to academic papers about input shaping:

[https://www.researchgate.net/publication/316556412_INPUT_SHAPING_CONTROL_TO_REDUCE_RESIDUAL_VIBRATION_OF_A_FLEXIBLE_BEAM]
[http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.465.1337&rep=rep1&type=pdf]


# How do i use it?

Install and configure your accelerometer and capture some data as described here: [Accelerometers](/User_manual/Connecting_hardware/Sensors_Accelerometer)

There is an DWC plugin that will help signifcantly in tuning input shaping to match your specific mahcine, see: [Input shaping plugin](/User_manual/Tuning/Input_shaping_plugin)

Configure the input shaper using the M593 gcode command and do some test prints that elicit ringing patterns. M593 details and usage: [M593 Configure Input Shaping](/User_manual/Reference/Gcodes/M593)

A ringing test print works well and you can change the shaper being used every several layers on the fly by sending a new M593. A good test print STL can be [found here](https://www.klipper3d.org/prints/ringing_tower.stl),

# Why don't we different shaping for the X and Y axes?

Two reasons:

1. Supporting different input shaping frequencies and/or algorithms on the X and Y axes would mean that if input shaping is applied to printing moves, the tool path will no longer follow the path commanded by the slicer during acceleration and deceleration. As a result, artefacts will occur in the print, especially around corners. The artefacts can be reduced by a smoothing process (as is done by Klipper firmware), but of course this reduces detail, for example by rounding corners that are supposed to be square.

2. It is rarely if ever necessary to use different shaping on X and Y axes, because a single input shaper can suppress a wide range of resonant frequencies. The EI3 shaper suppresses ringing over a frequency range of 3:1. Resonant frequencies vary in proportion to the square root of the mass being moved and the compliance of the belt or other elastic medium; so you would need these elements to differ by a factor of more than 9:1 between the X and Y axes for the resonant frequencies to exceed a 3:1 ratio. In practice, the resonant frequencies to be suppressed raely exceed a ratio of 2:1.