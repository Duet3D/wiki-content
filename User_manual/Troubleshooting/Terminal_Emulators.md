---
title: Terminal Emulators
description: There is no requirement to connect to your Duet hardware over USB for normal operation however for certain trouble shooting steps the use of a terminal emulator is required. This page provides some information on getting connected with one.
published: true
date: 2025-07-07T13:43:00.688Z
tags: yat, terminal emulator, troubleshooting
editor: markdown
dateCreated: 2021-10-31T18:10:59.107Z
---

# Introduction

It is not necessary to use USB with a Duet board for normal operation, however there are some configuration and troubleshooting instructions that require connecting to the Duet over USB, for example when setting up networking, or when connecting over the network is not working for some reason.

Operating systems may have a terminal emulator support built in. In Windows it is necessary to install a terminal emulator. MacOS and most Linux distributions have one built in, however a program with a GUI will make using it easier for people who prefer GUIs.

# Port settings

## Finding the port name

To find the port name, plug the Duet into your computer with a USB cable. Check that the Duet is seen on a port:
* **Windows**: Check a USB device appears as a `COM[number]` port in the Device Manager under Ports (COM & LPT).
* **macOS**: Open a Terminal window and send `ls /dev/tty.*`. The Duet uses a port with USB in the name, e.g. `/dev/tty.usbmodem1411`.
* **Linux**: Open a Terminal window and send `lsusb`, which should show an entry for the Duet. For the Serial Port name, send `ls /dev/tty*` which should show an entry `/dev/ttyACM[number]`. This is the port the Duet is using.

## Settings

Terminal Emulators require you to configure the settings to connect to the Duet. 

| Description | Setting |
|---|---|
|**Port** | Where the cable from the Duet is plugged in to your PC.<br>Windows: COM port<br>macOS: usbmodem#### port<br>Linux: /dev/ttyACM0 |
**Baud rate/Bits per second** | Set between 9600 and 115200
**Data Bits** | 8
**Parity** | None
**Stop bits** | 1
**Flow Control** | See note below
**Control Pins/Other settings** | Make sure DTR is enabled 

**NOTE**: In RepRapFirmware v3.6.0 and later, DTR needs to be enabled, as the USB driver has changed to tinyusb. Most terminal emulators have a button or checkbox to enable DTR, or do it automatically, but some require 'Flow control' to be set to 'Hardware'. On YAT, doing this also enables RTS, which then stops YAT communicating with the Duet. So only enable DTR. On macOS and Linux, Minicom and Screen also enable DTR automatically.
For earlier versions of RepRapFirmware, it does not matter how DTR is set. Leave Flow Control as 'None'.

# Windows

There are many terminal emulators available for windows, the simplest and most reliable we have found is Yet Another Terminal (YAT). Other options include PuTTY, CuteCom, CoolTerm, RealTerm, as well as many others. Check that they can send upper and lower case letters, as this may be necessary to set the WiFi SSID and password.

## YAT - terminal emulator for Windows ##

You can install YAT from [Sourceforge here](https://sourceforge.net/projects/y-a-terminal/)

### Line endings ###

With its standard setup the line endings are not set correctly for Duet. This page shows how to set the line endings correctly.

[![Image showing the YAT terminal emulation program with incorrect line endings set and a single long line of text with \<LF> inserted within the single line](/manual/troubleshooting/yat1.png =50%x){.align-right}](/manual/troubleshooting/yat1.png){target=_blank}There are different standards for how to end a line - see [Wikipedia](https://en.wikipedia.org/wiki/Newline) for more information. Setting them wrong in a terminal emulation program leads to a long single line (which may be too long for the terminal):
<p style="clear:both"></p>

[![Image showing the YAT terminal with correct line endings so the line end when they should](/manual/troubleshooting/yat2.png =50%x){.align-right}](/manual/troubleshooting/yat2.png){target=_blank}Setting them right gets the response you would expect:

#### Setting line endings in YAT
  
[![Image showing the Terminal Menu in YAT](/manual/troubleshooting/yat3.png =200x){.align-right}](/manual/troubleshooting/yat3.png){target=_blank}The Duet uses \<LF> for line endings  (Unix like). To set them in YAT go to ***Terminal*** in the top menu bar then ***Settings***:
<p style="clear:both"></p>

[![Image showing the Text Terminal Settings Dialog box in YAT](/guides/getting_connected/03_connect_to_duet_win_03.png =50%x){.align-right}](/guides/getting_connected/03_connect_to_duet_win_03.png){target=_blank}Then select **Text Settings** and change the EOL sequence selection to **\<LF>**

<!-- Old image, old YAT version
![Image showing the Text Terminal Settings Dialog box in YAT](/manual/troubleshooting/yat4.png =500x)
-->

### COM Port

[![Image showing the Terminal Settings Dialog box in YAT](/guides/getting_connected/03_connect_to_duet_win_02.png =50%x){.align-right}](/guides/getting_connected/03_connect_to_duet_win_02.png){target=_blank}Unless you have other serial devices connected to your machine, other than the Duet, there will probably only be one COM port available for selection in YAT.

Select the correct COM port under **Serial Port**. 
If unsure of the COM port number, press **Windows Key + R** on your keyboard and type `devmgmt.msc`. Your Duet will be listed under Ports (COM & LPT) as USB Serial Device (COM#) or similar. Use COM# as the Serial Port in YAT.

For newer versions of RepRapFirmware (3.6 and later), on Duet 3 boards, the terminal emulator must raise the DTR line, otherwise RRF will not transmit data. Select "DTR on". 

Older versions of YAT don't have this option, but DTR can be enabled on the connection screen. For older versions of RRF, it doesn't matter if this is enabled or disabled. 

The rest of the default values, eg for Bits per second etc, should work. Click OK to close the settings window.

<!-- Old image, old YAT version
![Image showing the Terminal Settings Dialog box in YAT](/manual/troubleshooting/yat5.png =500x)
-->

Then click the green tick to connect (or ***Terminal*** menu then ***Open/Start***).

If the COM port is not available, check your USB connections, and try a different cable and port. For more troubleshooting, see [Installing and Updating Firmware - Fallback procedure #2](https://docs.duet3d.com/User_manual/RepRapFirmware/Updating_firmware#fallback-procedure-2)
  
# macOS

Cutecom is also available for macOS.

### CoolTerm

[![CoolTerm](/guides/getting_connected/03_connect_to_duet_mac_02.png =50%x){.align-right}](/guides/getting_connected/03_connect_to_duet_mac_02.png){target=_blank}We recommend using CoolTerm from [https://freeware.the-meiers.org/](https://freeware.the-meiers.org/). 

Go to Options > Serial Port and select the Duet from the 'Port:' drop down menu; it will be named something like "usbmodem1411". Baud rate can be set to 115200. 

Go to Options > Terminal > 'Line mode', which sends your command line when you press return rather than character by character ('raw mode'), and turn on 'Local Echo' so it shows the command you have sent.

Click 'OK' to save the options, then click 'Connect'. For newer versions of RepRapFirmware (3.6 and later), on Duet 3 boards, the terminal emulator must raise the DTR line, otherwise RRF will not transmit data. CoolTerm should enable this automatically; check that the radio button next to 'DTR' is green, and click it if it is not. For older versions of RRF, it doesn't matter if this is enabled or disabled. 

## SerialTools

[![SerialTools](/guides/getting_connected/03_connect_to_duet_mac_01.png =50%x){.align-right}](/guides/getting_connected/03_connect_to_duet_mac_01.png){target=_blank}If you prefer to use an app from the Appstore, try [SerialTools](https://apps.apple.com/gb/app/serialtools/id611021963?mt=12){target=_blank}.

Install SerialTools and run it. Select the Duet from the 'Serial Port' drop down; it will be named something like "usbmodem1411". Baud rate should be 115200, and Local Echo should be ticked. Click 'Connect' to connect.

For newer versions of RepRapFirmware (3.6 and later), on Duet 3 boards, the terminal emulator must raise the DTR line, otherwise RRF will not transmit data. Click the checkbox next to "DTR". For older versions of RRF, it doesn't matter if this is enabled or disabled. 

SerialTools is a bit finicky about text input; you have to get the Gcode command correct, without pressing delete or cursor keys. It sends each character to the Duet as you type them (raw mode), rather than when you press return. If you navigate away from SerialTools to another application, and then back, in the middle of writing the command, the command won't work. You should get an 'ok' when a command goes through correctly.

## Screen

[![Screen Mac](/guides/getting_connected/03_connect_to_duet_mac_02.jpg =50%x){.align-right}](/guides/getting_connected/03_connect_to_duet_mac_02.jpg){target=_blank}[![Screen Mac](/guides/getting_connected/03_connect_to_duet_mac_03.jpg =50%x){.align-right}](/guides/getting_connected/03_connect_to_duet_mac_03.jpg){target=_blank}It is also possible to use the built-in 'screen' terminal application. However, you cannot see the commands you type in, and the responses from the Duet are not formatted nicely on the screen. 

If you want to use 'screen', open Applications > Utilities > Terminal. Run `screen /dev/tty.usbmodem1411 115200`, replacing "usbmodem1411" with the Serial Port name for your Mac, if different (see Step 2).

You should see a blank screen, but see note on Duets with WiFi below. Hit enter and you should see OK returned. You are connected successfully. To disconnect and return to the Terminal, type 'Ctrl-a k'.

If you get an error, *"Sorry, could not find a PTY."* unplug the USB, plug it back in and try again.

# Linux

There are many options to connect using Linux. Our favourite is to use CuteCom, which is a GUI serial terminal. Alternatively, you can use a text-based serial terminal application such as Minicom, or the built-in 'screen' terminal.

### Add user to dialout

Most likely, the first thing you will need to do on a Linux installation is to add the user to the 'dialout' group, to allow permission to connect to the Duet. Do this by opening a Terminal window and sending `sudo adduser [your_username] dialout`. Following this, you will need to log out and back in, or possibly restart, to enable this.

### CuteCom

[![Cutecom](/guides/getting_connected/03_connect_to_duet_lin_01.png =50%x){.align-right}](/guides/getting_connected/03_connect_to_duet_lin_01.png){target=_blank}There are many options to connect using Linux. Our favourite is to use CuteCom, which is a GUI serial terminal.

It's available in most package repositories, or install with `sudo apt install cutecom`. It can also be downloaded from [here](https://gitlab.com/cutecom/cutecom/-/blob/master/README.md). It should automatically detect the correct port (usually /dev/ttyACM0), and is configured correctly out of the box. Click 'Open' to connect.

For newer versions of RepRapFirmware (3.6 and later), on Duet 3 boards, the terminal emulator must raise the DTR line, otherwise RRF will not transmit data. Click the checkbox next to "DTR", or in Settings set 'Flow Control' to 'Hardware'. For older versions of RRF, it doesn't matter if this is enabled or disabled. 

### Minicom

[![Minicom](/guides/getting_connected/03_connect_to_duet_lin_02.png =50%x){.align-right}](/guides/getting_connected/03_connect_to_duet_lin_02.png){target=_blank}Alternatively, you can use a text-based serial terminal application such as Minicom. Install with `sudo apt install minicom`, and run from a Terminal window with `minicom -s`, which starts it in setup mode. Select 'Serial port setup', then 'A', and change the port to '/dev/ttyACM0' (or the port name from step 2). Press return twice, then 'Exit'. The terminal will start. 

Press CTRL-A then W for linewrap, CTRL-A then U for CR at end of line, and CTRL-A then E for local echo. CTRL-A then X quits Minicom, releasing the port.

### Screen

[![Screen Linux](/guides/getting_connected/03_connect_to_duet_lin_03.png =50%x){.align-right}](/guides/getting_connected/03_connect_to_duet_lin_03.png){target=_blank}Finally, you can use the built-in 'screen' terminal. However, you cannot see the commands you type in, and the responses from the Duet are not formatted nicely on the screen. If you want to use 'screen', do this:

Run `screen /dev/ttyACM0 115200`. (Replace "ttyACM0" with the port name if it's different.) Hit enter and you should see OK returned. You are connected successfully.

To exit 'screen': CTRL-A then \ to exit screen and return to terminal. 