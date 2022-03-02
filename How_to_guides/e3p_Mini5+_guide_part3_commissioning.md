---
title: Duet 3 Mini 5+ Guide Part 3: Commissioning
description: Ender 3 Pro and Duet 3 Mini 5+ Guide Part 3:  Commissioning
published: false
date: 2022-03-02T08:16:30.988Z
tags: 
editor: markdown
dateCreated: 2022-02-11T18:26:23.148Z
---

![A Duet 3 Mini5+ Ethernet on the Creality Ender 3 Pro](/guides/mini5p/mini5p_guide1_intro.jpg)
# Introduction

In the previous two guides, we connected all the wiring, and prepared the SD card with all the configuration files needed and connected to the printer.

This guide will cover the first steps you should take to get connected to the Duet Web Control and to safely verify that your hardware is connected and configured correctly.

This will test motor movement, endstop triggering, heater function, fan control, homing behaviour, and the LCD.

Though this guide is specific to the Ender 3, the procedures shown here are the same for any printer during first commissioning.

## Other guides in this series

* Part 1 - Wiring
* Part 2 - Configuration
* Part 3 - Commissioning
* Part 4 - Calibration
* Part 5 - Single Board Computer

# First Boot

Now that we've copied the config files from the configurator, the web control interface files, menu system files, and firmware files, we are ready to power on the Duet Mini for the first time.

When you’re finished copying all the files and folders, safely eject the SD card from your computer system, and place the SD card into the Mini5+.

A micro USB cable capable of both data and power is now required to connect the board to your computer.

The next step is to connect to the board via USB and terminal software like YAT, Printrun/Pronterface, Cutecom, Serial Tools, etc.

[See here for a detailed step by step tutorial on getting connected.](/How_to_guides/Getting_connected/Getting_connected_to_your_Duet)

# Connect to the Duet Web Control

If you set a static IP address for the Duet (in the [networking section](/How_to_guides/e3p_Mini5+_guide_part2_configuration#config-tool-network) of the previous guide) the use it here.

Otherwise following the "[Connect Duet to Network](How_to_guides/Getting_connected/Getting_connected_to_your_Duet#h-5-connect-duet-to-network)" section of the general getting connected guide to get the dynamic IP address assigned to your Duet 3 mini 5+ from the network. 

![](/guides/getting_connected/05_network_07.jpg =500x)

Use that IP address and connect to Duet Web Control (DWC).
![](/guides/getting_connected/06_dwc_01.jpg =500x)

You can familiarize yourself with the full Duet Web Control functionality in the [DWC Manual](/User_manual/Reference/Duet_Web_Control_Manual).

# Verify Firmware is Updated

Go to the Settings. Machine Specific and General page on the left side bar. Compare the version numbers shown to the versions we copied to the SD card in the previous guide.

## Updating Firmware

If they are not the same the follow the [updating firmware instructions](/User_manual/RepRapFirmware/Updating_firmware).

# Testing End Stop Switches

Next, we will verify that the end stops are triggering properly.

Go to the Settings > Machine Specific > Endstops.

X, Y, and Z end stops should say "No"

Manually trigger the end stops with your finger, you should see the value change to "Yes" for each one you press.

If the values do not change, power down the printer and double check your wiring connections.

If the values for X, Y, and Z show "Yes" by default, and "No" when triggered, you chose the wrong switch type in the configurator. The easiest wya to change this is going to config.g, find the linse that start with [M574](/User_manual/Reference/Gcodes/M574) and either add or remove the ! mark before the pin name in the P parameter

# Verify Motor Direction

# Testing Homing Behaviour

# Testing the Hot End Heater

# Testing the Bed Heater

# Testing the 12864 LCD

# Tuning Fans

# Calibration

This concludes the commissioning stage of the guide. The next guide will cover initial calibration.

## Calibrate CPU Temp Reading

The CPU on the Duet has an internal temperature sensor, which can be very useful for gauging the temperature of the electronics case and the stepper drivers. However, before it can provide a useful result, it must be calibrated.

- Following the procedure documented here: Calibrating the MCU temperature
- Start with your printer at room temperature. As in, powered off for many hours.
- Turn on the printer, and as quickly as possible read the MCU temperature from the Machine Status box at the top left of the screen. We will call this temperature Tc.
- Now read the temperature of the hot end and bed. This should be very close to room temperature. Alternatively, if you have a thermometer in the room, you can use that value. We will call this Tr.
- To calculate the offset value, use (Tr-Tc). This value may be positive or negative. We will use this resulting value in the following command.
- In the Gcode Console, send M912 P0 S#, where # is the offset value obtained by (Tr-Tc). After a few seconds the CPU temperature should then read about the same as room temperature.
- To make the calibration permanent, you must add that M912 P0 S# command to config.g. It doesn't matter where, but I put it near the end, before the M501 command and the T0 command.
