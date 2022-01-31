---
title: Wiring your Duet 3 mainboard
description: This guide covers connecting hardware, such as power, heaters, motors, endstops, fans, temperature sensors etc., to your Duet 3 mainboard.
published: true
date: 2022-01-31T12:27:10.363Z
tags: 
editor: markdown
dateCreated: 2022-01-31T12:27:10.363Z
---

# Introduction

**This document is relevant to:** Duet 3 MB6HC and Mini 5+.
**Firmware versions:** All versions
**Difficulty:** Moderate
**Time Required:** 30 minutes - 1 hour

This guide covers connecting hardware, such as power, heaters, motors, endstops, fans, temperature sensors etc., to your Duet 3 mainboard.

If you are setting up your Duet 3 with a connected **Single Board Computer** (SBC, eg Raspberry Pi), please see [SBC setup for Duet 3](https://duet3d.dozuki.com/Wiki/SBC_Setup_for_Duet_3) documentation for connecting the SBC.

**Note:** The pictures in this guide are from an earlier version of the Duet 2 WiFi/Ethernet however the wiring guide steps are still valid. Images relevant for other boards will be added as/when available.

If you have any problems with your Duet when using this guide, rather than posting comments, **please use our support forum: [https://forum.duet3d.com/](https://forum.duet3d.com/)**


# 1. Tools required

To complete the wiring of your Duet, you will usually need:
* screw drivers
* wire strippers
* crimping tool(s) (see tabs below)
* pliers

Here is a good guide to crimping: [Teaching Tech on YouTube](https://youtu.be/GZOh1NzqzzU)

## Crimping power supply and high current connectors

Duet 3 mainboards have a barrier strip with screw terminals to connect power supply wires, and are supplied with insulated spade crimps.
* **Duet 3 MB6HC**: uses a 6-way barrier strip for Power in, OUT0 power in, OUT0 (high current output, eg heated bed, max 15A, can be different voltage than main power in.)
* **Duet 3 Mini 5+**: uses a 4-way barrier strip for Power in, OUT0 (high current output, eg heated bed, max 15A).

Standard red/blue/yellow automotive crimp tools will crimp these terminals. There are ratchet tools to crimp these as well as low-cost non-ratchet tools; we find ratchet wire crimping tools produce better crimps than the non-ratcheting type.

## Crimping medium current connectors

Duet 3 mainboards use JST VH series connectors for medium current outputs:
* **Duet 3 MB6HC**: used for the 4-wire motor outputs (Driver 0 to 5) and OUT1, OUT2 and OUT3
* **Duet 3 Mini 5+**: used for OUT1 and OUT2

You will need a suitable crimping tool for the crimp pins, for example Engineer PA21 (use the 2.2mm jaw opening to crimp the bare wire and the 2.5mm on to crimp the insulation). Alternatively you can solder the wire to the crimp pin.

## Crimping low current connectors

Molex KK-compatible connectors are used for all other inputs and outputs on the Duet. Use a ratchet crimping tool, eg HT-225D ratchet crimper, or manual tool such as Engineer PA09 or PA20.

# 2. Board Diagram

* Take a moment to familiarize yourself with the wiring diagram. It may help to open the high resolution image in another window, or print it out. Right-click on the image, and 'Open image in new tab'.

## Tabs {.tabset}

### Duet 3 Mini 5+

![duet_3_mini_5+_wiring_v0.5_v1.0_v1.01.png](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_wiring_v0.5_v1.0_v1.01.png =800x)

### Duet 3 MB6HC

![duet_3_mb6hc_wiring_diagram_v1.0_v1.01.png](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_wiring_diagram_v1.0_v1.01.png =800x)

# 3. A Note on Fire Safety

* The crimp tool shown in this photo is a basic and inexpensive tool available from any hardware store, electrical shop, etc.
* The method of crimp shown here is called an indent crimp. You should ensure that the tool you use provides a tight crimp to the ferrule.
* It is also best to use the include Molex connectors for all other connections as they ensure correct polarity and lock into place.

# 4. Communicating With the Duet Board

* Lets begin by ensuring there's a connection to the board. This can be over USB, wifi, or ethernet. Please refer to [Getting Connected to Your Duet](/How_to_guides/Getting_connected/Getting_connected_to_your_Duet) for details.
* We'll be using Duet Web Control during this guide, though you can use your favorite host software over USB if a network connection is unavailable.
* If using USB, connect a USB cable at this time. But be aware of the dangers of [USB ground loops](/User_manual/Overview/USB_ground_loops) if you have already connected power to the board.

# 4. Powering the Duet Board

* Connect your power supply to the Power In terminal.
* Be sure to observe correct polarity when making the connections. The picture is of a Duet XX WiFi/Ethernet; **check polarity**
* WARNING: It is HIGHLY recommended to use the included insulated spade crimps, by crimping them to the wires before putting the wires in the terminal block. Failure to do so could allow the wires to creep over time, become loose, and could possibly short circuit and start a fire.
* Do not tin (add solder to) these wires. The solder will flow over time, and will become loose in the screw terminal.
* Be sure not to twist the terminal block while tightening the screws. It can help to hold the terminal block while tightening.
* Check the screws after a few days/week of operation to ensure they are still snug.
* The gauge of wire should be appropriate to the current that the Duet will draw. This depends on a number of factors, however at its maximum this will be dominated by the 15A for the bed heater. The insulated spade crimps provided can take wires up to 1.5mm^2 (15 AWG).


# 5. Reset button and erase button/jumper

**Rewrite for Duet 3**

* We'll start at the reset button, and work our way around the wiring diagram clockwise.
* The reset button reboots the board, this is OK to press if a reboot is needed.
* The firmware is erased in one of two ways depending on the hardware PCB revision:
* For revisions v1.04 and later it is a jumper which is bridged to erase the firmware.
* For all versions prior to v1.04 it is through an erase switch.
* The erase jumper/button will erase the firmware that is stored on the main processor. The SD card does not hold the firmware, just some configuration files.
* Do not press the erase button unless you know what you are doing. There firmware does not need to be erased for normal upgrading. The process of reinstalling the firmware after it is erased is detailed In fallback procedure #3.

# 6. LED Indicators

**Rewrite for Duet 3**

* These indicators show the status of 3.3v power, 5v power, the Vin (power from your power supply) and the two extruder heaters.
* When the board is idle and connected to a power supply, expect the 3.3v, 5v, and Vin LEDs to be illuminated.
* When the board is powered only through an external 5v supply or through USB, expect only the 5v and 3.3v lights to be on.
* In this area you will also find indicators to show when an extruder heater is turned on.

# 7. Endstops

* Endstops tell the printer when the travel limit has been reached on a particular axis. The Duet's connections are a little different than RAMPS style boards, so please review this step and your endstops carefully.

**Rewrite for Duet 3**

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

**Rewrite for Duet 3**

* Your hotend heaters should be connected here. From left to right, the connectors are E1 and E0.
* Polarity does not matter for hotend heaters.
* WARNING: It is HIGHLY recommended to use the included ferrules, by crimping them to the wires before putting the wires in the terminal block. Failure to do so could allow the wires to creep over time, become loose, and could possibly short circuit and start a fire.
* Do not tin (add solder to) these wires.
* Be sure not to twist the terminal block while tightening the screws. It can help to hold the terminal block while tightening.
* Check the screws after a few days/week of operation to ensure they are still snug.

# 10. Motors

* The Duet uses internal stepper drivers. In the event that you desire external drivers, consult the wiki.
* Is your stepper motor spinning in the wrong direction? We will cover reversing the direction in firmware later on in the configuration guide.

**Rewrite for Duet 3**

* If you have two Z-Axis stepper motors, connect them to ZA and ZB.
* If you only have one Z-Axis stepper motor, plug it into the ZA connector and be sure that the supplied jumpers are installed on the ZB connector.
* ZA and ZB are wired in series; without a second stepper motor or the jumpers in place on ZB, ZA will not function.
* If you use more than five stepper motors, you may use a DueX2 or DueX5 expansion board which offers 2 or 5 extra stepper motor outputs, respectively.

# 11. External 5V Input & power supply control

**Rewrite for Duet 3**

* EXT_5V is an optional 5v power input and is not required for operation.
* The Duet has an on-board 5v regulator that gets its source from the 12-24v input.
* If you wish to provide external 5v power, or control an external ATX power supply, you may read more about it in the wiki.
* Unless you plan to provide an external 5v source, you should at this time check that there is a jumper on "INT 5V EN" and NOT on "EXT 5V EN".
* INT 5V EN
* EXT 5V EN

# 12. Fans

**Rewrite for Duet 3**

* The Duet has three PWM controlled fan headers and two Always On fan headers.
* A PWM (Pulse Width Modulation) fan connection is for fans you wish to control the speed of, for example a print cooling fan.
* An always on fan is for something like an electronics fan - always on when the printer is on.
* Some fans are more compatible with PWM control than others. If you have trouble varying the speed of a fan, check the documentation for changing PWM frequency.
* The polarity of the fans is important - don't connect them backwards.

# 13. Bed Heater

**Rewrite for Duet 3**

* The Duet is able to power a heated bed, and is fused at 15A. This should be well above the requirement for most heated beds.
* If your heater has an integrated LED, then the polarity will matter as the LED will not light with reverse polarity. Otherwise, a heater's polarity doesn't matter.
* It is recommended to use the included ferrules, by crimping them to the wires before putting the wires in the terminal block.
* Do not tin (add solder to) these wires.
* Be sure not to twist the terminal block while tightening the screws. It can help to hold the terminal block while tightening.
* Check the screws after a few days/weeks of operation to ensure they are still snug.

# 14. Bed Thermistor

**Rewrite for Duet 3**

* Connect the bed thermistor to BED_TEMP.
* The polarity of a thermistor does not matter.

# 15. PANELDUE

Duet 3 MB6HC has none of these headers. Duet 3 Mini 5+ has a PANELDUE header.

* PANELDUE / CONN_SD is used to connect the SD card reader on a PanelDue touchscreen display to the Duet.
* If you are using a PanelDue , this connection is optional and does not effect the function of the touchscreen monitor itself.
* The CONN_LCD header is reserved for future development at this time and should not be used.

# 16. Expansion

**Rewrite for Duet 3**

* The expansion header is to connect the Duet 2 WiFi/Ethernet to a DueX2 or DueX5 expansion board, or an Expansion Breakout Board (EBoB).
* The DueX2 and DueX5 expansion is an available add-on which offers additional stepper motor drivers, heater outputs, fan headers, endstop inputs, additional thermistor inputs, support for additional Temperature Daughterboards, and more.
* The Expansion Breakout Board (EBoB) is an available add-on which offers connections for external stepper drivers and hobby servos.

# 17. Temperature Daughterboard

**Rewrite for Duet 3**

* A Temperature Daughterboard allows the Duet to connect with a thermocouple or PT100 temperature sensor, which send a different type of signal than a thermistor.
* Each daughterboard supports two additional temperature sensors of the same type, ie either 2x PT100 or 2x thermocouple.
* Duet 3 MB6HC, Duet 2 WiFi/Ethernet and Duet 2 Maestro boards supports up to two temperature daughterboards. Duet 3 Mini 5+ supports one temperature daughterboard. 
* A Temperature Daughterboard may be desired if, for example, you wish to print with materials which require greater than 290°C, which is the limit for a thermistor.
* If you plan to use PT100 temperature senesors, please read the wiki regarding the use of Temperature Daughterboards.

# 18. Z Probe

**Rewrite for Duet 3**


* The Duet supports a Z-probe via this dedicated connector. There are many different kinds of probe, and the wiring will be different for each one.
* See this section of the wiki, Choosing a Z probe, which will help you choose a suitable Z probe if you have not already.
* See this section of the wiki, Connecting a Z probe, which contains guidance for wiring your Z probe to the Duet.

# 19. Panel Due

**Rewrite for Duet 3**


* The PanelDue, an optional accessory sold separately, is a touchscreen display which gives a user the ability to control the Duet with an intuitive interface directly at the printer.
* The PanelDue can be connected in two ways:
* A 4 wire cable that contains power and serial signals. This has a maximum recommended length of 1 meter
* a 10 way flat cable which claos contains an external SD card signals with a maximum recommended length of 400mm
* The wiki has more information on the types and sizes of PanelDue available. Also on connecting a PanelDue.

# 21. Ethernet or WiFi Module

**Rewrite for Duet 3**

* The Ethernet or WiFi Module supports a connection over a web interface. It is responsible for a network connection as well as the web-based user interface.
* The WiFi module has a blue LED that flashes only when the firmware is being changed.
* The Ethernet module has two LEDs - Link, which is on when an ethernet connection is established, and Activity, which flashed whenever data is being actively transferred.

# 22. Hotend Thermistor(s)

**Rewrite for Duet 3**

* Connect your hotend thermistors here.
* The polarity of thermistors does not matter.
* A thermistor can read up to 290°C. If you wish to print at a higher temperature than this, you should upgrade to a thermocouple or PT100 temperature sensor (See step 16). A PT100 also provides a more consistent reading between multiple sensors and resists noise interference in 4-wire mode.

# 23. Wiring Complete!
* Congratulations! Your Duet is wired and is ready to configure. Continue to 3.) Configuration (General Cartesian) to configure your Duet!

