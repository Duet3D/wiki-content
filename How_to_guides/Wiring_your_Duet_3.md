---
title: Wiring your Duet 3 mainboard
description: This guide covers connecting hardware, such as power, heaters, motors, endstops, fans, temperature sensors etc., to your Duet 3 mainboard.
published: false
date: 2022-05-17T11:54:35.683Z
tags: 
editor: markdown
dateCreated: 2022-01-31T12:27:10.363Z
---

# Introduction

## **This page is in draft**


**This document is relevant to:** Duet 3 MB6HC, MB6XD and Mini 5+.
**Firmware versions:** All versions
**Difficulty:** Moderate
**Time Required:** 30 minutes - 1 hour

This guide covers connecting hardware, such as power, heaters, motors, endstops, fans, temperature sensors etc., to your Duet 3 mainboard.

If you are setting up your Duet 3 with a connected **Single Board Computer** (SBC, eg Raspberry Pi), please see [SBC setup for Duet 3](/User_manual/Machine_configuration/SBC_setup){target=_blank} documentation for connecting the SBC.

If you have any problems with your Duet when using this guide, rather than posting comments, **please use our support forum: [https://forum.duet3d.com/](https://forum.duet3d.com/){target=_blank}**

# 1. Board Diagram

* Take a moment to familiarize yourself with the wiring diagram. It may help to open the high resolution image in another window, or print it out; just click on the image to do this.

## Tabs {.tabset}

### Duet 3 Mini 5+

[![duet_3_mini_5+_wiring_v0.5_v1.0_v1.01.png](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_wiring_v0.5_v1.0_v1.01.png =800x)](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_wiring_v0.5_v1.0_v1.01.png){target=_blank}

### Duet 3 MB6HC

[![duet_3_mb6hc_wiring_diagram_v1.0_v1.01.png](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_wiring_diagram_v1.0_v1.01.png =800x)](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_wiring_diagram_v1.0_v1.01.png){target=_blank}

### Duet 3 MB6XD

duet_boards/duet_3_mb6xd/duet3_mb_6xd_v0.1_wiring.png

[![duet3_mb_6xd_v0.1_wiring.png](/duet_boards/duet_3_mb6xd/duet3_mb_6xd_v0.1_wiring.png =800x)](/duet_boards/duet_3_mb6xd/duet3_mb_6xd_v0.1_wiring.png){target=_blank}

# 2. Fire safety

> **WARNING:** it is HIGHLY recommended that you use the provided crimped connectors for the power supply, bed heater and hotend heaters.
{.is-warning}

* Failure to use these can result in wire creeping, causing the wires to come loose in the screw terminal and possibly create a hot spot or short circuit, which could result in fire. 
* Using bare wires may lead to stray wire strands causing short circuits, which could result in fire. 
* Do not tin (add solder to) these wires. The solder will flow over time, and will become loose in the screw terminal and possibly create a hot spot or short circuit, which could result in fire.
* The crimp tools shown above are generally available from any hardware store, electrical shop, online etc., and when used correctly will give good quality, solid connections.
* The method of crimp used is called an indent crimp. You should ensure that the tool you use provides a tight crimp of the ferrule to the wire.
* It is also best to use the included Molex connectors for all other connections as they ensure correct polarity and lock into place.


# 3. Tools required

To complete the wiring of your Duet, you will usually need:
* screw drivers
* wire strippers
* crimping tool(s) (see tabs below)
* pliers

## Crimping guide

Here is a good guide to crimping: [Teaching Tech on YouTube](https://youtu.be/GZOh1NzqzzU){target=_blank}

## Tabs {.tabset}

### Power supply and high current connectors

![wiring_crimp_tool_manual.jpg](/guides/wiring/wiring_crimp_tool_manual.jpg =50%x){.align-right}![wiring_crimp_tool_ratchet.jpg](/guides/wiring/wiring_crimp_tool_ratchet.jpg =50%x){.align-right}Duet 3 mainboards have a barrier strip with screw terminals to connect power supply wires, and are supplied with insulated spade crimps.
* **Duet 3 MB6HC**: uses a 6-way barrier strip for Power in, OUT0 power in, OUT0 (high current output, eg heated bed, max 15A, can be different voltage than main power in.)
* **Duet 3 Mini 5+**: uses a 4-way barrier strip for Power in, OUT0 (high current output, eg heated bed, max 15A).
* **Duet 3 MB6XD**: uses a 2-way barrier strip for Power in.

Standard red/blue/yellow automotive crimp tools will crimp these terminals. There are ratchet tools to crimp these as well as low-cost non-ratchet tools; we find ratchet wire crimping tools produce better crimps than the non-ratcheting type.

<p style="clear:both"></p>

### Medium current connectors

![wiring_engineer_pa-21.jpg](/guides/wiring/wiring_engineer_pa-21.jpg =50%x){.align-right}![wiring_ht225d.jpg](/guides/wiring/wiring_ht225d.jpg =50%x){.align-right}Duet 3 mainboards use JST-VH series connectors for medium current outputs:
* **Duet 3 MB6HC**: used for the 4-wire motor outputs (Driver 0 to 5) and OUT1, OUT2 and OUT3
* **Duet 3 Mini 5+**: used for OUT1 and OUT2
* **Duet 3 MB6XD**: used for OUT0, OUT1 and OUT2

JST-VH crimps require a minimum of 22AWG wire (20AWG or 0.5mm2 recommended. Most NEMA17 size stepper motor wire will will not be thick enough to use in the normal way, but you can double the stripped part of the wire back on itself to bulk it up, and put a small length of heatshrink sleeving over the insulation to bulk up the insulation. 

You will need a suitable crimping tool for the crimp pins, for example Engineer PA21. The PA21 is designed to handle the longer flanges of the VH crimps that grip the insulation. Use the 2.2mm jaw opening to crimp the bare wire and the 2.5mm on to crimp the insulation. The large side of a ratchet crimping tool, such as the HT-225D, may also be able to crimp the VH series. Alternatively you can solder the wire to the crimp pin.

<p style="clear:both"></p>

### Low current connectors

![wiring_engineer_pa-09.jpg](/guides/wiring/wiring_engineer_pa-09.jpg =50%x){.align-right}![wiring_ht225d.jpg](/guides/wiring/wiring_ht225d.jpg =50%x){.align-right}Molex KK-compatible connectors are used for all other inputs and outputs on Duet mainboards. Use a manual tool such as Engineer PA-09/PA-20/PA-21 (use the 1.6mm opening for the bare wire, and 1.9mm opening for the insulation), or ratchet crimping tool, eg HT-225D ratchet crimper.

<p style="clear:both"></p>

### Duet 3 expansion boards

![wiring_engineer_pa-09.jpg](/guides/wiring/wiring_engineer_pa-09.jpg =50%x){.align-right}Most Duet 3 expansion boards use the same connectors as the main boards. The exception to this is the Duet 3 Toolboard 1LC, which uses JST-PH and JST-ZH connectors to keep the size of the board as small as possible.

The JST-PH connectors can be crimped using a manual tool such as Engineer PA-09, or pre-crimped connectors are available 

The JST-ZH connectors (used for CAN and 1LC v1.0 headers) are difficult to crimp because they are so small, so Duet3D supply pre-terminated connectors. You can connect longer wires to them by hand soldering or using small size (white) solder sleeves. They can be manually crimped with Engineer PA-09, good eyesight and a steady hand. 

<p style="clear:both"></p>

# 4. Communicating with the Duet board

* You should begin by checking the Duet board and get it connected to your network. Please refer to [Getting Connected to Your Duet](/How_to_guides/Getting_connected/Getting_connected_to_your_Duet) for details.
* Once you have established communication, unplug the USB lead.
* **All wiring should be done with NO POWER to the Duet board!**

# 5. Powering the Duet board

[![wiring_d3_power_02.png](/guides/wiringd3/wiring_d3_power_02.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_power_02.png){target=_blank}[![wiring_d3_power_01.png](/guides/wiringd3/wiring_d3_power_01.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_power_01.png){target=_blank}Connect your power supply wires to the Power In screw terminals of the barrier strip.
* Be sure to observe correct polarity when making the connections.
* **WARNING:** It is HIGHLY recommended to use the included fork terminals, by crimping them to the wires before putting the wires in the terminal block. Failure to do so could allow the wires to creep over time, become loose, and could possibly short circuit and start a fire.
* Do not tin (add solder to) these wires. The solder will flow over time, and will become loose in the screw terminal.
* Be sure not to twist the barrier strip while tightening the screws. It can help to hold the barrier strip while tightening.
* Check the screws after a few days/week of operation to ensure they are still snug.
* The gauge of wire should be appropriate to the current that the Duet will draw. This depends on a number of factors, however at its maximum this will be dominated by the 15A for the bed heater. The fork terminals supplied with Duet 3 boards are suitable for wire 1.5mm² to 2.5mm², 16 AWG to 14 AWG.
* Once connected, make sure USB is disconnected (no power to board). Test the power by turning it on. You should see the LEDs on the edge of the board light up, with the 3.3v, 5v, and Vin LEDs illuminated.
* **TURN OFF THE POWER SUPPLY**. All wiring should be done with no power to the Duet board.
* For more details, see [User manual: Choosing the power supply](/User_manual/Connecting_hardware/Power_choosing) and [User manual: Power wiring](/User_manual/Connecting_hardware/Power_wiring)

# 6 Reset button and erase jumper 

### Reset button

[![wiring_d3_reset_01.png](/guides/wiringd3/wiring_d3_reset_01.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_reset_01.png){target=_blank}[![wiring_d3_reset_02.png](/guides/wiringd3/wiring_d3_reset_02.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_reset_02.png){target=_blank}The reset button reboots the board. Press if a reboot is needed. 

### Erase jumper

[![wiring_d3_erase_02.png](/guides/wiringd3/wiring_d3_erase_02.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_erase_02.png){target=_blank}[![wiring_d3_erase_01.png](/guides/wiringd3/wiring_d3_erase_01.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_erase_01.png){target=_blank}Putting a jumper on the erase pins will erase the firmware on the Duet board. Do not erase the firmware unless you know what you are doing. The firmware does not need to be erased for normal updating. The process of reinstalling the firmware after it is erased is detailed here: [User manual: Updating firmware](/User_manual/RepRapFirmware/Updating_firmware#fallback-procedure-2).

**MB6HC, MB6XD**:
  * Bridge the 'Erase' pins with a jumper, to erase the firmware.
  * The erase jumper will erase the firmware that is stored on the main processor. The SD card does not hold the firmware, just some configuration files.

**Mini 5+**:
  * There is no jumper or button to erase the firmware. However, a double press on the reset button will put the board into UF2 bootloader upload mode. See [User manual: Updating firmware - Duet 3 Mini 5+ via USB](/User_manual/RepRapFirmware/Updating_firmware#duet-3-mini-5-wifiethernet)

# 7. LED Indicators

[![wiring_d3_led_02.png](/guides/wiringd3/wiring_d3_led_02.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_led_02.png){target=_blank}[![wiring_d3_led_01.png](/guides/wiringd3/wiring_d3_led_01.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_led_01.png){target=_blank}On the edge of the board around the reset button are a number of LEDs. 
* These indicators show the status of 3.3V power, 5V power, 12V power and Vin (power from your power supply).
* When the board is idle and connected to a power supply, expect the 3.3v, 5v, 12v and Vin LEDs to be illuminated, and the STATUS/DIAG LED to be flashing steadily.
* When the board is powered only through an external 5v supply or through USB, expect only the 5v and 3.3v lights to be on.
* There are also LED indicators on the board to show when each high current OUT_x outputs are turned on.

The LED indicators have the following function:

| Label | Colour | Function |
|--|--|--|
| **ACT** | Green | Indicates activity on the CAN-FD bus (Mini 5+, MB6XD) |
| **STATUS/DIAG** | Red | See description below |
| **V_FUSED** | Blue | Indicates fused VIN supply present |
| **12V+** | Amber | Indicates indicates on-board 12V regulator operating |
| **5V+** | Red | Indicates indicates 5V supply present |
| **3.3V+** | Green | Indicates on-board 3.3V regulator operating |
| **USB** | Red | Indicates presence of 5V power from USB (MB6HC) |
| **ESP** | Green | Indicates WiFi activity; flashing for searching/connecting, on for connected. (Mini 5+ WiFi version) |

The red LED labelled "STATUS" (Mini 5+, MB6XD) or "DIAG" (MB6HC) indicates the state of the board, as follows.

| LED | Meaning |
|:---|:---|
| Flashing steadily, about half a second off and half a second on | Normal operation, RepRapFirmware is running |
| Glowing dimly, or off | Firmware has been erased (MB6HC, MB6XD) |
| Pulsing from bright to dim and back again | USB bootloader activated (Mini 5+) |
| Flashing three times, then off for a while | Firmware CRC check failed |

# 8. Endstops
[![wiring_d3_endstops_02.png](/guides/wiringd3/wiring_d3_endstops_02.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_endstops_02.png){target=_blank}[![wiring_d3_endstops_01.png](/guides/wiringd3/wiring_d3_endstops_01.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_endstops_01.png){target=_blank}Endstops tell the printer when the travel limit has been reached on a particular axis. The Duet's connections may be different from other controller boards, so please review this step and your endstops carefully.
* You can connect simple microswitch endstops to any of the IO connectors.
* On an IO connector, the pins are +3.3V, signal in, GND, signal out and +5V.
* If using 2 wire microswitch endstops, connect one wire to GND and the other wire to signal in.
* Never connect your endstop wires from +3.3V or +5V to ground. This will create a short circuit and could damage the Duet.
* If using other endstops, please refer to [User manual: Connecting endstop switches](/User_manual/Connecting_hardware/Sensors_endstops) for details.

# 9. Hotend heaters

[![wiring_d3_heaters_02.png](/guides/wiringd3/wiring_d3_heaters_02.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_heaters_02.png){target=_blank}[![wiring_d3_heaters_01.png](/guides/wiringd3/wiring_d3_heaters_01.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_heaters_01.png){target=_blank}Your hotend heaters connect to the pin headers shown in the image, <span style="background-color:#FFFF00">outlined yellow</span>. 
* JST VH series connectors are used for medium current outputs:
  * **Duet 3 MB6HC**: used for OUT1, OUT2 and OUT3
  * **Duet 3 Mini 5+**: used for OUT1 and OUT2
  * **Duet 3 MB6XD**: used for OUT0, OUT1 and OUT2
* Crimp the heater wires to the supplied JST VH crimps, and insert them in the plastic shell, before plugging the heater into the pin headers on the board. See earlier on this page for crimping advice.
* Polarity does not matter for hotend heaters.
* For more details, see [User manual: Heaters overview](/User_manual/Connecting_hardware/Heaters_overview)

# 10. Stepper motors

[![wiring_d3_motors_02.png](/guides/wiringd3/wiring_d3_motors_02.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_motors_02.png){target=_blank}[![wiring_d3_motors_01.png](/guides/wiringd3/wiring_d3_motors_01.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_motors_01.png){target=_blank}Duet 3 boards have different options to connect stepper motors:
**Duet 3 MB6HC**: 6x JST VH 4-pin headers for stepper motors up to 6.3A peak current
**Duet 3 Mini 5+**: 5x Molex KK-compatible 4-pin headers for stepper motors up to 2A peak current
**Duet 3 MB6XD**: 6x Molex KK-compatible 6-pin headers for external stepper or servo motor driver connections
* Motor outputs are labelled DRIVER_0 to DRIVER_5 (MB6HC, MB6XD) or DRIVER_0 to DRIVER_4 (Mini 5+)
* **WARNING:** For MB6HC and Mini 5+, you MUST connect the two wires for one phase of the stepper motor to the two pins at one end of the connector, and the two wires for the other phase to the two pins at the other end (see image above). Incorrectly wiring the stepper motor may result in the failure of the stepper driver on the Duet. Do not assume your stepper motors are wired correctly! Please see [User manual: Connecting stepper motors](/User_manual/Connecting_hardware/Motors_connecting) for more details and ways to identify the stepper motor phases.
* Crimp the motor wires to the supplied crimps, and insert them in the plastic shell, before plugging the heater into the pin headers on the board. See earlier on this page for crimping advice.
* For **MB6XD**, see [MB6XD datasheet: Connecting external motor drivers](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6XD_Hardware_Overview#connecting-external-motor-drivers) for details on connecting drivers and motors.
* If you need more stepper motors than available connections, you may use any of the Duet 3 CAN-connected expansion boards. See [Duet 3 Family](/Duet3D_hardware/Duet_3_family)
* For the **Mini 5+** there is also a 2x stepper driver expansion board that connects directly to the Mini 5+; for more details see [Duet_3_Expansion_Mini_2+](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_Mini_2+).
* For more details, see [User manual: Choosing stepper motors](/User_manual/Connecting_hardware/Motors_choosing)
* If you want to run external drivers, see [User manual: Connecting external stepper motor drivers](/User_manual/Connecting_hardware/Motors_connecting_external).

# 11. External 5V input and power supply control

## Tabs {.tabset}

### Duet 3 Mainboard 6HC

[![wiring_d3_5v_02.png](/guides/wiringd3/wiring_d3_5v_02.png =50%x){.align-right}](/guides/wiringd3/wiring_d3_5v_02.png){target=_blank}

* EXT_5V is an optional 5V power input and is not required for operation.
* The Duet has an on-board 5V regulator that gets its source from the VIN input (12-36V).
* Unless you plan to provide an external 5V source, you should at this time check that there is a jumper on 'Int 5V EN'.
* External 5V power can be provided to the board by removing the jumper on 'Int 5V EN' and supplying 5V power to the 'EXT 5V' connector.
* The EXT_5V header also has a pin for controlling an external power supply, `pson`. This allows for the board to be powered from 5V, with an external supply for VIN turned on and off as required. For more details, see [User manual: Power wiring](/User_manual/Connecting_hardware/Power_wiring)

<p style="clear:both"></p>

### Duet 3 Mini 5+

* EXT_5V is an optional 5V power input and is not required for operation.
* The Duet has an on-board 5V regulator that gets its source from the VIN input (12-24V).
* Unless you plan to provide an external 5V source, you should at this time check that there is NO jumper on "Internal 5V Disable" or "+5V Select".
* External 5V power can be supplied to:
  * "5V_EXT_IN" pin on "EXT_5V" connector. In this case, add a jumper to the "+5V Select" jumper between "5V_EXT_IN" and "5V_COM" pins, and add a jumper to "Internal 5V Disable".
  * In some cases you may want to power the Duet from the 5V output of an SBC connected to the SBC header. Note that the total power of the Duet+ peripherals must be factored into the SBC power budget, and should not exceed 1A. Powering the SBC from the duet is not supported. In this case, add a jumper to the "+5V Select" jumper between "5V_SBC" and "5V_COM" pins, and add a jumper to "Internal 5V Disable".
* The EXT_5V header also has a pin for controlling an external power supply, `pson` (note signal shared with io4.out). This allows for the board to be powered from 5V, with an external supply for VIN turned on and off as required. For more details, see [User manual: Power wiring](/User_manual/Connecting_hardware/Power_wiring)

  * <span style="background-color:#00FF00">INT 5V EN</span>
  * <span style="background-color:#FFFF00">EXT 5V EN</span>

<p style="clear:both"></p>

# 12. Fans

**Rewrite for Duet 3**

## Tabs {.tabset}

### Duet 2 WiFi and Ethernet

![wiring_d2we_10_fans.jpg](/guides/wiring/wiring_d2we_10_fans.jpg =49%x) ![wiring_d2we_11_fans.jpg](/guides/wiring/wiring_d2we_11_fans.jpg =49%x) 
* The Duet has three PWM controlled fan headers (<span style="background-color:#FFFF00">outlined yellow</span> in the image above) and two Always On fan headers (<span style="background-color:#FF0000">outlined red</span>).
* A PWM (Pulse Width Modulation) fan connection is for fans you wish to control the speed of, for example a print cooling fan.
* An always on fan is for something like an electronics fan - always on when the printer is on.
* Some fans are more compatible with PWM control than others. If you have trouble varying the speed of a fan, check the documentation for changing PWM frequency.
* The polarity of the fans is important - don't connect them backwards.
* For more details, see [User manual: Connecting and configuring fans](/User_manual/Connecting_hardware/Fans_connecting)

### Duet 2 Maestro

* There are three PWM controlled fan headers and one Always On fan header. Voltage is selectable between VIN, 5V or external supply, in 2 banks. See the wiring diagram for details.
* A PWM (Pulse Width Modulation) fan connection is for fans you wish to control the speed of, for example a print cooling fan.
* An always on fan is for something like an electronics fan - always on when the printer is on.
* Some fans are more compatible with PWM control than others. If you have trouble varying the speed of a fan, check the documentation for changing PWM frequency.
* The polarity of the fans is important - don't connect them backwards.
* For more details, see [User manual: Connecting and configuring fans](/User_manual/Connecting_hardware/Fans_connecting)

# 13. Bed Heater

**Rewrite for Duet 3**

![wiring_d2we_12_bed_heater.jpg](/guides/wiring/wiring_d2we_12_bed_heater.jpg =50%x){.align-right}
* The Duet is able to power a heated bed, and is fused at 15A. This should be well above the requirement for most heated beds.
* If your heater has an integrated LED, then the polarity will matter as the LED will not light with reverse polarity. Otherwise, a heater's polarity doesn't matter.
* **WARNING:** It is HIGHLY recommended to use the included ferrules, by crimping them to the wires before putting the wires in the terminal block. Failure to do so could allow the wires to creep over time, become loose, and could possibly short circuit and start a fire.
* Do not tin (add solder to) these wires.
* Be sure not to twist the terminal block while tightening the screws. It can help to hold the terminal block while tightening.
* Check the screws after a few days/weeks of operation to ensure they are still snug.
* For more details, see [User manual: Connecting and configuring a bed heater](/User_manual/Connecting_hardware/Heaters_bed).

# 14. Bed Thermistor

**Rewrite for Duet 3**

![wiring_d2we_13_bed_thermistor.jpg](/guides/wiring/wiring_d2we_13_bed_thermistor.jpg =50%x){.align-right}
* Connect the bed thermistor to BED_TEMP (<span style="background-color:#FFFF00">outlined yellow</span>).
* The polarity of a thermistor does not matter.
* For more details, see [User manual: Connecting thermistors and PT1000 temperature sensors](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000).

# 15. CONN_SD and CONN_LCD - Duet 2 WiFi and Etherenet

**Rewrite for Duet 3**

![wiring_d2we_14_conn.jpg](/guides/wiring/wiring_d2we_14_conn.jpg =50%x){.align-right}
* <span style="background-color:#FFFF00">CONN_SD</span> is used as an alternative way (see Step 20 below) to connect a [PanelDue](/Duet3D_hardware/Accessories/PanelDue) touchscreen display to the Duet that allows the Duet to access the PanelDue's SD card reader. It can also be used to connect an external SD card reader.
* If you are using a PanelDue, this connection is optional and does not effect the function of the touchscreen monitor itself.  For details, see [Connecting a PanelDue](/User_manual/Connecting_hardware/Display_PanelDue).
* The <span style="background-color:#FF0000">CONN_LCD</span> header can be used for driving [external stepper drivers](/User_manual/Connecting_hardware/Motors_connecting_external), extra [endstop](/User_manual/Connecting_hardware/Sensors_endstops) and/or [filament monitor](/User_manual/Connecting_hardware/Sensors_filament) connections, and connecting [12864 displays](/User_manual/Connecting_hardware/Display_12864).

# 16. PANELDUE_SD and 12864_EXP1/2 - Duet 2 Maestro

**Rewrite for Duet 3**

* The PANELDUE_SD connector is used as an alternative way (see Step 20 below) to connect a [PanelDue](/Duet3D_hardware/Accessories/PanelDue) touchscreen display to the Duet that allows the Duet to access the PanelDue's SD card reader. It can also be used to connect an external SD card reader.
* If you are using a PanelDue, this connection is optional and does not effect the function of the touchscreen monitor itself. For details, see [User manual: Connecting a PanelDue](/User_manual/Connecting_hardware/Display_PanelDue).
* The 12864_EXP1 and 12864_EXP2 connectors are used to connect a 12864 display. For details, see [User manual: Connecting 12864 displays](/User_manual/Connecting_hardware/Display_12864).

# 17. Expansion

**Rewrite for Duet 3**

## Tabs {.tabset}

### Duet 2 WiFi and Ethernet

![wiring_d2we_15_expansion.jpg](/guides/wiring/wiring_d2we_15_expansion.jpg =50%x){.align-right}
* The expansion header is used to connect the Duet 2 WiFi/Ethernet to a [DueX2 or DueX5](/Duet3D_hardware/Duet_2_family/DueX2_and_DueX5) expansion board, or an [Expansion Breakout Board (EBoB)](/Duet3D_hardware/Duet_2_family/Duet_Expansion_Breakout_Board).
* The DueX2 and DueX5 expansion is an available add-on which offers additional stepper motor drivers, heater outputs, fan headers, endstop inputs, additional thermistor inputs, support for additional Temperature Daughterboards, and more.
* The Expansion Breakout Board (EBoB) is an available add-on which offers connections for external stepper drivers and hobby servos.
* The expansion connector can be used to connect other hardware directly; probes, stepper drivers, endstops, general purpose input/output, sensors and triggers. See [User manual: Connecting hardware](/User_manual/Connecting_hardware) for details.

### Duet 2 Maestro

* The expansion header is used to connect other hardware directly; hobby servo, general purpose input/output, sensors and triggers. See [User manual: Connecting hardware](/User_manual/Connecting_hardware) for details.

# 18. Temperature Daughterboard

**Rewrite for Duet 3**

<!--![wiring_d2we_16_tempdb.jpg](/guides/wiring/wiring_d2we_16_tempdb.jpg =32%x) ![wiring_d2we_17_tempdb.jpg](/guides/wiring/wiring_d2we_17_tempdb.jpg =32%x) ![wiring_d2we_18_tempdb.jpg](/guides/wiring/wiring_d2we_18_tempdb.jpg =32%x)-->
* The Temperature Daughterboard connector (labelled 'SPIO' or 'TEMP_DB') allows the Duet to connect with a thermocouple or PT100 temperature sensor, which send a different type of signal than a thermistor.
* Each daughterboard supports two additional temperature sensors of the same type, ie either 2x PT100 or 2x thermocouple.
* Duet 2 WiFi/Ethernet and Duet 2 Maestro boards supports up to two temperature daughterboards.
* A Temperature Daughterboard may be desired if, for example, you wish to print with materials which require greater than 290°C, which is the limit for a thermistor.
* If you plan to use PT100 temperature senesors, please read the wiki regarding the use of Temperature Daughterboards.
* For more details, see [User manual: Connecting thermocouples](/User_manual/Connecting_hardware/Temperature_connecting_thermocouples) and [User manual: Connecting PT100 temperature sensors](/User_manual/Connecting_hardware/Temperature_connecting_PT100)

# 19. Z Probe

**Rewrite for Duet 3**

<!--![wiring_d2we_19_zprobe.jpg](/guides/wiring/wiring_d2we_19_zprobe.jpg =50%x){.align-right}-->
* The Duet supports a Z-probe via this dedicated connector. There are many different kinds of probe, and the wiring will be different for each one.
* See [User manual: Choosing a Z probe](/User_manual/Connecting_hardware/Z_probe_choosing), which will help you choose a suitable Z probe if you have not already.
* See [User manual: Connecting a Z probe](/User_manual/Connecting_hardware/Z_probe_connecting), which contains guidance for wiring your Z probe to the Duet.

# 20. PanelDue

**Rewrite for Duet 3**

<!--![wiring_d2we_20_paneldue.jpg](/guides/wiring/wiring_d2we_20_paneldue.jpg =50%x){.align-right}--> 
* The [PanelDue](/Duet3D_hardware/Accessories/PanelDue), an optional accessory sold separately, is a touchscreen display which gives a user the ability to control the Duet with an intuitive interface directly at the printer.
* The PanelDue can be connected in two ways:
  * A 4-wire cable that contains power and serial signals. This has a maximum recommended length of 1 meter. It plugs into the <span style="background-color:#FFFF00">PanelDUE</span> header.
  * a 10-way flat cable which claos contains an external SD card signals with a maximum recommended length of 400mm (see earlier section on CONN_SD/PANELDUE_SD)
* For more details, see [User manual: Connecting a PanelDue](/User_manual/Connecting_hardware/Display_PanelDue).

# 21. WiFi or Ethernet Module

**Rewrite for Duet 3**

<!--![wiring_d2we_23_wifi.jpg](/guides/wiring/wiring_d2we_23_wifi.jpg =49%x) ![wiring_d2we_24_ethernet.jpg](/guides/wiring/wiring_d2we_24_ethernet.jpg =49%x)-->
* Note: **Duet 2 Maestro** is Ethernet-only.
* The WiFi or Ethernet Module supports a connection over a web interface. It is responsible for a network connection as well as the web-based user interface.
* The WiFi module has a blue LED that flashes only when the firmware is being changed.
* The Ethernet module has two LEDs on the RJ45 housing; Link, which is on when an ethernet connection is established, and Activity, which flashed whenever data is being actively transferred.

# 22. Hotend Thermistor(s)

**Rewrite for Duet 3**

<!--![wiring_d2we_26_thermistors.jpg](/guides/wiring/wiring_d2we_26_thermistors.jpg =50%x){.align-right}-->
* Connect your hotend thermistors to <span style="background-color:#FFFF00">E0_TEMP</span> and/or <span style="background-color:#FFFF00">E1_TEMP</span>.
* The polarity of thermistors does not matter.
* A thermistor can read up to 290°C. If you wish to print at a higher temperature than this, you should upgrade to a thermocouple or PT100 temperature sensor (see step 18). A PT100 also provides a more consistent reading between multiple sensors and resists noise interference in 4-wire mode.
* **Duet 2 Maestro** has an additional temperature input, C_TEMP. It is electrically identical to the other temperature inputs, so can be used in the same way. 
* For more details, see [User manual: Connecting thermistors and PT1000 temperature sensors](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000).

# 23. Wiring Complete!
* Congratulations! Your Duet is wired and is ready to configure. 

<!--Continue to 3.) Configuration (General Cartesian) to configure your Duet!-->