---
title: Using triggers to control the Duet
description: 
published: false
date: 2022-05-31T14:19:20.035Z
tags: 
editor: markdown
dateCreated: 2022-05-31T14:19:20.035Z
---

# Introduction

RepRapFirmware allows you to define external 'triggers' (such as a button press) that will run an associated system macro. This could be for an [emergency stop](/User_manual/Connecting_hardware/IO_E_stop){target=_blank}, but can be used for many other purposes, as the GCode commands that are run are held in a macro file for each trigger.

Below is an example, where user Clinton Thomas builds a control panel. This originally appeared here: [Duet Dozuki wiki](https://duet3d.dozuki.com/Wiki/Using_M581_-_External_Triggers_and_Building_a_Control_Panel){target=_blank}

# Required Hardware

* Some kind of push button or switch (eg Idec and Allen-Bradley brand push buttons with Normally Open contact blocks)
* Dupont connectors
* Wire

# Wiring

Connect one side of your switch to GND or the negative coming from your power supply. The other side of the switch will go to a pin on the Duet, using Dupont connectors.

## Tabs {.tabset}

### Duet 3

### Duet 2

| [image 1703 **UPDATE LINK**]() | [image 1704 **UPDATE LINK**]() |

If you look closely you'll see that I used 2P Dupont connectors but only one of the pins has a wire going to it. This is because 1P connectors kept falling off so I put a wire in one side of a 2P connector with just an empty pin in the other side.

 I used the following pins:

* Pin 4 = E2_STOP
* Pin 9 = E3_STOP
* Pin 14 = E4_STOP
* Pin 19 = E5_STOP
* Pin 26 = E6_STOP
* Pin 44 = RESET

# Firmware configuration

Pin 44 forces a reset when shorted to ground so that button was easy enough. For the rest, I had to make changes to the config file. I found the following digging around RepRap.org:

```
M581: Configure external trigger
Parameters
    Tnn Logical trigger number to associate the endstop input(s) with, from zero up to a firmware-specific maximum (e.g. 9 for RepRapFirmware)
    X, Y, Z, E Selects endstop input(s) to monitor
    P Reserved, may be used in the future to allow general I/O pins to cause triggers
    S Whether trigger occurs on a rising edge of that input (S1, default), falling edge (S0), or ignores that input (S-1). By default, all triggers ignore all inputs.
    C Condition: whether to trigger at any time (C0, default) or only when printing a file from SD card (C1)

In RepRapFirmware, trigger number 0 causes an emergency stop as if
 M112 had been received. Trigger number 1 causes the print to be paused as if M25 had been received. Any trigger number # greater then 1 causes the macro file sys/trigger#.g to be executed.
```

So, in order to get buttons for pause, home, ATX on, and disable steppers, I had to add the following to my config file:

```
; Input/Output
M581 E2 S1 T1 C1         ; Pause - PIN4
M581 E5 S1 T3 C0         ; Disable Steppers - PIN19 - trigger3.g
M581 E4 S1 T4 C0         ; Home All - PIN14 - trigger4.g
M581 E3 S1 T2 C0         ; ATX On - PIN9 - trigger2.g
```

Finally, I had to add macro trigger files. Which are just text files containing the respective commands saved as trigger#.gcode.

```
;tigger2.g
M80            ; ATX ON
```

```
;trigger3.g
M400            ; Finish Current Moves
M18            ; Disable Steppers
```

```
;trigger4.g
M400            ; Finish current moves
G28            ; Home all axes
```