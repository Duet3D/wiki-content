---
title: BLTouch Troubleshooting
description: Getting the most out of your BLTouch on the Duet.
published: true
date: 2024-10-28T13:12:53.765Z
tags: 
editor: markdown
dateCreated: 2021-12-05T23:15:24.992Z
---

<!-- [summary_image|912]-->

The BLTouch is an attractive Z probe option because it works on any surface, is relatively cheap, and provides good accuracy and repeatability. However, it can be tricky to get working. This guide will provide some troubleshooting tips for the most commonly encountered problems.

## General

* **Avoid Clones.** This guide assumes you are using a genuine BLTouch from Antclabs. There are clones available, but many users have encountered quality issues using them. The most current version of the BLTouch is the [BLTouch-Smart V3.1 from Antclabs](https://www.antclabs.com/bltouch-v3). Most clones are a knockoff of the older BLTouch Classic and may require a resistor to function properly with 3.3v logic boards. You must ask yourself, are you really saving any money on the clone if you end up having to buy 2 anyway just to get a working one?

## Mounting

* **Mount the BLTouch Properly.** The BLTouch must be mounted firmly and squarely to the print carriage. Any flex or tilt could make the results unreliable. Also, follow the BLTouch instructions carefully with regards to mounting height. The tip of the BLTouch pin, when not extended, must be 2.3mm to 4.3mm above the nozzle tip to trigger properly.
* **Check the Pin.** The BLTouch won't function reliably if the pin is dirty or damaged. The pin can be removed for cleaning or replacement. The set screw at the top of the BLTouch holds the pin inside the body, and also acts as the ferrous core for the electromagnet to draw up the pin. Try loosening or tightening the set screw slightly to adjust the trigger sensitivity.

## Wiring

* **Double Check Your Wiring.** [Follow the installation guide carefully](/User_manual/Connecting_hardware/Z_probe_connecting#bltouch) The BLTouch has 5 wires that must be properly connected to function. Check for a loose crimp connection, or a broken wire with a multimeter. 
* **Cut the Trace.** Version2 BLTouch Smart probes have a trace on the PCB which can be cut to allow safe functionality with 3.3v logic board, such as that used by the Duet. Use a sharp xacto knife to cut the trace completely. Use a multimeter to verify that it is completely cut. The Duet should be able to handle it without cutting the trace, but there have been user reports of problems being resolved after making sure the trace is cut.
* **Note that BLTouch V3+ no longer has a trace and is not needed to work with the Duet.**
* **Use the Resistor.** Older BLTouch Classic probes and most clones required the use of an included 240 Ohm resistor to function with 3.3v logic boards. Again, this shouldn't be necessary with the Duet, but there have been reports that it has solved problems, especially with clones.

## Firmware configuration

* **Update Duet Firmware.** Firmware version 1.21 and 2.01 have some BLTouch specific improvements which will let you get the best performance from the probe. See here for a [firmware upgrade walkthrough](/User_manual/RepRapFirmware/Updating_firmware).
* **Use Probe Type 9.** RRF now has a dedicated BLTouch [M558 probe type](/User_manual/Reference/Gcodes/M558). Older firmware versions used type 5, but this method is no longer recommended. 
* **Double Check Deployprobe.g and Retractprobe.g** Make sure your deploy and retract macros located in the sys folder are configured correctly. Make sure the servo commands reference the servo index number created by an [M950](/User_manual/Reference/Gcodes/M950) command (RRF 3.x) or the heater pin number (RRF 2.x) you have chosen to use.
* **Don't use M98 P"deployprobe.g"**. The old way (RRF before 1.21/2.01) using probe type 5 required you to use `M98 P"Deployprobe.g"` and `M98 P"Retractprobe.g"` to deploy and retract the pin. Type 9 now handles the deployment internally. If you need to manually deploy and retract, use [M401](/User_manual/Reference/Gcodes/M401) and [M402](/User_manual/Reference/Gcodes/M402), respectively. This allows the Duet to keep track of the deployment state of the pin. [G30](/User_manual/Reference/Gcodes/G30) and [G29](/User_manual/Reference/Gcodes/G29) will automatically deploy as needed when using Type 9.
* **Measure your Trigger Height.** To get a proper first layer height, the distance from the nozzle tip to the bed must be known at the point when the probe triggers. [Follow this guide to measure it](/User_manual/Connecting_hardware/Z_probe_testing#calibrate-the-z-probe-trigger-height).
* **Measure your XY Offset.** To get accurate probe results, the Duet needs to know how far the BLTouch is from the nozzle in X and Y. To measure this, place a piece of paper on the bed and use tape to hold it in place. Move the nozzle down until the it touches the paper and leaves a small imprint. Make note of the current X and Y coordinate. Use a marker to make the spot clearly visible. Now jog the print head in X and Y until the BLTouch pin is on the exact spot you marked. Make note of the new X and Y coordinates. The difference between the two will be your X Y offset in the G31 command.
* **Modify your M558 and G31.** The BLTouch behaviour is controlled by the [M558](/User_manual/Reference/Gcodes/M558) and [G31](/User_manual/Reference/Gcodes/G31) commands in config.g. Here is what an ideal set of commands should look like. 
  ```
  M558 P9 F100 H5 R0.2 T6000 A5 B1
  ``` 
  This sets probe type 9 for BLTouch, 100mm/min probe speed, 5mm dive height, 0.2 second pause time before probe move, 100mm/s travel moves between probe points, probe up to 5 times , or until 2 consecutive probe results match, and disable heaters during a probe move to limit interference. 
  ```
  G31 P25 X10 Y10 Z2
  ```
  Sets the trigger sensitivity to 25, the X Y and Z values should be measured to match your setup.
* **Unbind the Heater Pin.** (RRF 2.x only) The BlTouch uses a spare heater pin as a servo pin. To do this, the Duet must be told to release the heater pin from its default use by adding 
  ```
  M307 H7 A-1 C-1 D-1
  ```
  to config.g H7 refers to heater pin 7 on the expansion pins, but you can also use heater pin 3, in which case you would use H3.
* **Check your config-override.g for heater bindings.** If you still have problems after unbinding your heater pin in config.g check config-override.g for any additional [M307](/User_manual/Reference/Gcodes/M307) commands using the heater pin you intend to use and remove it.
* **Use G30 to Home Z.** Make sure your homing macros use [G30](/User_manual/Reference/Gcodes/G30) to home Z and not a G1 S1 Z movement.
* **Make sure you have all the required files** if you are coming from another probe or endstop to the BLTouch, make sure you change your homez.g, add retractprobe.g and deployprobe.g.

## Testing

* **Test the Probe First.** It's a good idea to test the probe configuration before mounting it on the printer. You don't want the probe to fail during a home operation because you could damage your hotend or bed surface. [See here for testing procedure](/User_manual/Connecting_hardware/Z_probe_testing).