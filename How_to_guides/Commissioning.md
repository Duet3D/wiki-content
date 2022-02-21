---
title: Commissioning your machine
description: 
published: false
date: 2022-02-21T15:40:25.484Z
tags: 
editor: markdown
dateCreated: 2022-02-04T13:42:24.938Z
---

# Introduction

**This document is relevant to:** All Duet boards
**Firmware versions:** All versions
**Difficulty:** Moderate
**Time Required:** 30 minutes - 1 hour

This guide covers configuring RepRapFirmware for your machine.

If you have any problems with your Duet when using this guide, rather than posting comments, **please use our support forum: [https://forum.duet3d.com/](https://forum.duet3d.com/){target=_blank}**

# 1. Turn on power

> If you have just changed your configuration, and the Duet has rebooted with mains power, **IMMEDIATELY** check that your new configuration is not heating heaters/moving axes/starting spindles or any other unexpected and unwanted behaviour. If it is, **TURN OFF THE POWER!** You can investigate any problems and check configuration by powering the Duet with USB power only.
{.is-warning}

# 2. Connect to Duet Web Console (DWC)

> If you changed the machine name in the RepRapFirmware Configurator, this will be reflected in the .local address. For instance, if you named the machine "My Printer", you will navigate to myprinter.local/ (without spaces and not case sensitive).
{.is-info}

* Open your browser and navigate to yourduetname.local/ or the IP address .

# 3. Check Thermistors

Check the temperature reading on heaters, eg "T0", "Bed". 
* It should be around room temperature if the heaters have not recently been on. 
* It is OK if there's a few degrees of error as thermistor readings have better resolution at higher temperatures.
* If you get a temperature reading of "-273°C", this indicates an open circuit, i.e. nothing is connected to the defined pins.
  * Check that the temperature sensor is connected to the correct pins
  * Check the wiring for breaks
  * Measure the resistance of the wires that connect to the Duet, and that it corresponds with what the firmware expects
  * Check that the configuration is set correctly for the temperature sensor.
* If you get a temperature reading of "2000°C", this indicates a dead short between the temperature sensor pins on the Duet, or mis-configured firmware.
  * Check your M305/M308 configuration
  * Check the wiring isn't grounding out to something
* See [User manual: Connecting thermistor and PT1000 temperature sensors](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000) and [M308](/User_manual/Reference/Gcodes/M308) (RRF 3.x) or [M305](/User_manual/Reference/Gcodes/M305) (RRF 2.x) for more details.

# 4. Check Fans 

* Always On fans should already be on. Check them at this time.
* If you have a fan that is connected as FAN 0, you can enable it by sending G-Code command `M106 P0 S1`. To turn it off, send `M106 P0 S0`
* For thermostatically controlled fans, we can check them by temporarily changing the temperature at which they activate.
  * In the Duet web interface, load the G-Code Console.
  * Send the following command to the Duet: `M106 P1 T1 H1`. The T parameter sets the temperature it comes on 
  * P1 is for Fan 1. If you have two thermostatically controlled fans, repeat this step after changing P1 to P2.
* After confirming the operation of the fans, you may reset the configuration by simply pressing the "Reset" button on the Duet, or send `M999`.
* See [User manual: Connecting and configuring fans](/User_manual/Connecting_hardware/Fans_connecting) and GCode [M106](/User_manual/Reference/Gcodes/M106) for more details.

# Check Heater Functionality

Since we have checked for proper operation of our thermistors, we may now check our heaters.
* On the Machine Control page, enter a number in the "Active" box for each heater. Start off with a low number such as 35°C.
* Select the drop down box next to "Tool 0" and click "Select Tool".

You should see the corresponding temperature begin to rise. It is possible that it will overshoot the set temperature a bit, this is OK.

Complete this step by selecting "Tool 0" and "Deselect Tool" to put the tool into standby.

Repeat this step for the bed heater.

WARNING: If you enable the heater but do not observe an increase in the temperature reading, turn off the controller immediately and check your wiring.

It is recommended that you tune your heaters after ensuring their functionality.

# Check Endstop Configuration 

You should have already checked for proper operation of endstops. At this time, we will make sure that they are correctly configured.

Navigate to the console.

Send M119 - you will see the state of the endstops, will all be non triggered, then hold each one down in turn, while sending M119 again and confirm the state changes to triggered.

# 9. Checking Endstops

* It is important that you check that the Duet is receiving a signal from your endstops. Failure to do so could cause damage to your printer!
* On Duet 2 WiFi/Ethernet, there is an enstop status LED between each motor driver.
* You can also see the status of your endstops a number of ways:

## Tabs {.tabset}

### M119

[![wiring_d2we_06_test_endstop_01.png](/guides/wiring/wiring_d2we_06_test_endstop_01.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_01.png){target=_blank}
The simplest way of checking endstop status is to send [M119](/User_manual/Reference/Gcodes/M119), and this can be sent from DWC or if connected by serial terminal over USB.
* In DWC, go to Control > Console and type in `M119` in the text box, then press return or the 'Send' button. You should get the endstop status response in the area below.
* If connected to the Duet by a serial terminial over USB, type `M119` and press return; the Duet will respond with the endstop status.
* Press and hold an endstop switch, and sent the command again, and you should see the status response of that switch change.

<p style="clear:both"></p>

### Object model browser

[![wiring_d2we_06_test_endstop_02.png](/guides/wiring/wiring_d2we_06_test_endstop_02.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_02.png){target=_blank}[![wiring_d2we_06_test_endstop_03.png](/guides/wiring/wiring_d2we_06_test_endstop_03.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_03.png){target=_blank}TO DO

<p style="clear:both"></p>

### Endstop plugin

[![wiring_d2we_06_test_endstop_04.png](/guides/wiring/wiring_d2we_06_test_endstop_04.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_04.png){target=_blank}[![wiring_d2we_06_test_endstop_05.png](/guides/wiring/wiring_d2we_06_test_endstop_05.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_05.png){target=_blank}TO DO

<p style="clear:both"></p>


# Configure Endstops: Active High or Low 

If you found that any endstops are not configured properly in the last step, navigate to Settings, then to "System Editor" and open the config.g file.

Endstops are configured here. The first line (S0) defines all active low endstops.

An active low endstop is one which pulls the signal to ground when the endstop is pressed (normally open). An active high endstop is one which pulls the signal to ground when the endstop is not pressed.

Never connect an endstop wires from +3.3v to ground. This will create a short circuit and could damage the Duet.

The second line (S1) defines all active high endstops.

If you are unsure if your endstops are active high or active low, you can test them by observing the light next to the corresponding stepper motor connector. If the light is lit when the button is pressed, then this would indicate an active low endstop.

# Configure Endstops: High or Low End

Remaining in the same config.g file as the last step, observe the number next to each axis' endstop - for example X0, Z0 and Y2.

Think of "low end" and "high end" as a numeric scale for the axis. If your Y axis has a 250mm range of motion, the low end is 0mm and the high end is 250mm.

Observe the physical location of each endstop and compare it to this setting. If it is incorrect, change it now.

# Check Stepper Motors 

Before conducting this step, temporarily allow axis movement without homing by navigating to the GCode console and entering: `M564 S0 H0`

Navigate back to the Machine Control page. At this time, we will check the operation of our stepper motors.

Move each stepper motor, individually, 1 mm in each direction. If any motors are incorrect, we will fix them in the next step.

Note that a stepper can't be moved before homing. unless the M564 command is used to override this safety default.

If your motor moves in the correct direction, you may home the axis at this time.

Important: Be sure that you have confirmed the correct operation of your endstops before homing.

# Reversing a Stepper Motor 

To reverse the direction of a stepper motor, navigate to Settings, then to System Editor and open the config.g file.

Look for M569 settings. S1 configures a drive to move in the "forward" direction and S0 is "reverse".

For any stepper motors that are moving in the wrong direction, change the configuration of S1 to S0 for the corresponding drive.

The default mapping for drives is 0=X, 1=Y, 2=Z, 3=E0 and 4=E1.

# Configuring PanelDue

If you are using a PanelDue, if connected properly it should already be configured.

You can check that it is functioning by making sure that it turned on and is displaying reasonably correct temperature information. You may also choose to home an axis or by starting a heater.

# Configuring Z Probe

Begin by familiarizing yourself with the different probe modes which can be found Connecting a Z probe

Navigate to Settings, then System Editor and open the config.g file.

Change the P value of M558 to match whichever probe you are using.

X, Y and Z values define whether or not there is a probe on that axis. A zero means that a probe is not used.

H value defines the dive height, which is the height above the trigger height from which probing starts.

F value defines the "feed rate", or probing speed in millimeters per minute.

T value defines the travel speed at which the probe is moved between probe points in millimeters per minute.

For more information on Z probing see these wiki pages: Choosing a Z probe, how to Test and calibrate a Z probe and Setting up automatic probing of the print bed.

# Test Z Probe

Test the Z probe and ensuring that the status changes in Duet Web Control.

e.g. If using an IR probe, by putting a piece of paper under the probe

# Configuration Complete!

Congratulations! Upon completion of this guide, your Duet is set up. For further details the following wiki pages are a good place to start:

Step by step guide to using your Duet

Configuring RepRapFirmware for a Cartesian Printer

FAQ

The complete reference for all gcodes use for configuration and control: Gcode dictionary