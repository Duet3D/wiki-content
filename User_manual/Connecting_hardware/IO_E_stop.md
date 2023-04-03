---
title: Connecting an Emergency Stop
description: 
published: true
date: 2022-06-15T11:56:36.944Z
tags: 
editor: markdown
dateCreated: 2021-10-26T14:19:50.302Z
---

![sensors_emergency_stop.jpg](/manual/sensors/sensors_emergency_stop.jpg =400x)
*Image from [www.flickr.com](https://www.flickr.com/photos/dumbledad/3225255407)*

# Introduction

Most 3D printers have very few sensors, and all sensors are fallible, so sometimes a 3D printer will start behaving in an uncontrolled way. Although the Duet Web Interface has an Emergency Stop button, it can be valuable to have a physical button attached to the printer. Traditionally robots use a big red mushroom so it's easy to hit even if, say, your arm is trapped in the robot. But there are several grades of "stop" one could accomplish.

# What the button should do

*Note that a true "emergency" stop should cut the power, external from any electronic and firmware control - the big red button illustrated for example*

## Cut all power

The most definite way to stop a robot is to cut all power to it. Yanking the plug out of the wall works, as does a circuit breaker that's easy to flip by hand, or a [Residual Current Device (RCD)](https://en.wikipedia.org/wiki/Residual-current_device) whose test button is easy to press. This will cut all power regardless of any component failure aboard the Duet or robot. Of course it requires a physical action to reset.

## Cut the 12/24V supply

Executing GCode [M112](/User_manual/Reference/Gcodes/M112) causes any moves in progress to be immediately terminated, then Duet shuts down. All motors and heaters are turned off. However, your PSU will continue to run.

If your power supply is controlled by the PS_ON pin of the Duet, executing M112 does NOT shut down the 12/24V circuit. If you want to turn off the PS_ON pin using an external trigger, consider writing a macro that turns it off via [M81](/User_manual/Reference/Gcodes/M81) before it calls M112; see "Making a button do what you want" below.

Note that turning off the PSU will make your "always-on" fans turn off even though your hot end may be hot, and it also leaves the head wherever it was when you stopped the machine. The former might produce a hot end jam, and the latter might heat the bed. Also, if your axes are smooth enough, turning off the stepper motors entirely may allow the machine to fall. On the other hand, one of the ways for the MOSFETs controlling the heaters to fail is for them to fail shorted, so that the heaters are on 100% regardless of what the MCU commands; shutting off the 12/24V supply will shut them off in spite of this.

## Just stop doing what you're doing

If the machine's misbehaviour is mild, you may want to stop what it's doing but not shut everything down. You can write your own G-code macro for what should happen in this case, for example, turn off all heaters and home the head. At the moment, there is no way to make this interrupt actions in progress (including "wait for heater to reach temperature").

# Making a button do what you want

The Duet firmware allows you to associate a state change on any endstop with a "trigger", a user-definable action, with the [M581](/User_manual/Reference/Gcodes/M581) G-code. Trigger 0 does an emergency stop (like M112), Trigger 1 pauses the print, and any higher number *n* executes the file trigger*n*.g. So you can arrange for a physical button to execute any command by connecting it to the pins of an unused endstop. 

Note that triggers only fire on a transition off -> on; you can use [M582](/User_manual/Reference/Gcodes/M582) to make the trigger fire if the button is already pressed. You can use [M574](/User_manual/Reference/Gcodes/M574) to select whether the button is normally open or closed.

The easiest way to implement an external reset/emergency stop, is to configure it as an External Trigger using [M581](/User_manual/Reference/Gcodes/M581).

# Connecting the button

**Duet 3:** Connect to any available IO connector, between 3.3V and IO_x.in.

**Duet 2:** You can use any free endstop connector on the Duet. If you don't have a DueX board connected to the expansion connector, you can use any of the five En_STOP pins (n = 2-6). They are labelled on the underside of the Duet.

# Firmware configuration

The forced emergency stop using the easiest method is setup inside your config.g file as follows.

## Tabs {.tabset}

### RepRapFirmware 3.x

RepRapFirmware 3.x requires a pin definition with [M950](/User_manual/Reference/Gcodes/M950) before setting the trigger with [M581](/User_manual/Reference/Gcodes/M581):

```
;Duet 3
M950 J1 C"io4.in"

;Duet 2
M950 J1 C"e1stop"

M581 P1 T0 S1 R0
```

The parameters:

* Pnn - Specify one or more input pin numbers that you created using M950 with the J parameter, or -1 to delete the trigger
* Tnn - Logical trigger number to associate the input(s) with, from zero up to a firmware-specific maximum
* Sn - Whether trigger occurs on an inactive-to-active edge of that input (S1, default), active-to-inactive edge (S0), or ignores that input (S-1). By default, all triggers ignore all inputs.
* Rn - Condition: whether to trigger at any time (R0, default), only when printing a file from SD card (R1), or only when not printing a file from SD card (R2, supported in RRF 3.2 and later). R-1 temporarily disables the trigger.
* X, Y, Z or any other axis letter - axis or axes whose endstop switches are to cause the trigger

### RepRapFirmware 2.x

```
M581 T0 E1 S1 C1
```

The parameters:

* T0 = emergency stop on trigger; T1 = pause print; T{N} = runs the macro "sys/trigger{N}.g" (useful if you want to do things in a specific order, before pausing or sending the emergency stop).
* X, Y, Z, E0, E1 = select the endstop pin to monitor for trigger; typically one would use E0 or E1.
* S1 or S0 = sets it as raising edge (S1) or falling edge (S0). Set this based on your switch type (NO or NC); typically one would use a NC switch to GND as this will cause a trigger if the switch is removed (providing a safe fallback).
* C0 or C1 = when it may trigger: C0 is anytime; C1 is only during a print.

## Using the switch at startup

If the switch is reliable and works as expected, you can add a check for an external trigger with [M582](/User_manual/Reference/Gcodes/M582) inside your config.g - useful if you need the button to be reset before the Duet can restart (useful if using the PS_ON features).

## Software resets

Pressing the "Emergency Stop" switch within Duet Web Control or sending M112 also reset the board. this can be useful however are not a direct substitute for a hardwired emergency stop that cuts the power.