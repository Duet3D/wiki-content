---
title: What to do if your Duet won't respond
description: 
published: true
date: 2025-03-19T15:13:18.039Z
tags: 
editor: markdown
dateCreated: 2021-12-05T22:52:16.731Z
---

# Check the voltage indication LEDs

* If you have a PanelDue, disconnect it from the Duet to reduce the load on the 5V supply. 
* If you have a Single Board Computer (SBC) connected, disconnect the ribbon cable.
* Connect your Duet to a PC using the USB cable. Do not apply VIN power to the Duet at this point.
* Check the LEDs on the Duet:
  * **Duet 3 MB6HC**: There should be a red LED illuminated between the USB connector and SD card socket. The red 5V LED at the edge of the board should also be lit, so should the green 3.3V LED.
  * **Duet 3 Mini 5+**: The red 5V LED at the edge of the board should be lit, so should the green 3.3V LED.
  * **Duet 2**: There should be a red LED illuminated between the USB connector and the Reset button, which indicates 5V power via USB. The red 5V LED at the edge of the board should also be lit, so should the green 3.3V LED.
* If the red LED(s) are lit but not the green 3.3V LED then most likely there is a short between the 3.3V power rail and ground, or the 3.3V voltage regulator has failed. Disconnect the Duet from USB, then remove all endstop and Z probe connectors from the Duet. Duet 2: If you have a DueX2 or DueX5 connected as well, disconnect the ribbon cable joining them too. Then reconnect the USB cable and check the LEDs again.
* The DIAG LED may or may not be lit, but can help diagnosis:
  * **Duet 3 MB6HC**: DIAG LED is between the reset switch and the SD card socket 
  * **Duet 3 Mini 5+**: STATUS LED is between the reset switch and the corner of the PCB 
  * **Duet 2**: DIAG LED is between the USB connector and the SD card socket 
* On **Duet 3**, in normal operation:
  * the DIAG/STATUS LED flashes steadily, about half a second off and half a second on.
  * If the firmware has been erased the DIAG/STATUS LED will glow permanently (ie not full brightness).
  * on Duet 3 Mini 5+, if the STATUS LED flashes three times, then off for a while, Firmware CRC check has failed (reflash the firmware)
* On **Duet 2**, in normal operation:
  * the DIAG LED will turn on as power is applied, then turn off as soon as the firmware starts loading, usually less than 0.5 second.
  * In normal operation, it will turn back on if the probe type is set to M558 P1 in config.g. Removing the SD card and resetting should switch it off. Apart from this case, the DIAG LED should normally be off.
  * Errors in config.g may cause the Duet to get stuck in a boot loop, which will cause the DIAG LED to be on permanently. Remove SD card and reset.
  * If the firmware has been erased the DIAG LED will be on permanently. 
* If the reset button is stuck in (rare), or has been mounted so it is pushed in by an enclosure (also check connected PanelDue/12864 displays for this, by disconnecting them), the Duet will get stuck in a boot loop, which will cause the DIAG LED to be on permanently. Check there is clearance around the reset button, or use some tweezers to gently pull the button out.
* If no LEDs are lit, then most likely your PC is not supplying power through the USB cable. Try a different cable and a different USB port. If necessary, use a powered USB hub.
* Remove the SD card, as a corrupt or incorrectly configured config.g file can cause the firmware to continually reboot. There have also been occasions where the SD card causes a short circuit.

# Connect to a PC via USB and look for the port

* If the LEDs are correctly lit, go to your PC and see whether a new COM port appeared when you plugged in the USB cable. Under Windows you do this by loading Device Manager, then expand **Ports - COM and LPT**. 
* If a port called **Duet 3D Printer Electronics** is shown, then you should be able to connect to your Duet using YAT, Pronterface or another host program or terminal emulator program. Proceed to update the main firmware or the WiFi firmware if you need to.
* If a port called **Bossa port** is shown then the firmware has been erased. Perhaps you pressed the Erase button. You need to re-install firmware via USB. See the "Fall-back procedure #2" section in [Installing and Updating Firmware](/User_manual/RepRapFirmware/Updating_firmware).
* If an **Unknown Device** is shown (but only when the Duet is connected) and you are running Windows, install the Windows device driver for the Duet.
* If no port appears, or you get a Windows error message several seconds after you connect the Duet via USB, try connecting it to a different USB port, and try using a different USB cable, making sure that the USB cable is a data cable (some USB cables provide power only). 
* Windows 10 sometimes gets individual USB ports stuck and pops up an "Error reading device descriptor" message when you connect devices to them. If this happens, use another port, or reboot Windows.

# If the port still doesn't appear on the PC

* If neither port is shown, then the Duet may be faulty or it may not have valid firmware installed. With USB power applied, erase the firmware. **Only erase the firmware if your board is completely unresponsive, or if instructed to do so by Duet3D.** On older boards do this by holding down the Erase button for at least 1 second, then press the Reset button. On newer boards there is no Erase button; so fit a jumper across the Erase pins, then connect to USB for a few seconds, then disconnect from USB, remove the jumper and connect to USB again. You may have to do this a couple of times if the Duet does not appear in the Device Manager.
* The Bossa Port should appear in Device Manager; if it does then you can upload firmware using SAM-BA or Bossa as described in section "Fall-back procedure #2" in [Installing and Updating Firmware](/User_manual/RepRapFirmware/Updating_firmware).
* If all the above steps fail and your Duet is still under warranty, contact your supplier (if you purchased your Duet direct from Duet3D then follow [the warranty return process](https://www.duet3d.com/page/warranty-policy))