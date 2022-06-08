---
title: Using triggers to control the Duet
description: 
published: false
date: 2022-06-08T16:03:39.157Z
tags: 
editor: markdown
dateCreated: 2022-05-31T14:19:20.035Z
---

# Introduction

RepRapFirmware allows you to define external 'triggers' (such as a button press) that will run an associated system macro. This could be for an [emergency stop](/User_manual/Connecting_hardware/IO_E_stop){target=_blank}, but can be used for many other purposes, as the GCode commands that are run are held in a macro file for each trigger.

Below is an example, where Duet user Clinton Thomas builds a control panel, with a number of buttons which control various actions. This originally appeared here, using a Duet 2 and RRF 2.x: [Duet Dozuki wiki](https://duet3d.dozuki.com/Wiki/Using_M581_-_External_Triggers_and_Building_a_Control_Panel){target=_blank}

# Required hardware

* Some kind of push button or switch (eg Idec and Allen-Bradley brand push buttons with Normally Open contact blocks)
* Dupont connectors
* Wire

# Wiring

Connect one side of your switch to GND or the negative coming from your power supply. The other side of the switch will go to an input pin on an IO or endstop connector on the Duet, using Dupont connectors.

## Tabs {.tabset}

### Duet 3

Wire the switch in the same way as an endstop, ie 

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

Triggers are configured in the firmware using [M581](/User_manual/Reference/Gcodes/M581). The parameters for RRF 3.01 and later (see GCode dictionary for earlier versions) are:

* **P** Specify one or more input pin numbers that you created using M950 with the J parameter, or -1 to delete the trigger
* **Tnn** Logical trigger number to associate the input(s) with, from zero up to a firmware-specific maximum
* **S** Whether trigger occurs on an inactive-to-active edge of that input (S1, default), active-to-inactive edge (S0), or ignores that input (S-1).
* **R** Condition: whether to trigger at any time (R0, default), only when printing a file from SD card (R1), or only when not printing a file from SD card (R2, supported in RRF 3.2 and later). R-1 temporarily disables the trigger.
* **X**, **Y**, **Z** or any other axis letter: axis or axes whose endstop switches are to cause the trigger

Note that:
* Trigger number 0 causes an emergency stop as if M112 had been received. Trigger number 1 causes the print to be paused as if M25 had been received. Any trigger number # greater than 1 causes the macro file sys/trigger#.g to be executed. Polling for further trigger conditions is suspended until the trigger macro file has been completed. RepRapFirmware does not wait for all queued moves to be completed before executing the macro, so you may wish to use the M400 command at the start of your macro file. If several triggers are pending, the one with the lowest trigger number takes priority.
* When M581 is executed, if the T parameter is present but the other parameters are omitted, the trigger inputs and edge polarities for that trigger number are reported. Otherwise, the specified inputs and their polarities are added to the conditions that cause that trigger.
* A maximum of 32 triggers can be configured on Duet 3, a maximum of 16 on Duet 2.
* Warning: if executed during a job, and more than one line long the GCode within the trigger file may be executed between later commands from the job. Bounding the trigger file with M25 and M24 may help, but this will cause warnings if the trigger happens outside of a job. The use of M25/M24 will cause the execution of pause and resume system macros.

## Tabs {.tabset}

### Duet 3

### Duet 2

Pin 44 forces a reset when shorted to ground so that button was easy enough. For the rest, I had to make changes to the config file. I found the following digging around RepRap.org:

In order to get buttons for pause, home, ATX on, and disable steppers, the following were added to config.g:

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