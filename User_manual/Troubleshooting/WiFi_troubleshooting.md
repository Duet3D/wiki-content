---
title: WiFi troubleshooting
description: 
published: true
date: 2025-12-03T17:02:09.258Z
tags: 
editor: markdown
dateCreated: 2021-12-06T00:23:04.224Z
---

# Introduction

If you find that your web browser repeatedly disconnects from the Duet, typically with an AJAX Error message from older versions of DuetWebControl or "The last HTTP request has timed out" from version 1.20 or later, follow these troubleshooting steps. Many of them relate to the WiFi-equipped Duets only, but some are also applicable to Ethernet-equipped Duets.

# Troubleshooting

* Make sure you are running the [latest stable versions](https://github.com/Duet3D/RepRapFirmware/releases) of the main firmware, the WiFi module firmware (DuetWiFiServer), and DuetWebControl.
  * In particular, Duet WiFi Server 1.19.2 and later (which requires DuetWiFiFirmware 1.19.2 too) tries harder to reconnect to your access point if the WiFi connection is lost. You can check which versions you are running on the Settings/General tab of Duet Web Control.
* Make sure you are connecting to a 2.4GHz network. Most routers are able to create both 2.4GHz and 5GHz WiFi connections, but Duet WiFi boards currently only support 2.4GHz connections.
  * Some routers aggressively use 'band steering' to make a WiFi device that is connected to 2.4GHz change to a 5GHz connection, which can generate disconnections. If the Duet initially connects, but is then disconnected, check the router settings and turn off band steering.
* If an AJAX Error is reported, check the Error Reason given at the bottom of the error message box. If it is anything other than Timeout, this indicates mismatched versions of Duet Web Control, or possibly a bug. If you are running the current stable firmware versions, or later beta versions, report the problem on the forum.
* If the disconnection always happens when you switch to the GCode Files page in Duet Web Control, see [Network disconnections after uploading large files](/User_manual/RepRapFirmware/SD_card#network-disconnections-after-uploading-large-files).
* Run `M122` from either the web interface (if you can reconnect) or from USB. In the Network diagnostics, check the **WiFi signal strength**, also called **RSSI**. Values in the range -30 to -50 are good, -50 to -60 is OK, -60 to -70 is marginal. Anything below -70 is weak and likely be unreliable. See below for how to improve RSSI.
* Also in the M122 Network diagnostics check the WiFi Module Reset Reason. It should be "turned on by main processor". If it is anything else (e.g. "exception"), report the problem on the forum.
* In Duet Web Control, in the Settings > Machine specific > Communication section, check that "Maximum number of AJAX retries" is at least 3.
* Are the disconnections related to another device that uses the 2.4GHz band? Typical devices that may cause problems include cordless telephones, baby monitors, and leaky microwave ovens.
* Try changing the channel on your WiFi router. Most routers default to Auto, but can be set to use a fixed channel instead. The channels overlap, so it is common to use channel 1, 6 or 11. This may also help if another device is causing interference.
  * Particularly if using channel 1, change the router WiFi channel. Duets with onboard WiFi antennas may be more susceptible to interference from the Duet itself at this frequency. Try channel 6 or 11.
* Is the connection stable when the printer is idle, but unreliable when printing? If so then there may be too few CPU cycles to service the network interface, because of an excessive step pulse rate.
  * Check the M122 report after a disconnection during a print, or after completing the print, and look at the number of 'hiccups' ('MaxReps' in old firmware versions) figure in the 'DDARing' or 'Move' section of the diagnostics. 
  * This value should be kept below about 50. If it is higher, reduce either microstepping (M350) or maximum speed (M203).
  * Please note, hiccups and MaxReps is reset when you run M122 so only the value the first time you run M122 after a disconnection or completion of a print is significant.
* Also if the connection is stable when the printer is idle but not when printing: could it be temperature-related? Does the disconnection occur when the CPU temperature displayed in DWC reaches a certain value? If so, try cooling the Duet with a fan, if you are not doing so already.
* Do you have a PanelDue connected to the Duet? If so, is the WiFi connection stable if you leave the PanelDue on its Setup page? If so then upgrading the PanelDue to firmware 3.5.1 or later may help.
* If unable to connect to your wireless network at boot up, try deleting all saved wifi networks with `M588 S"*"` (That is, using an asterix for the network name), then re-adding it with M587. For example, `M587 S"networkname" P"password"`.
* You can manually reset the wifi module if you have console access via USB, or a PanelDue. Send `M552 S0`, to disable networking, followed by `M552 S1` to enable networking.
* Consider running the Duet WiFi in access point mode. This way the Duet creates a network which you can join from your wireless enabled device, (eg, PC, tablet, phone, etc.) and connect to the DWC to perform further configuration. See [M589](/User_manual/Reference/Gcodes/M589).
* If you are completely unable to connect, you can remove the SD card and edit the config.g files so that the network setup commands are run at startup.

# How to improve RSSI

* If your printer is largely constructed from metallic components, make sure that the WiFi antenna at the edge of the Duet 2 WiFi is outside of or at the edge of the metallic structure, so that radio waves from your router can reach it unimpeded. 
* Duet 3 Mini 5+ WiFi and some Duet 2 WiFi boards have external antennas. Make sure the antenna is firmly connected to the WiFi module.
* Move the printer and your router closer to each other. Avoid having solid walls between them.
* Try a different WiFi channel (see earlier).
* Consider locating a WiFi repeater, or dedicated 2.4Ghz Wireless G router in the room with the printer.
* Ensure there are no large motors or fans in the area, or wireless telephones, or microwaves, as these can interfere with the 2.4Ghz spectrum.
* It's now possible to replace the ESP-12F WiFi module on the Duet 2 WiFi by an ESP-07S with external antenna, but this is not an easy modification to make unless you are experienced with hot air SMD rework and have the necessary equipment.

# Firmware corruption

* The firmware upload protocol defined by the manufacturer of the WiFi module does not include error detection, and it may appear to succeed but to leave the WiFi module running corrupt firmware sometimes. In this case you can try reflashing the WiFi firmware. Connect via USB and send `M997 S1`.