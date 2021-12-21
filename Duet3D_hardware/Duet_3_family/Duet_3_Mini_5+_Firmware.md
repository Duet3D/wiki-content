---
title: Duet 3 Mini 5+ Firmware
description: The Duet 3 Mini 5 Firmware compatibility and update process is described here.
published: true
date: 2021-12-10T15:37:56.074Z
tags: 
editor: markdown
dateCreated: 2021-07-13T15:40:54.315Z
---

# Introduction

RepRapFirmware version 3.2 and later is required for the Duet 3 Mini 5+ and it is highly recommended to use the [latest stable release](https://github.com/Duet3D/RepRapFirmware/releases). The binary for version 0.4 boards and later is called Duet3Mini5plus.uf2.

# Updating Firmware

The main firmware (RepRapFirmware) is supplied in a .uf2 file. Firmware can be updated by uploading through DWC or via USB, either in stand alone mode or through an attached single board computer. 

The WiFi module firmware is DuetWiFiServer.bin version 1.24 or later.

Some Duet 3 Mini 5+ boards have shipped with a version of the IAP (in-app programmer) that doesn't allow for firmware updates from within DWC. If your board is running a beta version of RRF 3.2, eg RRF 3.2beta3.2+1, make sure the 'Duet3_SDiap_Mini5plus.bin' file is the same size as 'Duet3_SDiap32_Mini5plus.bin' from the RRF 3.2 release. If not, upload 'Duet3_SDiap32_Mini5plus.bin' and rename it without the '32'.

## Updating firmware with DuetWebControl

If you already have access to DWC, you can update the main firmware, WiFi firmware and DWC files by uploading the main zip file from the latest stable RRF release. Upload the zip file to the system tab. Do not extract it first. It should be detected and extracted automatically to the correct locations and prompt you to update and reboot. This is the recommended update method.

The supported web interface is Duet Web Control 3.2 and later. Note the IAP file "Duet3_SDiap_Mini5plus.bin" must be present in the /sys directory (RRF 3.2) or /firmware directory (RRF3.2.2 or later) for the firmware to be updated over the network in stand alone mode.

To update just the firmware (not DWC or WiFi firmware), upload the firmware file Duet3Firmware-Mini5plus.uf2 through the System page of Duet Web Control.

## Updating firmware with gcode command

If the firmware files are already present on the SD card and you wish to re-flash the firmware, you can send M997 from the gcode console either from DWC, PanelDue, or USB terminal. This will initiate the flash procedure.

Note the IAP file "Duet3_SDiap_Mini5plus.bin" must be present in the /sys directory (RRF 3.2) or /firmware directory (RRF3.2.2 or later) for the firmware to be updated over the network in stand alone mode.

To connect to the Duet by USB terminal, connect your PC to the Duet with a USB cable. Identify the COM port presented by the Duet and attach to that port from YAT, Pronterface Octoprint etc. or a terminal emulator on the PC.

## Updating firmware via USB

* Connect the Duet to a PC using a USB cable
* Press the Reset button twice quickly in succession. The Status LED should cycle slowly between dim and bright. This indicates that the bootloader is active.
* A new Mass Storage device should appear on the PC. Identify its drive letter (Windows) or mount path (Linux).
![duet_3_mini_5+_firmware_uf2.png](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_firmware_uf2.png)
* A directory listing of this mass storage device should show these virtual files. File CURRENT.UF2 is the existing firmware on the Duet.
* Copy the new Duet3Firmware-Mini5plus.uf2 file to the mass storage device
* The Status LED will flicker as the firmware is being written, then the Duet will reboot using the new firmware. The mass storage device will disappear from your PC and the Duet 3 COM port will appear.

## Updating firmware with attached Single Board Computer

Use 'sudo apt update' and 'sudo apt upgrade'.

## Updating WiFi module firmware

The procedure for updating the WiFi module firmware is the same as for the Duet 2 WiFi. The procedure for updating it is the same as for the Duet WiFi. That is use the "Upload System Files" function within the system page of DWC or copy it into the sys directory (RRF 3.2) or firmware directory (RRF 3.2.2 or later) and then send the M997 S1 command to install it, either from DWC or over USB

# Prototype hardware

Version 0.2 boards run RepRapFirmware 3.2beta1 or beta2. The WiFi and Ethernet variants use a common firmware binary, named Duet3Mini5plus.uf2 in the 3.2beta 1 release, and Duet3Mini5plus_v02.uf2 in the 3.2beta 2 release. These binaries also support an attached Single Board Computer.

Note that support for version 0.2 prototypes will not be maintained in future firmware, starting from RRF 3.2 stable onwards.

# Programming the bootloader

The bootloader is in protected memory and should not need to be reprogrammed. If you do need to reprogram it, you will need a Windows PC running Atmel Studio, and a SWD programming tool. Suitable tools, in order of increasing price, include:

* SEGGER J-Link EDU Mini (for non-commercial use only)
* Atmel ICE Basic
* SEGGER J-Link BASE

If using the Segger tool, see [this Adafruit page](https://learn.adafruit.com/how-to-program-samd-bootloaders/overview) for an overview of the process.

To connect the programmer to the Duet, you will need a SWD cable breakout board such as [this one](https://www.adafruit.com/product/2743) and a 6-pin JST ZH cable.

You will need to remove the bootloader protection by doing Erase Chip first and then setting USER_WORD_0.NVMCTRL_BOOTPROT to zero. After that you can program the bootloader binary into memory.