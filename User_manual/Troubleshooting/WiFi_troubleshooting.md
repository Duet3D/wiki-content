---
title: WiFi troubleshooting
description: 
published: true
date: 2026-10-30T12:36:28.355Z
tags: 
editor: markdown
dateCreated: 2021-12-06T00:23:04.224Z
---

# Introduction

If you find that your web browser repeatedly disconnects from the Duet, follow these troubleshooting steps. Many of them relate to the WiFi-equipped Duets only, but some are also applicable to Ethernet-equipped Duets. The first step also covers a Duet whose WiFi module connects and reports an IP address, but which the browser cannot reach.

# Troubleshooting

* If the WiFi module connects and reports an IP address, but the browser cannot connect at all, check that HTTP is enabled. In RRF 3.7 and later HTTP is not enabled by default, and a config.g made for RRF 3.6 or earlier does not enable it. Send [M586](/User_manual/Reference/Gcodes/M586) over USB to see which protocols are enabled. Send `M586 P0 S1` to enable HTTP, and add it to config.g. On a Duet 3 Mainboard 6HC with the optional WiFi module, add `I1` to both commands.
* Make sure you are running the [latest stable versions](https://github.com/Duet3D/RepRapFirmware/releases) of the main firmware, the WiFi module firmware, and Duet Web Control. RRF 3.7 and later do not support Duet 2. The last supported version is 3.6.x for Duet 2 WiFi and Ethernet, and 3.5.x for Duet 2 Maestro.
  * Send [M122](/User_manual/Reference/Gcodes/M122) to check which versions you are running. The main firmware version is at the top of the report, and the WiFi module firmware version is in the WiFi section. See [Installing and updating firmware](/User_manual/RepRapFirmware/Updating_firmware) for the WiFi firmware file that your board uses.
* Make sure you are connecting to a 2.4GHz WiFi network. Most WiFi routers are able to create both 2.4GHz and 5GHz WiFi connections, but the WiFi modules on Duet boards support 2.4GHz connections only.
  * Some routers aggressively use 'band steering' to make a WiFi device that is connected to 2.4GHz change to a 5GHz connection, which can generate disconnections on the Duet. If the Duet initially connects, but is then disconnected, check the router settings and turn off band steering.
* If the disconnection always happens when you switch to the Jobs page in Duet Web Control, see [Network disconnections after uploading large files](/User_manual/RepRapFirmware/SD_card#network-disconnections-after-uploading-large-files).
* Run `M122` from either the web interface (if you can reconnect) or from USB. In the WiFi section, check the **Signal strength** value, also called **RSSI**. Values in the range -30 to -50 dBm are good, -50 to -60 is OK, -60 to -70 is marginal. Anything below -70 is weak and likely be unreliable. See below for how to improve RSSI.
* Also in the WiFi section of the M122 report, check the **Module reset reason**. If it is "Exception", "Hardware watchdog", "Software watchdog" or "Brownout", report the problem on the forum.
* In Duet Web Control, check that "Maximum HTTP retries" in the Communication settings is at least 3. In DWC 3.6 and earlier this setting is called "Number of maximum AJAX retries", under Settings > Machine-Specific > Communication.
* Are the disconnections related to another device that uses the 2.4GHz band? Typical devices that may cause problems include cordless telephones, baby monitors, and leaky microwave ovens.
* Try changing the channel on your WiFi router. Most routers default to Auto, but can be set to use a fixed channel instead. The channels overlap, so it is common to use channel 1, 6 or 11. This may also help if another device is causing interference.
  * Particularly if using channel 1, change the router WiFi channel. Duets with onboard WiFi antennas may be more susceptible to interference from the Duet itself at this frequency. Try channel 6 or 11.
* Is the connection stable when the printer is idle, but unreliable when printing? If so then there may be too few CPU cycles to service the network interface, because of an excessive step pulse rate.
  * Check the M122 report after a disconnection during a print, or after completing the print, and look at the 'hiccups added' figure in the 'Move' section of the diagnostics. 
  * This figure has two values. The second value is the number of hiccups that the step interrupt added, and it should normally be zero. If it is not zero, reduce microstepping (M350) or the maximum speed (M203).
  * Please note, the hiccups figure is reset when you run M122 so only the value the first time you run M122 after a disconnection or completion of a print is significant.
* Also if the connection is stable when the printer is idle but not when printing: could it be temperature-related? Does the disconnection occur when the MCU temperature reaches a certain value? M122 reports the MCU temperature. If so, try cooling the Duet with a fan, if you are not doing so already.
* Do you have a PanelDue connected to the Duet? If so, is the WiFi connection stable if you leave the PanelDue on its Setup page? If so then make sure the PanelDue runs the latest PanelDue firmware.
* If unable to connect to your wireless network at boot up, try deleting all saved wifi networks with `M588 S"*"` (That is, using an asterisk for the network name), then re-adding it with M587. For example, `M587 S"networkname" P"password"`.
* You can manually reset the wifi module if you have console access via USB, or a PanelDue. Send `M552 S0`, to disable networking, followed by `M552 S1` to enable networking. On a Duet 3 Mainboard 6HC with the optional WiFi module, add `I1` to both commands.
* Consider running the WiFi module in access point mode. This way the Duet creates a network which you can join from your wireless enabled device, (eg, PC, tablet, phone, etc.) and connect to the DWC to perform further configuration. Configure the access point with [M589](/User_manual/Reference/Gcodes/M589), then send `M552 S2` to start it.
* If you are completely unable to connect, you can remove the SD card and edit the config.g files so that the network setup commands are run at startup.

# WiFi scan

If your Duet board is running RRF 3.5 or later with WiFi firmware 2.0 or later (send M122 to check), you can run a WiFi network scan with [M587.1](/User_manual/Reference/Gcodes/M587_1). It's best to connect via a serial terminal (eg YAT) via USB, as during the scan, any existing WiFi connection will be lost until the scan is complete. The scan should only take a couple of seconds to complete, and then send [M587.2](/User_manual/Reference/Gcodes/M587_2) to see the results. This will show the WiFi networks that the Duet can see.

# How to improve RSSI

* If your printer is largely constructed from metallic components, make sure that the WiFi antenna is outside of or at the edge of the metallic structure, so that radio waves from your router can reach it unimpeded. On the Duet 2 WiFi with the on-board antenna, the antenna is at the edge of the board.
* Some Duet 2 WiFi and Duet 3 Mini 5+ WiFi boards have external antennas. Make sure the antenna is firmly connected to the WiFi module.
* Move the printer and your router closer to each other. Avoid having solid walls between them.
* Try a different WiFi channel (see earlier).
* Consider locating a WiFi repeater, or a dedicated 2.4GHz access point, in the room with the printer.
* Ensure there are no large motors or fans in the area, or wireless telephones, or microwaves, as these can interfere with the 2.4GHz spectrum.
* It's now possible to replace the ESP-12F WiFi module on the Duet 2 WiFi by an ESP-07S with external antenna, but this is not an easy modification to make unless you are experienced with hot air SMD rework and have the necessary equipment.

# Firmware corruption

* The WiFi firmware upload checks each block as it is sent, but does not verify the firmware after it has been written to the WiFi module. An upload can therefore appear to succeed but leave the WiFi module running corrupt firmware. In this case you can try reflashing the WiFi firmware. Connect via USB and send `M997 S1`.