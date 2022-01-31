---
title: Wiring your Duet 2 mainboard
description: This guide covers connecting hardware, such as power, heaters, motors, endstops, fans, temperature sensors etc., to your Duet 2 mainboard.
published: true
date: 2022-01-31T15:15:10.175Z
tags: 
editor: markdown
dateCreated: 2022-01-28T15:53:00.844Z
---

# Introduction

**This document is relevant to:** Duet 2 WiFi / Ethernet and Duet 2 Maestro
**Firmware versions:** All versions
**Difficulty:** Moderate
**Time Required:** 30 minutes - 1 hour

This guide covers connecting hardware, such as power, heaters, motors, endstops, fans, temperature sensors etc., to your Duet 2 mainboard.

**Note:** The pictures in this guide are from an earlier version of the Duet 2 WiFi/Ethernet however the wiring guide steps are still valid. Images relevant for other boards will be added as/when available.

If you have any problems with your Duet when using this guide, rather than posting comments, **please use our support forum: [https://forum.duet3d.com/](https://forum.duet3d.com/)**


# 1. Tools required

To complete the wiring of your Duet, you will usually need:
* screw drivers
* wire strippers
* crimping tool(s) (see tabs below)
* pliers

Here is a good guide to crimping: [Teaching Tech on YouTube](https://youtu.be/GZOh1NzqzzU)

**Crimping power supply and high current connectors**

* **Duet 2 Wifi and Ethernet** uses bootlace ferrules on the end of the power supply and heated bed wires, which connect to the screw terminal on the Duet board. Use an appropriate bootlace ferrule crimping tool to crimp the wires in the bootlace ferrule. Tools are available that can crimp the ferrule into a C shape along it's length, or that crimp around the ferrule to make it into a square or hexagonal shape.
* **Duet 2 Maestro** has a 4-way barrier strip with screw terminals to connect power supply and heated bed wires, and are supplied with insulated spade crimp terminals. Use an appropriate crimping tool; we find ratchet wire crimping tools produce better crimps than the non-ratcheting type.

**Crimping medium current connectors**

Duet 2 boards use bootlace ferrules on the end of the medium current (usually hot end heater) wires, which connect to the screw terminal on the Duet board. Use an appropriate bootlace ferrule crimping tool to crimp the wires in the bootlace ferrule, as for the 

**Crimping low current connectors**

Molex KK-compatible connectors are used for all other inputs and outputs on the Duet. Use a ratchet crimping tool such as HT-225D ratchet crimper, or manual tool such as Engineer PA09 or PA20.


# 2. Board Diagram

* Take a moment to familiarize yourself with the wiring diagram. It may help to open the high resolution image in another window, or print it out. Right-click on the image, and 'Open image in new tab'.

## Tabs {.tabset}

### Duet 2 WiFi and Ethernet

![Diagram showing the Duet 2 board with the pins labelled to aid wiring.](/duet_boards/duet_2_wifi_ethernet_maestro/duet2ndgen_connections_v1.0-1.5_d2.1.png =800x)

### Duet 2 Maestro

![duet_2_maestro_v1.0_wiring.png](/duet_boards/duet_2_wifi_ethernet_maestro/duet_2_maestro_v1.0_wiring.png =800x)

# 3. A Note on Fire Safety
* WARNING: it is imperative that you use the provided crimped ferrule connectors for the power supply, hotend heaters, and bed heater. Failure to use these can result in wire creeping, causing the wires to come loose and can possibly create a short circuit, which could result in fire.
* The crimp tool shown in this photo is a basic and inexpensive tool available from any hardware store, electrical shop, etc.
* The method of crimp shown here is called an indent crimp. You should ensure that the tool you use provides a tight crimp to the ferrule.
* It is also best to use the include Molex connectors for all other connections as they ensure correct polarity and lock into place.

# 3. Powering the Duet Board

* Connect your power supply to the Power In terminal.
* Be sure to observe correct polarity when making the connections. The picture is of a Duet 2 WiFi/Ethernet; **check polarity** if wiring Duet 2 Maestro or Duex 2/5.
* WARNING: It is HIGHLY recommended to use the included ferrules, by crimping them to the wires before putting the wires in the terminal block. Failure to do so could allow the wires to creep over time, become loose, and could possibly short circuit and start a fire.
* Do not tin (add solder to) these wires. The solder will flow over time, and will become loose in the screw terminal.
* Be sure not to twist the terminal block while tightening the screws. It can help to hold the terminal block while tightening.
* Check the screws after a few days/week of operation to ensure they are still snug.
* The gauge of wire should be appropriate to the current that the Duet will draw. This depends on a number of factors, however at its maximum this will be dominated by the 15A for the bed heater. The red and black ferrules provided are 1.0mm^2 and 1.5mm^2 respectively. This relates to 17 and 15 AWG respectively.

# 4. Communicating With the Duet Board

* Lets begin by ensuring there's a connection to the board. This can be over USB, wifi, or ethernet. Please refer to [Getting Connected to Your Duet](/How_to_guides/Getting_connected/Getting_connected_to_your_Duet) for details.
* We'll be using Duet Web Control during this guide, though you can use your favorite host software over USB if a network connection is unavailable.
* If using USB, connect a USB cable at this time. But be aware of the dangers of [USB ground loops](/User_manual/Overview/USB_ground_loops).

# 5. Reset button and erase button/jumper

* We'll start at the reset button, and work our way around the wiring diagram clockwise.
* The reset button reboots the board, this is OK to press if a reboot is needed.
* The firmware is erased in one of two ways depending on the hardware PCB revision:
* For revisions v1.04 and later it is a jumper which is bridged to erase the firmware.
* For all versions prior to v1.04 it is through an erase switch.
* The erase jumper/button will erase the firmware that is stored on the main processor. The SD card does not hold the firmware, just some configuration files.
* Do not press the erase button unless you know what you are doing. There firmware does not need to be erased for normal upgrading. The process of reinstalling the firmware after it is erased is detailed In fallback procedure #3.

# 6. LED Indicators

* These indicators show the status of 3.3v power, 5v power, the Vin (power from your power supply) and the two extruder heaters.
* When the board is idle and connected to a power supply, expect the 3.3v, 5v, and Vin LEDs to be illuminated.
* When the board is powered only through an external 5v supply or through USB, expect only the 5v and 3.3v lights to be on.
* In this area you will also find indicators to show when an extruder heater is turned on.

# 7. Endstops

* Endstops tell the printer when the travel limit has been reached on a particular axis. The Duet's connections are a little different than RAMPS style boards, so please review this step and your endstops carefully.
* X Endstop
* Y Endstop
* Z Endstop
* From left to right on an endstop connector, the pins are signal (marked STP on the back of the board), +3.3v, and GND.
* If using 2 wire microswitch endstops, connect one wire to GND and the other wire to signal (STP), which are the outer two pins on the Duet connector.
* If using other endstops, please refer to this link for details.
* Never connect your endstop wires from +3.3v to ground. This will create a short circuit and could damage the Duet.

# 8. Checking Endstops

* It is important that you check that the Duet is receiving a signal from your endstops. Failure to do so could cause damage to your printer!
* You can see the status of your endstops a number of ways:

## Tabs {.tabset}

### Object model browser

TO DO

### Endstop plugin

TO DO

### M119

TO DO

# 9. Hotend Heaters

* Your hotend heaters should be connected here. From left to right, the connectors are E1 and E0.
* Polarity does not matter for hotend heaters.
* WARNING: It is HIGHLY recommended to use the included ferrules, by crimping them to the wires before putting the wires in the terminal block. Failure to do so could allow the wires to creep over time, become loose, and could possibly short circuit and start a fire.
* Do not tin (add solder to) these wires.
* Be sure not to twist the terminal block while tightening the screws. It can help to hold the terminal block while tightening.
* Check the screws after a few days/week of operation to ensure they are still snug.

# 10. Motors

* The Duet uses internal stepper drivers. In the event that you desire external drivers, consult the wiki.
* Is your stepper motor spinning in the wrong direction? We will cover reversing the direction in firmware later on in the configuration guide.
* If you have two Z-Axis stepper motors, connect them to ZA and ZB.
* If you only have one Z-Axis stepper motor, plug it into the ZA connector and be sure that the supplied jumpers are installed on the ZB connector.
* ZA and ZB are wired in series; without a second stepper motor or the jumpers in place on ZB, ZA will not function.
* If you use more than five stepper motors, you may use a DueX2 or DueX5 expansion board which offers 2 or 5 extra stepper motor outputs, respectively.

# 11. External 5V Input & power supply control

* EXT_5V is an optional 5v power input and is not required for operation.
* The Duet has an on-board 5v regulator that gets its source from the 12-24v input.
* If you wish to provide external 5v power, or control an external ATX power supply, you may read more about it in the wiki.
* Unless you plan to provide an external 5v source, you should at this time check that there is a jumper on "INT 5V EN" and NOT on "EXT 5V EN".
* INT 5V EN
* EXT 5V EN

# 12. Fans

* The Duet has three PWM controlled fan headers and two Always On fan headers.
* A PWM (Pulse Width Modulation) fan connection is for fans you wish to control the speed of, for example a print cooling fan.
* An always on fan is for something like an electronics fan - always on when the printer is on.
* Some fans are more compatible with PWM control than others. If you have trouble varying the speed of a fan, check the documentation for changing PWM frequency.
* The polarity of the fans is important - don't connect them backwards.

# 13. Bed Heater

* The Duet is able to power a heated bed, and is fused at 15A. This should be well above the requirement for most heated beds.
* If your heater has an integrated LED, then the polarity will matter as the LED will not light with reverse polarity. Otherwise, a heater's polarity doesn't matter.
* It is recommended to use the included ferrules, by crimping them to the wires before putting the wires in the terminal block.
* Do not tin (add solder to) these wires.
* Be sure not to twist the terminal block while tightening the screws. It can help to hold the terminal block while tightening.
* Check the screws after a few days/weeks of operation to ensure they are still snug.

# 14. Bed Thermistor

* Connect the bed thermistor to BED_TEMP.
* The polarity of a thermistor does not matter.

# 15. PANELDUE, CONN_SD and CONN_LCD

Duet 2 Maestro have a PANELDUE header. Duet 2 WiFi/Ethernet has CONN_SD and CONN_LCD.

* PANELDUE / CONN_SD is used to connect the SD card reader on a PanelDue touchscreen display to the Duet.
* If you are using a PanelDue , this connection is optional and does not effect the function of the touchscreen monitor itself.
* The CONN_LCD header is reserved for future development at this time and should not be used.

# 19. Panel Due

* The PanelDue, an optional accessory sold separately, is a touchscreen display which gives a user the ability to control the Duet with an intuitive interface directly at the printer.
* The PanelDue can be connected in two ways:
  * A 4 wire cable that contains power and serial signals. This has a maximum recommended length of 1 meter
  * a 10 way flat cable which claos contains an external SD card signals with a maximum recommended length of 400mm
* The wiki has more information on the types and sizes of PanelDue available. Also on connecting a PanelDue.

# 16. Expansion

* The expansion header is to connect the Duet 2 WiFi/Ethernet to a DueX2 or DueX5 expansion board, or an Expansion Breakout Board (EBoB).
* The DueX2 and DueX5 expansion is an available add-on which offers additional stepper motor drivers, heater outputs, fan headers, endstop inputs, additional thermistor inputs, support for additional Temperature Daughterboards, and more.
* The Expansion Breakout Board (EBoB) is an available add-on which offers connections for external stepper drivers and hobby servos.

# 17. Temperature Daughterboard

* A Temperature Daughterboard allows the Duet to connect with a thermocouple or PT100 temperature sensor, which send a different type of signal than a thermistor.
* Each daughterboard supports two additional temperature sensors of the same type, ie either 2x PT100 or 2x thermocouple.
* Duet 3 MB6HC, Duet 2 WiFi/Ethernet and Duet 2 Maestro boards supports up to two temperature daughterboards. Duet 3 Mini 5+ supports one temperature daughterboard. 
* A Temperature Daughterboard may be desired if, for example, you wish to print with materials which require greater than 290°C, which is the limit for a thermistor.
* If you plan to use PT100 temperature senesors, please read the wiki regarding the use of Temperature Daughterboards.

# 18. Z Probe

* The Duet supports a Z-probe via this dedicated connector. There are many different kinds of probe, and the wiring will be different for each one.
* See this section of the wiki, Choosing a Z probe, which will help you choose a suitable Z probe if you have not already.
* See this section of the wiki, Connecting a Z probe, which contains guidance for wiring your Z probe to the Duet.

# 21. Ethernet or WiFi Module

* The Ethernet or WiFi Module supports a connection over a web interface. It is responsible for a network connection as well as the web-based user interface.
* The WiFi module has a blue LED that flashes only when the firmware is being changed.
* The Ethernet module has two LEDs - Link, which is on when an ethernet connection is established, and Activity, which flashed whenever data is being actively transferred.

# 22. Hotend Thermistor(s)

* Connect your hotend thermistors here.
* The polarity of thermistors does not matter.
* A thermistor can read up to 290°C. If you wish to print at a higher temperature than this, you should upgrade to a thermocouple or PT100 temperature sensor (See step 16). A PT100 also provides a more consistent reading between multiple sensors and resists noise interference in 4-wire mode.

# 23. Wiring Complete!
* Congratulations! Your Duet is wired and is ready to configure. Continue to 3.) Configuration (General Cartesian) to configure your Duet!

