---
title: Installing and Updating PanelDue Firmware
description: This page describes how to update the PanelDue Firmware.
published: true
date: 2022-04-26T13:22:11.503Z
tags: 
editor: markdown
dateCreated: 2021-11-30T13:21:58.270Z
---

![paneldue_5i_top.png](/hardware/paneldue/paneldue_5i_top.png =500x)

# Introduction

This page describes how to update the PanelDue Firmware.

* From RepRapFirmware version 3.2 (beta 4.1), you can flash the PanelDue firmware from the Duet itself, provided you have a V3/5i/7i PanelDue. 
* For earlier RepRapFirmware versions, and earlier PanelDue, flash the firmware via the USB connector on the PanelDue.

# Download PanelDue firmware

## Check current PanelDue firmware version

With the PanelDue connected to the Duet, and powered up, click on "Setup" on the display. Current firmware version is at the top of the 'Setup' screen.

## PanelDue firmware versions

For all versions of RepRapFirmware up to RRF 3.1.1, and all versions of PanelDue, use [PanelDue firmware version 1.25.0](https://github.com/Duet3D/PanelDueFirmware/releases/tag/1.25.0)

From RepRapFirmware 3.1.1, late V2/V3/5i/7i PanelDue can use the improved [PanelDue firmware version 3.2.x and later](https://github.com/Duet3D/PanelDueFirmware/releases/). 
Unfortunately, due to the lack of RAM, these versions will not run on V1 or early V2 PanelDue boards that use the ATSAM3S2B chip. See [PanelDue firmware notes](https://github.com/Duet3D/PanelDueFirmware/blob/master/whatsnew.md) for details.

## Find the correct file

Locate and download PanelDue firmware for your display from [Duet3D PanelDue Github](https://github.com/Duet3D/PanelDueFirmware/releases).

The following table shows the filenames for the PanelDue Firmware 3.4 release.

| PanelDue version | Screen size | Firmware file (Duet3D logo) | Firmware file (no logo) |
|:---|:---|
| PanelDue 5i | 5" | PanelDueFirmware-logo-3.4.0-5.0i.bin | PanelDueFirmware-3.4.0-5.0i.bin |
| PanelDue 7i | 7" | PanelDueFirmware-logo-3.4.0-7.0i.bin | PanelDueFirmware-3.4.0-7.0i.bin |
| PanelDue v3.0 | 4.3" | PanelDueFirmware-logo-3.4.0-v3-4.3.bin | PanelDueFirmware-3.4.0-v3-4.3.bin |
| ^^ | 5" | PanelDueFirmware-logo-3.4.0-v3-5.0.bin | PanelDueFirmware-3.4.0-v3-5.0.bin |
| ^^ | 7" | PanelDueFirmware-logo-3.4.0-v3-7.0.bin | PanelDueFirmware-3.4.0-v3-7.0.bin |
| ^^ | 7"C | PanelDueFirmware-logo-3.4.0-v3-7.0c.bin | PanelDueFirmware-3.4.0-v3-7.0c.bin |
| PanelDue v1.0, v1.1, v2.0 | 4.3" | PanelDueFirmware-logo-3.4.0-v2-4.3.bin | PanelDueFirmware-3.4.0-v2-4.3.bin |
| ^^ | 5" | PanelDueFirmware-logo-3.4.0-v2-5.0.bin | PanelDueFirmware-3.4.0-v2-5.0.bin |
| ^^ | 7" | PanelDueFirmware-logo-3.4.0-v2-7.0.bin | PanelDueFirmware-3.4.0-v2-7.0.bin |
| ^^ | 7"C | PanelDueFirmware-logo-3.4.0-v2-7.0c.bin | PanelDueFirmware-3.4.0-v2-7.0c.bin |

## Choosing the correct firmware binary

The naming scheme is:
PanelDueFirmware\[-\<ext>]-\<major>.\<minor>.\<patch>\[-\<suffix>]-\<hardware>.bin

ext - optional name extension; usually blank, 'logo' or 'nologo' (see note below)
major - major version
minor - minor version
patch - patchlevel version
suffix - optional suffix version, specifies pre-release, rc versions
hardware - hardware version

Examples are PanelDueFirmware-3.4.1-pre1-5.0i.bin or PanelDueFirmware-logo-3.4.1-pre1-7.0i.bin.

For the 5" and 7" integrated versions of PanelDue, use file PanelDue firmware versions ending in -5.0i.bin or -7.0i.bin.

For versions of PanelDue with a separate controller board that plugs into the back of the display, choose the file name with -v3- in it if you have a version 3 PanelDue controller, or the one with -v2- in it if you have a version 1 or 2 PanelDue controller, and the file name that ends with the screen size you are using.

The files ending in -7.0c are for 7" displays using a CPLD controller (not sold by Duet3D or their distributors).

## Logo and nologo versions

From PanelDue firmware version 3.4, firmware filenames that have 'logo' in the name, eg PanelDueFirmware-logo-3.4.0-5.0i.bin, have a Duet3D logo splash screen on startup. Firmware filenames that have do not have 'logo' have no logo. 

For PanelDue firmware versions up to 3.3, firmware filenames that DO NOT have logo in the name have the Duet3D splash screen. Firmware filenames that have 'nologo' in them have... no logo.

You can create your own splash screen images; see readme on the [PanelDue firmware GitHub page](https://github.com/Duet3D/PanelDueFirmware#splash-screen) for instructions.

# Firmware update via Duet

From RepRapFirmware version 3.2 (beta 4.1), you can flash the PanelDue firmware from the Duet itself, provided you have a V3/5i/7i PanelDue running firmware 3.2.2 or later

## Requirements

* Only works for V3/5i/7i PanelDue running firmware versions 3.2.2 or later 
* Filename must be renamed to PanelDueFirmware.bin (future versions of DWC will rename uploaded PanelDue firmware files automatically), and reside in 0:/firmware (RRF 3.3 and later) or 0:/sys (RRF 3.2)
* Flashing requires a baud rate of 115200. RRF will automatically change to this baud rate (and back at the end) if it is a different value but the wiring must provide stable communication at that speed
* Automatic erase and reset is only supported from PanelDueFirmware 3.2.2 (earlier versions require manual erase and reset)

## Update firmware

Upload the PanelDue firmware file to the Duet via the System > Upload system files button in Duet Web Control. Check it is in the Duet's '0:/firmware' (RRF 3.3 and later) or '0:/sys' (RRF 3.2) folder. If necessary, rename the firmware file (not the .zip file download)  to 'PanelDueFirmware.bin'.

To update the PanelDue firmware, send:

`M997 S4 P"PanelDueFirmware-3.4.0-pre3-v3-7.0.bin"`

Your Duet will attempt to Erase the PanelDue flash memory, and upload the PanelDue firmware. If you are updating from an old PanelDue version, you may need to press the 'erase' button on the PanelDue, followed by the 'reset' button first.

The Duet will upload and verify the firmware, then reset the PanelDue. You may need to manually reset the PanelDue, if it does not reset automatically.

# Firmware update via USB

If your Duet is running versions of RepRapFirmware older than RRF 3.2, you will need to update the PanelDue firmware via its USB port, by connecting it to a PC. Download the version

## Requirements

* Download and install Bossa 1.9.1 from [ShumaTech](http://www.shumatech.com/web/products/bossa). Versions of bossa or bossac earlier than 1.8 will not be able to program the version 3.0 PanelDue board. Apple Macintosh users, please see [this forum thread](https://forum.duet3d.com/topic/11445/flashing-firmware-on-mac-os-x)
* Download and unzip the appropriate PanelDue firmware file from the sources listed above.

## Connect the PanelDue to the host computer

* Power off the Duet, and disconnect the PanelDue from the Duet, both the ribbon cable and 4-wire interface cable, otherwise the Duet will receive power when USB power is applied to the PanelDue. If the back of the PanelDue is inaccessible, disconnect it at the Duet end, but check that the free end of the 4-wire interface cable cannot short against anything.
* Use a suitable cable to connect the micro USB connector on the PanelDue controller to a USB port on your PC. Caution! With version 1.0 and 1.1 PanelDue boards, take care not to exert too much sideways or up/down force on the USB connector, or you may pull the USB connector off the board. If the board has already been programmed then the backlight should illuminate.

## Erase firmware

* Press and hold the Erase button of the PanelDue for at least one second. You can access this button by pressing the end of a straightened-out paper clip through the hole in the enclosure.
* Release the Erase button, then press and release the Reset button. If you have a version 2.0 board then the backlight will turn off.

## Update firmware using Bossa GUI application

The easiest way to update the firmware is using the Bossa application.

![paneldue_firmware_flashing.jpg](/hardware/paneldue/paneldue_firmware_flashing.jpg =550x)

* Identify the COM port number or port name of the PanelDue board on your PC.
  * **Windows:** Click Start->Control Panel->System->Device Manager. Expand Ports (COM and LPT), and look for Bossa port. Make a note of the port number. 
  * **Mac OS:** Open a Terminal window and type `ls /dev/tty.*` The response will be something like:
  `/dev/tty.Bluetooth-Incoming-Port    /dev/tty.usbmodem14101` 
  where `/dev/tty.usbmodem14101` is the PanelDue.
* If you can’t find the port, try repeating the Erase and Reset sequence, or disconnecting and reconnecting the USB cable, or try a different USB port on your computer. Make sure the USB cable you are using is a USB data cable; some USB cables only provide power. Note: the port number of the Bossa port for PanelDue will not be the same as the one you use when uploading firmware to Duet electronics.
* Open BOSSA application, select PanelDue COM port from 'Serial Port' drop down list, click 'Browse' to select firmware to upload to your PanelDue (extracted from the downloaded firmware zip file).
* You **MUST** check the **Erase all**, **Lock**, and **Boot from flash** boxes.
* Click 'Write' to send firmware to PanelDue. Once it has written, you can check it by clicking 'Verify'.
* Close BOSSA application, press reset on the PanelDue to check the firmware is functioning. Note: if the panel remains dark and doesn't respond you likely have an incorrect version of the firmware for your panel.

## Update firmware from command line/console

If you are unable to use the BOSSA application, try the command line tool.

* Identify the COM port number or port name of the PanelDue board on your PC.
  * **Windows:** Click Start->Control Panel->System->Device Manager. Expand Ports (COM and LPT), and look for Bossa port. Make a note of the port number. 
  * **Mac OS:** Open a Terminal window and type `ls /dev/tty.*` The response will be something like:
  `/dev/tty.Bluetooth-Incoming-Port    /dev/tty.usbmodem14101` 
  where `/dev/tty.usbmodem14101` is the PanelDue.
* If you can’t find the port, try repeating the Erase and Reset sequence, or disconnecting and reconnecting the USB cable, or try a different USB port on your computer. Make sure the USB cable you are using is a USB data cable; some USB cables only provide power. Note: the port number of the Bossa port for PanelDue will not be the same as the one you use when uploading firmware to Duet electronics.
* Use bossac to program the board. Under Windows, open a command prompt and use this command:
`"C:\Program Files\BOSSA\bossac.exe" --port=COM9 -e -w -v -b PanelDueFirmware.bin`

* Replace COM9 in this command by the COM port number you found, and PanelDueFirmware.bin by the path to your downloaded firmware file.
* When bossac has completed and reported “Verify successful”, press the Reset button again. If you have successfully installed firmware that is correct for the type of display you are using, the display should be legible.
* Disconnect the USB cable.

# Notes

* On version 2.0 and earlier boards, whenever new firmware is uploaded, the touch panel calibration and touch pad orientation is lost. Therefore, on starting up the software enters the touch panel calibration phase. Touch the spots as instructed on the screen as accurately as possible. It is more accurate to use the tip of your fingernail or a stylus (print one!) rather than the pad of your finger. If the display is upside down or inverted left-right, you can correct it after touch calibration using the options provided on the Setup page.
* The PanelDue controller board is supplied as standard with firmware for driving a 4.3 inch 480 x 272 pixel display. If you use a different display, then you will need to re-program the board with firmware for that display. There may be more recent firmware available even if you are using the recommended display.
* If you have a PanelDue with a 7 inch TFT panel, then some PC USB ports and especially laptop USB ports may not be able to supply enough power to the PanelDue when the backlight is on. If you press Erase and Reset while the PanelDue it powered from your controller electronics, then the backlight should turn off, which should resolve the problem. Or you can disconnect the PanelDue control board from the TFT panel (unless you are using the integrated version), then upgrade the firmware, then reconnect it.
* After you have connected the PanelDue to your 3D printer, you can still update the firmware using the same procedure. If you have a version 3.0 PanelDue then you must either disconnect it from your 3D printer first or power up your 3D printer. Version 2.0 and earlier can be updated without disconnecting them from your printer electronics even if the printer is not powered.