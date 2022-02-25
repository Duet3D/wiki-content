---
title: Commissioning your machine
description: 
published: false
date: 2022-02-25T22:54:19.597Z
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

* Open your browser and navigate to `yourduetname.local` or the IP address you set. If you have trouble finding the IP address of the Duet, you can:
  * Connect to your Duet via USB. Send [M552](/User_manual/Reference/Gcodes/M552) to see the network configuration and IP address.
  * Login to your router; it should be able to show a list of connected devices
  * Use a network scanning app to show the connected devices on the network.
* You will be using Duet Web Console (DWC) for most of the commissioning; see [User manual: Duet Web Console](/User_manual/Reference/Duet_Web_Control_Manual) for a full introduction to the interface.

# 3. Check thermistors

> To get accurate temperatures, you must configure temperature sensing correctly for the type of temperature sensor you are using. The default values in the RepRapFirmware Configuration Tool are unlikely to be correct!
{.is-warning}

On the 'Control > Dashboard' page of DWC, in the Tools section, check the 'Current' temperature reading on each heater. Each heater is configured to be either a Tool, Bed or Chamber, eg "T0", "Bed". 
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

# 4. Check fans 

* Always On fans should already be on. Check them at this time.
* If you have a fan that is connected as FAN 0 (usually the part cooling fan), you can use the Fan Control slider to check it works. You can also enter GCode commands directly in the 'Control > Console' page:
  * Turn it on by sending GCode command `M106 P0 S1`. 
  * Turn it off by sending `M106 P0 S0`. 
* For thermostatically controlled fans, we can check them by temporarily changing the temperature at which they activate.
  * In DWC, go to Control > Console.
  * In the box under Status section, enter the following command then press return to send it to the Duet: `M106 P1 T1 H1`. The T parameter sets the temperature the fan comes on at. 
  * To turn it off, send `M106 P1 T50 H1`
  * P1 is for Fan 1. If you have two thermostatically controlled fans, repeat this step after changing P1 to P2.
* See [User manual: Connecting and configuring fans](/User_manual/Connecting_hardware/Fans_connecting) and GCode [M106](/User_manual/Reference/Gcodes/M106) for more details.

> After confirming the operation of the fans, to reset the configuration you can either press the "Reset" button on the Duet, or send `M999`, or re-run config.g by sending `M98 P"config.g"` in 'Control > Console'.
{.is-info}

# 5. Check heater functionality

Since we have checked for proper operation of our thermistors, we may now check our heaters.
* On the Dashboard page, the Tools section lists the tools, bed heaters and chamber heaters.
* The firmware allows one tool to be selected/active at a time. The associated heater(s) is/are set to 'active' when the tool is selected, and set to 'standby' when the tool is deselected. However, tool heaters can also be manually controlled, independently of tools.
* Enter a number in the "Active" box for the first tool. Start off with a low number such as 35°C. 
* Under the first 'Heater [#]' (where # is the heater number) it should say 'off', 'standby' or 'active'.
  * Clicking the associated tool name (eg 'T0' in the Tool column) will cycle the heater between 'active' and 'standby', as the tool is selected and deselected. The target temperature for each state is set by the associated Active and Standby temperature.
  * Clicking the heater name (eg 'Heater 1' in the Heater column) will cycle the heater between 'active', 'standby' and 'off'. The heater can be controlled independently of the tool state.
  * Bed and Chamber heaters are controlled more directly, and cycle through 'active', 'standby' and 'off' whether you click the name or the heater number.
* With the heater set to 'active', you should see the corresponding temperature begin to rise. It is possible that it will overshoot the set temperature a bit; this is OK.
> **WARNING:** If you enable the heater but do not observe an increase in the temperature reading, turn off the controller immediately and check your wiring.
{.is-warning}
* You may receive errors such as "Error: Heater [heater #] fault: temperature rising too slowly". This may be because the firmware does not have an accurate model of how the heater responds. You will need to 'tune' the heaters, which is covered in the next section.
  * Click on the tool, bed or chamber name, or the heater name, to reset the heater fault. DWC will pop up a stern warning with a timer, and allow you to reset the fault after a few seconds. You can also send [M562](/User_manual/Reference/Gcodes/M562) to reset temperature faults.
* Complete this step by clicking the tool, bed or chamber name, or the heater name, to set the tool/heater into standby.
* Repeat this step for the rest of the tool, bed and chamber heaters.
* If you need to 'hot-tighten' your hot end nozzle, now is a good time to do it. Set the Tool heater active temperature to the temperature specified by the manufacturer and click the tool name to set the tool to active. Once finished, click the tool name to turn off the heater.

# 6. Tune heaters

It is recommended that you *'tune'* your heaters after ensuring their functionality. This gives the firmware an accurate model of how your heater responds, so it can spot if something goes wrong. Make sure that your temperature sensors are configured correctly and reporting sensible temperatures first!
* If you have received a temperature error and a heater is marked as 'fault', click on the tool, bed or chamber name, or the heater name, to reset the heater fault. DWC will pop up a stern warning with a timer, and allow you to reset the fault after a few seconds. You can also send [M562](/User_manual/Reference/Gcodes/M562) to reset temperature faults.
* To tune heaters, use the [M303](/User_manual/Reference/Gcodes/M303) GCode command.
  * Heaters must be at room temperature before starting tuning.
  * Move the hot end so the nozzle is around 1mm above the bed. If you haven't tested moving the motors yet, you can move the hot end SLOWLY by hand. Tuning can use check the effect of the cooling fan on the nozzle heater.
  * Tune tools first. In Control > Console, send `M303 T0 S200` where 'T' is the tool number, and 'S' is the target temperature. Tuning a tool heater usually takes around five minutes.
  * To tune a bed or chamber heater, send `M303 H0 S60` where 'H' is the heater number for the bed or chamber, and 'S' is the target temperature. Tuning these heaters can take a long time, possibly up to 2 hours, as the heater needs to go through a number of heating and cooling cycles. The bigger the heater, the longer this takes.
* If successful, the firmware will report the parameters to use, and an [M307](/User_manual/Reference/Gcodes/M307) GCcode command eg 
```
M307 H1 R7.046 K1.519:0.006 D3.58 E1.35 S1.00 B0 V23.9
```
* Either:
  * Copy this into your config.g, replacing the existing one for the heater, or
  * If your config.g has [M501](/User_manual/Reference/Gcodes/M501) at the end, you can save the current heater settings by sending [M500](/User_manual/Reference/Gcodes/M500). This will save the current parameters to the sys/config-override.g on the SD card. M501 in config.g will load them at each reboot.
* If you encounter any errors, see [User manual: Tuning the heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning) for more details.

> After confirming the operation of the heaters and to save and implement the changes you have made, you need to reset the Duet. If you edit then save the config.g, DWC should ask if you want to reset the Duet or re-run config.g. You can also press the "Reset" button on the Duet, or send `M999`, or re-run config.g by sending `M98 P"config.g"` in 'Control > Console'.
{.is-info}

# 7. Check Endstops

> Never connect endstop wires from +3.3v to ground. This will create a short circuit and could damage the Duet.
{.is-warning}

When 'homing' your machine, each axis will move towards the end of its travel. It expects to trigger a switch, which will set the axis location. Simple microswitches, hall sensors or optical sensors can be used.
* It is important that you check that the Duet is receiving a signal from your endstops, if you have them fitted. Failure to do so could cause damage to your printer!
* You want the firmware to report them as 'not stopped' when they are not triggered, and 'at max/min stop' when they are triggered.
* On Duet 2 WiFi/Ethernet, there is an endstop status LED between each motor driver.
* You can also see the status of your endstops a number of ways:

## Tabs {.tabset}

### M119

[![wiring_d2we_06_test_endstop_01.png](/guides/wiring/wiring_d2we_06_test_endstop_01.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_01.png){target=_blank}The simplest way of checking endstop status is to send [M119](/User_manual/Reference/Gcodes/M119), and this can be sent from DWC or if connected by serial terminal over USB.
* In DWC, go to Control > Console and type in `M119` in the text box, then press return or the 'Send' button. You should get the endstop status response in the area below.
* If connected to the Duet by a serial terminial over USB, type `M119` and press return; the Duet will respond with the endstop status.
* Press and hold an endstop switch, and sent the command again, and you should see the status response of that switch change.

<p style="clear:both"></p>

### Object model browser

[![wiring_d2we_06_test_endstop_02.png](/guides/wiring/wiring_d2we_06_test_endstop_02.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_02.png){target=_blank}[![wiring_d2we_06_test_endstop_03.png](/guides/wiring/wiring_d2we_06_test_endstop_03.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_03.png){target=_blank}You can also check the endstops status in the DWC Object model browser. The RepRapFirmware Object model shows all the firmware variables and values.
* Enable the Object model browser by going to 'Settings > Plugins > Integrated plugins' ('Settings > General > Built-in Plugins' in older versions of DWC) and click 'Start' on the 'Object Model Browser'.
* A new menu option 'Object Model' will appear; select it.
* Navigate to 'sensors > endstops'. Expand the numbered sections. Trigger an endstop, and it will show as 'triggered = true' if correctly configured.

<p style="clear:both"></p>

### Endstop plugin

[![wiring_d2we_06_test_endstop_04.png](/guides/wiring/wiring_d2we_06_test_endstop_04.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_04.png){target=_blank}[![wiring_d2we_06_test_endstop_05.png](/guides/wiring/wiring_d2we_06_test_endstop_05.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_05.png){target=_blank}You can also install a plugin to show endstop status.
* Go to [https://github.com/Duet3D/DSF-Plugins/releases/](https://github.com/Duet3D/DSF-Plugins/releases/){target=_blank}
* Download the "EndstopsMonitor-xxx.zip", where "xxx" is the version number, and matches the version of DWC you are running (check on the 'Setting > General' page).
* Go to 'Settings > Plugins > External plugins' ('Settings > Machine-specific > Machine-specific plugins' in older versions of DWC) and click 'Install plugin'.
* Navigate to the "EndstopsMonitor-X.X.zip" you downloaded, select and click 'Open'.
* Click through the next few windows, reading the information and warnings.
* Once installed, click on 'Start'. 
* You can now see the endstop status in 'Settings > Machine-specific > Endstops'.

<p style="clear:both"></p>

# 8. Re-configure Endstops

If you found that any endstops are not configured properly in the last step, navigate to 'Files > System' and open the config.g file.
* Endstops are configured here. Each endstop has it's own configuration line, using [M574](/User_manual/Reference/Gcodes/M574).

**High end or low end?**
* In RRF 3.x, the  M574 X, Y, and Z parameters configure the position of the endstop; 0 for no endstop, 1 for low end endstop, 2 for high end endstop. eg `M574 X0` would mean no endstop on X, `M574 Y2` means Y endstop on the high end.
* Think of "low end" and "high end" as a numeric scale for the axis. If your Y axis has a 250mm range of motion, the low end is 0mm and the high end is 250mm.
* Observe the physical location of each endstop and compare it to this setting. If it is incorrect, change it now.
* On a Delta, all endstops are usually at the high end. On Cartesian and CoreXY, the Z low end is where the nozzle is closest to the bed, ie where Z=0.

**Active high or active low?**
* In RRF 3.x, the M574 P parameter configures the pins the endstop is connected to. It also configures how the signal is interpreted.
* An active low endstop is one which pulls the signal to ground when the endstop is pressed. On Duet, this means that it is 'normally open' (or NO) when not pressed. 
* An active high endstop is one which pulls the signal to ground when the endstop is not pressed. On Duet, this means it is 'normally closed' (or NC) when not pressed. Duet3D recommend NC endstops, as they are less susceptible to interference from other sources, eg stepper motor wiring.
* If you found that the endstop was responding the wrong way in the test in the previous section, we can invert the input on the pins, in the endstop definition. Do this by:
  * Invert the signal in the M574 command with `!`, eg `M574 Y2 P"!ystop"`
  * If the M574 command already has a `!` in it, remove it.
* On Duet 2 WiFi/Ethernet, you can test whether your endstops are active low or active high by observing the red LED next to the corresponding stepper motor connector. If the LED is lit when the endstop is triggered, this would indicate an active low endstop. If the LED is lit when the endstop is not triggered, this would indicate an active high endstop.
* See [Configuring endstop switches](/User_manual/Connecting_hardware/Sensors_endstops) for more details.

> After confirming the operation of the endstops and to save and implement the changes you have made, you need to reset the Duet. If you edit then save the config.g, DWC should ask if you want to reset the Duet or re-run config.g. You can also press the "Reset" button on the Duet, or send `M999`, or re-run config.g by sending `M98 P"config.g"` in 'Control > Console'.
{.is-info}

<!-- Done up to here -->

# 9. Check Stepper Motors 

* Before conducting this step, temporarily allow axis movement without homing by navigating to 'Control > Console' and entering: `M564 S0 H0`
* Navigate back to 'Control > Dashboard'. You will check the operation of our stepper motors.
* Move each stepper motor, individually, 1 mm in each direction. If any motors are incorrect, we will fix them in the next step.
* Note: for cartesian kinematics, where only one motor moves for each X, Y and Z axis, this is straightforward. If you have a CoreXY, delta or Scara, see [User manual: Testing stepper motors](/User_manual/Connecting_hardware/Motors_testing) for the correct motor movements.
* Note: a stepper can't be moved before homing, unless the [M564](/User_manual/Reference/Gcodes/M564) command is used to override this safety default.
* Be sure that you have confirmed the correct operation of your endstops before homing.
* If your motor moves in the correct direction, you may home the axis at this time.

**Reversing a Stepper Motor**
* To reverse the direction of a stepper motor, navigate to 'Files > System' and open the config.g file.
* Look for M569 settings. S1 configures a drive to move in the "forward" direction and S0 is "reverse".
* For any stepper motors that are moving in the wrong direction, change the configuration of S1 to S0 for the corresponding drive.
> After confirming the operation of the motors and to save and implement the changes you have made, you need to reset the Duet. If you edit then save the config.g, DWC should ask if you want to reset the Duet or re-run config.g. You can also press the "Reset" button on the Duet, or send `M999`, or re-run config.g by sending `M98 P"config.g"` in 'Control > Console'.
{.is-info}

# 10. Configuring PanelDue

* If you are using a PanelDue, if connected properly it should already be configured.
* You can check that it is functioning by making sure that it turned on and is displaying reasonably correct temperature information. You may also choose to home an axis or by starting a heater.
* If the display is not connecting, make sure you have the following command in config.g: `M575 P1 S1 B57600`

# 11. Z Probe

**Check configuration**
* Begin by familiarizing yourself with the different probe modes; see [User manual: Connecting a Z probe](/User_manual/Connecting_hardware/Z_probe_connecting)
* Navigate to 'Files > System' and open the config.g file.
* [M558](/User_manual/Reference/Gcodes/M558) configures the Z probe. Check the P value matches whichever probe you are using.
* H value defines the dive height, which is the height above the trigger height from which probing starts.
* F value defines the "feed rate", or probing speed in millimeters per minute.
* T value defines the travel speed at which the probe is moved between probe points in millimeters per minute.
* For more information on Z probing see: 
  [User manual: Choosing a Z probe](/User_manual/Connecting_hardware/Z_probe_choosing)
  [Connecting a Z probe](/User_manual/Connecting_hardware/Z_probe_connecting)

**Test Z Probe**
* Test the Z probe by ensuring that the status changes in Duet Web Control. e.g. If using an IR probe, by putting a piece of paper under the probe.
* For more detailed instructions, see [User manual: Test and calibrate a Z probe](/User_manual/Connecting_hardware/Z_probe_testing)

# 12. Commissioning Complete!

> Congratulations! Upon completion of this guide, your Duet is set up. 
{.is-info}

The next step will be calibrating and tuning the various parts of your printer, running test prints, and then printing! This can include:
* Levelling the bed.
* Calibrating extruder steps per mm.
* Tuning Acceleration, Jerk, Retraction, Pressure Advance
* Input shaping

For the above see [User manual: Tuning](/User_manual/Tuning)

For further details the following wiki pages are a good place to start:
* [Configuring RepRapFirmware for a Cartesian Printer](/User_manual/Machine_configuration/Configuration_cartesian)
* [Gcode dictionary](/User_manual/Reference/Gcodes)