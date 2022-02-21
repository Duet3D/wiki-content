---
title: Commissioning your machine
description: 
published: false
date: 2022-02-21T13:36:37.262Z
tags: 
editor: markdown
dateCreated: 2022-02-04T13:42:24.938Z
---

# Check Thermistors

Note: If you changed the machine name in the RepRapFirmware Configurator, this will be reflected in the .local address. For instance, if you named the machine "My Printer", you will navigate to myprinter.local/ (without spaces and not case sensitive).

Open your browser and navigate to yourduetname.local/.

Check that you are getting a temperature reading on "Tool 0" and "Bed". It should be around room temperature. It is OK if there's a few degrees of error as thermistor readings have better resolution at higher temperatures.

If you get a reading of "2000°C", there is a problem with your thermistor wiring. Correct this before continuing.

# Check Fans 

Always On fans should already be on. Check them at this time.

If you have a fan that is connected to F0, you can enable it by sending G-Code command "M106 P0 S1"

For thermostatically controlled fans, (which are F1 and F2 by default) we can check them by temporarily changing the temperature at which they activate.

In the Duet web interface, load the G-Code Console.

Send the following command to the Duet: "M106 P1 T1 H1".

P1 is for Fan 1. If you have two thermostatically controlled fans, repeat this step after changing P1 to P2.

After confirming the operation of the fans, you may reset the configuration by simply pressing the "Reset" button on the Duet, if you wish. Do not confuse the Reset button with the Erase button!

For more details on configuring fans see these wiki pages: Connecting and configuring fans, and Gcode: M106

# Check Heater Functionality

Since we have checked for proper operation of our thermistors, we may now check our heaters.

On the Machine Control page, enter a number in the "Active" box for each heater. Start off with a low number such as 35°C.

Select the drop down box next to "Tool 0" and click "Select Tool".

You should see the corresponding temperature begin to rise. It is possible that it will overshoot the set temperature a bit, this is OK.

Complete this step by selecting "Tool 0" and "Deselect Tool" to put the tool into standby.

Repeat this step for the bed heater.

WARNING: If you enable the heater but do not observe an increase in the temperature reading, turn off the controller immediately and check your wiring.

It is recommended that you tune your heaters after ensuring their functionality.

# Check Endstop Configuration 

You should have already checked for proper operation of endstops. At this time, we will make sure that they are correctly configured.

Navigate to the console.

Send M119 - you will see the state of the endstops, will all be non triggered, then hold each one down in turn, while sending M119 again and confirm the state changes to triggered.

# Configuring Active High or Active Low Endstops 

If you found that any endstops are not configured properly in the last step, navigate to Settings, then to "System Editor" and open the config.g file.

Endstops are configured here. The first line (S0) defines all active low endstops.

An active low endstop is one which pulls the signal to ground when the endstop is pressed (normally open). An active high endstop is one which pulls the signal to ground when the endstop is not pressed.

Never connect an endstop wires from +3.3v to ground. This will create a short circuit and could damage the Duet.

The second line (S1) defines all active high endstops.

If you are unsure if your endstops are active high or active low, you can test them by observing the light next to the corresponding stepper motor connector. If the light is lit when the button is pressed, then this would indicate an active low endstop.

# Configure High End or Low End Endstops

Remaining in the same config.g file as the last step, observe the number next to each axis' endstop - for example X0, Z0 and Y2.

Think of "low end" and "high end" as a numeric scale for the axis. If your Y axis has a 250mm range of motion, the low end is 0mm and the high end is 250mm.

Observe the physical location of each endstop and compare it to this setting. If it is incorrect, change it now.

# Check Stepper Motors 

Before conducting this step, temporarily alow axis movement without homing by navigating to the G Code console and entering: M564 S0 H0

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