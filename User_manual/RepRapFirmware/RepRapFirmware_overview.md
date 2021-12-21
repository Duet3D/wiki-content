---
title: RepRapFirmware overview
description: 
published: true
date: 2021-12-14T15:16:35.733Z
tags: 
editor: markdown
dateCreated: 2021-11-29T15:30:45.435Z
---

<!-- Migrated not updated! -->

# Introduction

RepRapFirmware is a comprehensive motion control firmware intended primarily for controlling 3D printers, but with applications in laser engraving/cutting and CNC too. Unlike most other 3D printer firmwares, it is targeted only at modern 32-bit processors, not outdated 8-bit processors with limited CPU power. So it is designed to make good use of the power of modern inexpensive ARM processors to implement advanced features. 

It is configured with human editable files located on an SD-card plugged into the printer electronics. As such, there is no need for ordinary users to compile the software nor install any development tools.

# Innovation

RepRapFirmware was the first open-source firmware to implement significant advances in 3D printing, including:

* Heater feedforward for changes in extrusion rate (RRF v3.4)
* Heater feedforward for changes in cooling fan RPM (RRF v3.2)
* CAN bus-based distributed control system (Duet 3, RRF v3.0)
* Conditional GCode, object model, daemon task (RRF v3.0)
* Heater power compensated for changes in supply voltage (November 2017)
* Support for SPI-controlled stepper drivers (August 2016)
* Least-squares auto calibration of delta printers (April 2015)
* Segmentation-free delta motion (January 2015)
* Accurate extruder pressure advance, including retraction before the end of a move when needed (December 2014)
* Precise timing of step pulses, even during acceleration (December 2014)
* Support for mixing extruders (July 2014)

# Kinematics supported

RepRapFirmware supports the following machine kinematics:

* [Cartesian](/User_manual/Machine_configuration/Configuration_cartesian)
* [CoreXY](/User_manual/Machine_configuration/Configuration_coreXY), CoreXZ, CoreXYU, CoreXYUV
* [Linear delta](/User_manual/Machine_configuration/Configuration_linear_delta), rotary delta
* [Multiple independent axes (IDEX)](/User_manual/Machine_configuration/Configuration_IDEX)
* Markforged
* Serial SCARA, five-bar parallel SCARA
* Hangprinter
* Polar
* Additionally, RepRapFirmware can support any kinematics for which the movement of each axis is a linear combination of the movement of the motors. The relationship between axis movement and motor movement is defined by a matrix.

# Checking firmware versions

To check the firmware versions in Duet Web Control, go to the Settings page and select the General tab. 

<!-- You should see a box listing the versions of the three firmware files, like this:

[image 289 **UPDATE LINK**]() UPDATE IMAGE 

For production Duet 2 WiFi and Ethernet boards, the minimum recommended version of RepRapFirmware, DuetWifiServer (on the Duet 2 Wifi) and DuetWebControl are:

* Firmware Version (file Duet2CombinedFirmware.bin): 1.21
* WiFi Server Version (file DuetWiFiServer.bin): 1.21
* Web Interface Version (file DuetWebControl.zip): 1.21

ABOVE NOT NECESSARY?

-->

Duet boards are shipped with RepRapFirmware installed however users are advised to check the version installed and, if required, to upgrade to the latest stable version. To upgrade firmware see the [Installing and Updating Firmware](/User_manual/RepRapFirmware/Updating_firmware) page.

## Checking versions when not on the network yet

If you cannot get onto Duet Web Control yet because the network is not set up, then connect using YAT or another terminal program and use [M115](/User_manual/Reference/Gcodes/M115) to get a print out of the firmware versions.

# Where to get firmware, software and drivers

## Duet 3

Duet 3 boards run RepRapFirmware 3; RRF 2 is not compatible with them. If you are running a Duet 3 Mainboard 6HC or Duet 3 Mini 5+ using an attached Single Board Computer (SBC) then the firmware update process is part of updating the Duet Software Framework on the SBC. Please see here for more details: [Getting Started With Duet 3](https://docs.duet3d.com/User_manual/Overview/Getting_started_Duet_3_MB6HC#updating-duet-3-main-board-firmware)

If you are running a Duet 3 Mainboard or Mini in stand alone mode (booting off an SD card and using the attached network) then the process is similar to Duet 2. It is highly recommended to read all the notes on the version of firmware you are looking to upgrade to.

For RRF 3 see the [changelog here](https://github.com/Duet3D/RepRapFirmware/wiki/Changelog-RRF-3.x)

### Duet 3 Mainboard 6HC

* It is recommended that in most cases the **Duet2and3Firmware-3.x.x.zip** is downloaded and then [uploaded via DWC](/User_manual/Reference/Duet_Web_Control_Manual). this will ensure that all the firmware, DWC etc is updates at the same time to compatible versions.
* The main firmware file is **Duet3Firmware_MB6HC.bin** 
* The firmware update loader file stored in /sys on the internal SD card is called **Duet3_SDiap_MB6HC.bin**.

### Duet 3 Mini5+ Wifi or Ethernet

*Note that the Duet 3 Mini 5+ requires RRF 3.2b3 or later*

* It is recommended that in most cases the **Duet2and3Firmware-3.x.x.zip** is downloaded and then [uploaded via DWC](/User_manual/Reference/Duet_Web_Control_Manual). this will ensure that all the firmware, DWC etc is updates at the same time to compatible versions.
* The main firmware file is **Duet3Firmware_Mini5plus.uf2** 
* The firmware update loader file stored in /sys on the internal SD card is called **Duet3_SDiap_Mini5plus.bin**.

## Duet 2

It is highly recommended to read all the notes on the version of firmware you are looking to upgrade to.

For RRF 3.x see the [changelog here](https://github.com/Duet3D/RepRapFirmware/wiki/Changelog-RRF-3.x). For RRF 2.x see the [changelog here](https://github.com/Duet3D/RepRapFirmware/wiki/Changelog-RRF-1.x-&-2.x)

Stable releases of the dc42 build of main firmware and wifi firmware can be found at [RepRapFirmware Releases](https://github.com/Duet3D/RepRapFirmware/releases).

You will also find the appropriate versions of DuetWebControl there. The files to download depend on which Duet you have.

### Duet 2 WiFi

* It is recommended that in most cases the **Duet2and3Firmware-3.x.x.zip** is downloaded and then [uploaded via DWC](/User_manual/Reference/Duet_Web_Control_Manual). this will ensure that all the firmware, DWC etc is updates at the same time to compatible versions.
* The main firmware file is **Duet2CombinedFirmware** for version 1.21 and later ( **DuetWiFiFirmware.bin** up to version 1.20)
* The WiFi module firmware is **DuetWiFiServer.bin**
* The firmware update loader file stored in /sys on the internal SD card is called **Duet2CombinedIAP.bin**.
* With firmware 1.19 and later, the Duet Web Control files are stored on the SD card in the /www directory
* With firmware version 1.18.2 and earlier, the Duet Web Control files were not stored on the internal SD card. Instead they were uploaded to the WiFi module from file **DuetWebControl.bin**

### Duet 2 Ethernet

* It is recommended that in most cases the **Duet2and3Firmware-3.x.x.zip** is downloaded and then [uploaded via DWC](/User_manual/Reference/Duet_Web_Control_Manual). this will ensure that all the firmware, DWC etc is updates at the same time to compatible versions.
* The main firmware file is **Duet2CombinedFirmware** for version 1.21 and later ( **DuetEthernetFirmware.bin** up to version 1.20)
* The web interface is **DuetWebControl.zip**
* The firmware update loader file stored in /sys on the internal SD card is called **Duet3_SDiap_MB6HC.bin**.

### Duet 2 Maestro

* The main firmware is **DuetMaestroFirmware.bin**
* The web interface is **DuetWebControl.zip**
* The firmware update loader file stored in /sys on the internal SD card is called **DuetMaestroIAP.bin**.

## Drivers & helper files

* The firmware update helper file (iap, or in-app programmer) is available alongside the main firmware release files at [https://github.com/Duet3D/RepRapFirmware/releases](https://github.com/Duet3D/RepRapFirmware/releases).
* The Windows device driver files which may be required if you want to communicate with the Duet over USB from your PC are available at [https://github.com/Duet3D/RepRapFirmware/tree/dev/Driver](https://github.com/Duet3D/RepRapFirmware/tree/dev/Driver)
* If you need to re-install the main firmware via USB, you will need Atmel's SAM-BA version 2.17 from [Microchip](http://aem-origin.microchip.com/en-us/development-tool/SAM-BA-IN-SYSTEM-PROGRAMMER).
* Alternatively you can install main firmware via USB using Bossa 1.8 from [ShumaTech](http://shumatech.com/web/products/bossa)

# Installing and Updating Firmware

See the wiki page on [Installing and Updating Firmware](/User_manual/RepRapFirmware/Updating_firmware).

# SD card structure

See the wiki page on the [SD card](/User_manual/RepRapFirmware/SD_card).

# Firmware differences from other firmwares

The Duets run RepRapFirmware. This differs from Marlin, Repetier and other firmwars in a number of ways.

* You do not need to build the firmware yourself unless you are a firmware developer. The firmware is supplied as a ready-built binary file. All important configuration parameters are defined within the /sys/config.g file on the on-board SD card, which you can edit either in the web interface, or by moving the on-board SD card to a PC and using an ordinary text editor such as Notepad++. You can also change these parameters on the fly by sending the appropriate G-codes.
* The homing behaviour is defined in homing files also on the SD card. Bed probing behaviour is defined in the bed.g file on the on-board SD card. For more details, see [Configuring RepRapFirmware for a Cartesian Printer](/User_manual/Machine_configuration/Configuration_cartesian)
* PID auto tune works differently. The firmware models each heater as a first order process with dead time, and measures the corresponding model parameters for your heater. It uses these parameters to compute two sets of PID values. One is designed to minimise overshoot when heating up to operating temperature. The other is used to give faster response to changes in load, for example when a print cooling fan is turned on or the extrusion rate is changed. The model parameters are also used to compute the expected behaviour of your heater, so that the firmware can better detect deviations from this behaviour that could be symptomatic of dangerous heater faults.
* For delta printers, fast auto delta calibration is provided using a least-squares algorithm. The process takes around 30 seconds or less, making it practical to run at the start of every print if you wish.
* Firmware updates are normally done over the web interface, with USB used only as a backup.
* Nozzle heaters are controlled differently. With RepRapFirmware, you associate heaters and extruder drives to tools (you can have more than one of each used by a tool if you like), you assign active and standby temperatures to each tool's heaters, then you activate the tool. You can have several tools that share the same heaters and extruder drives. Other firmwares have a much more primitive concept of tools and typically consider each heater to be a tool.
* Marlin and Reptier often use **mm/s** for speed values. RepRapFirmware uses **mm/min**. Be careful when switching between the two. To convert from one to the other you will either need to multiply or divide by 60. Example 100mm/s = 6000mm/min. 

See also [Adapting an existing printer to Duet](/User_manual/Overview/Adapting)

# Known firmware issues and limitations

Version 1.19 firmware used with DuetWebControl 1.19 had the following known issues and limitations:

* Connecting to the Duet 2 WiFi by name only works if your client device supports mDNS protocol (e.g. Apple Bonjour service). Otherwise you have to connect to it by IP address.
* The Duet 2 Ethernet and Duet 2 Maestro do not yet support mDNS, please connect to the IP address.
* You cannot change microstepping on the fly during a print, because you must re-home the printer after changing microstepping.
* The diagnostic LED is not currently used. In future it will be used to indicate progress of firmware updates, and for other purposes.

# Printing over USB connection (Octoprint, Cura, Simplify3D, Etc.)

The Duet control board supports printing your gcode files over a USB connection streamed by a gcode sender software like those mentioned in the title. However, there are limitations, including, but not limited to:

* Print statistics, such as remaining time estimation, current layer etc, not showing.
* Pause print will have to be handled by the GCode sender, and will take longer because all queued moves will have to complete before the pause takes place.
* Filament sensors will not function.
* Resume after power fail will not function.
* Print speed may be limited because many GCode senders can't supply data fast enough over USB, so print time may be extended.

It is strongly recommended that you use the Duet Web Control for machine control and the internal SD card for gcode printing, however, if your application requires printing over USB, such as using the Mosaic Palette, it will work as expected, with the above limitations.