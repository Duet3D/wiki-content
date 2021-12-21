---
title: Connecting and configuring a chamber heater
description: 
published: true
date: 2021-09-22T13:34:10.752Z
tags: 
editor: markdown
dateCreated: 2021-09-22T13:26:27.130Z
---

# Introduction

RepRapFirmware supports a chamber heater, but by default no chamber heater is configured. To use a chamber heater, you will need a spare heater channel, and either the thermistor channel associated with that heater or another temperature sensing channel.

# Connecting the chamber heater

Chamber heaters are typically AC mains powered, so you will need a Solid State Relay (SSR) to drive one. Use a zero-crossing DC/AC SSR such as Fotek SSR-25DA + safety cover, or Crydom D2425 + safety cover KS101. If your chamber heater draws more than about 5A then the SSR may need a heatsink.

If you have a chamber heater that runs on the same voltage as your Duet, you can connect it to one of the extruder heater outputs (5A maximum), or to the heated bed output (15A maximum).

### Connecting a SSR to a heater output on the Duet

SSRs generally accept anywhere between 3V and 32V input voltage, so you can connect the SSR to the heater terminal block, or any other PWM pin that has sufficient voltage and current to turn the SSR on and off. Make sure you connect the + and - terminals the right way round. The + terminal on the SSR goes to the heater terminal marked + or VIN, and the - terminal pin the SSR goes to the heater terminal marked -.

Connect the thermistor that measures chamber temperature to the associated thermistor input.

### Connecting a SSR directly to the Duet 2 expansion connector

If you have a Duet 2 WiFi/Ethernet without an expansion board and no spare heater channels, you can connect the input of the SSR directly to the expansion connector. The heater outputs of the Duet are active low, so connect the SSR - input terminal to the appropriate expansion connector pin, and connect the SSR + input terminal to +3.3V (pin 3 on the expansion connector).

You will also need a thermistor input channel to monitor the chamber temperature. If you are using a PT100 or thermocouple sensor for a hot end, then you can use the thermistor input pins that would otherwise be used for that hot end.

# Configuring the chamber heater

# Tabs {.tabset}

## RepRapFirmware 3.x and later

Add the following commands to your config.g.
- Define the thermistor that will control the chamber heater with [M308](/User_manual/Reference/Gcodes/M308). 
- Define the heater, or SSR that controls the heater, with [M950](/User_manual/Reference/Gcodes/M950). You can set the PWM frequency with the Q parameter, usually 10Hz for a chamber heater. 
- Set the heater parameters with [M307](/User_manual/Reference/Gcodes/M307). 
- Once connected, tune the chamber heater with [M303](/User_manual/Reference/Gcodes/M303), and update the [M307](/User_manual/Reference/Gcodes/M307) parameters with the new settings. 
- Use [M141](/User_manual/Reference/Gcodes/M141) to define this heater as a chamber heater. 
- Set a temperature limit for chamber heater with [M143](/User_manual/Reference/Gcodes/M143). 

Example:

### Tabs {.tabset}

#### Duet 3

```
; Heaters
M308 S2 P"temp2" Y"thermistor" T100000 B3950 ; configure sensor 2 as thermistor on pin temp2
M950 H2 C"out2" T2                           ; create chamber heater output on out2 and map it to sensor 2
M307 H2 B0 S1.00                             ; disable bang-bang mode for the chamber heater and set PWM limit
M141 H2                                      ; map chamber to heater 2
M143 H2 S60                                  ; set temperature limit for heater 2 to 60C
```

#### Duet 2

```
M308 S2 P"e1temp" Y"thermistor" T100000 B3950  ; configure sensor 2 as thermistor on pin e1temp
M950 H2 C"e1heat" T2                           ; create chamber heater output on e1heat and map it to sensor 2
M307 H2 B0 S1.00                               ; disable bang-bang mode for the chamber heater and set PWM limit
M141 H2                                        ; map chamber to heater 2
M143 H2 S100                                   ; set temperature limit for heater 2 to 100C
```

## RepRapFirmware 2.x and earlier

Add the following commands to your config.g.
- [M141](/User_manual/Reference/Gcodes/M141) tells the firmware that you have a chamber heater and which heater channel it uses
- [M305](/User_manual/Reference/Gcodes/M305) configures the temperature sensor
- [M301](/User_manual/Reference/Gcodes/M301) command to configure the PID parameters. 

Example:

```
M141 H3                     ; heater 3 is the chamber heater
M305 P3 R4700 T100000 B3950 ; heater 3 is monitored by a 100K thermistor with B=3950 and a 4.7K series resistor
M301 H3 B1                  ; use bang-bang control for the chamber heater
```

To use a different thermistor channel form the heater channel, add a suitable X parameter to the [M305](/User_manual/Reference/Gcodes/M305) command. For example, X1 would tell the firmware to use the thermistor channel associated with heater 1 (which is normally the first extruder heater).

You can use PID control of the chamber heater if you wish. RepRapFirmware uses low frequency (10Hz) PWM on the chamber heater channel so as to be compatible with most SSRs.

# Using the chamber heater

Use [M141](/User_manual/Reference/Gcodes/M141) and [M116](/User_manual/Reference/Gcodes/M116) to control the chamber heater. 

Add the command ```M141 S###``` to your slicer start gcode, where ### is the required chamber temperature at the start. Also make sure there is a ```M116``` command somewhere after the M141 command, to wait for all temperatures to reach their assigned values.

In your end gcode, either use the command M0 (which normally switches all heaters off), or the command M141 S0 to turn the chamber heater off.