---
title: Terminal Emulators
description: There is no requirement to connect to your Duet hardware over USB for normal operation however for certain trouble shooting steps the use of a terminal emulator is required. This page provides some information on getting connected with one.
published: true
date: 2025-07-04T13:38:06.993Z
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

There are different standards for how to end a line - see [Wikipedia](https://en.wikipedia.org/wiki/Newline) for more information. Setting them wrong in a terminal emulation program leads to a long single line (which may be too long for the terminal):

![Image showing the YAT terminal emulation program with incorrect line endings set and a single long line of text with \<LF> inserted within the single line](/manual/troubleshooting/yat1.png =800x)

Setting them right gets the response you would expect:

![Image showing the YAT terminal with correct line endings so the line end when they should](/manual/troubleshooting/yat2.png =800x)

#### Setting line endings in YAT
  
The Duet uses \<LF> for line endings  (Unix like). To set them in YAT go to ***Terminal*** in the top menu bar then ***Settings***:
Any content here will go into the first tab...

![Image showing the Terminal Menu in YAT](/manual/troubleshooting/yat3.png =300x)

Then select **Text Settings** and change the EOL sequence selection to **\<LF>**

![Image showing the Text Terminal Settings Dialog box in YAT](/manual/troubleshooting/yat4.png =500x)

### COM Port

Unless you have other serial devices connected to your machine, other than the Duet, there will probably only be one COM port available for selection in YAT:

![Image showing the Terminal Settings Dialog box in YAT](/manual/troubleshooting/yat5.png =500x)

Select the available port under Serial Port, the defaults for Bits per second etc should work.

Then click the green tick to connect (or ***Terminal*** menu then ***Open/Start***).

If the COM post is not available that is outside the scope of these instructions, check your USB connections, try a different cable and port, see [Installing and Updating Firmware - Fallback procedure Num 2](https://docs.duet3d.com/User_manual/RepRapFirmware/Updating_firmware#fallback-procedure-2)
  
# macOS

Cutecom is also available for macOS.

## SerialTools

For macOS, we recommend using [SerialTools](https://apps.apple.com/gb/app/serialtools/id611021963) from the AppStore. 

![SerialTools](/guides/getting_connected/03_connect_to_duet_mac_01.jpg =600x)

Install SerialTools and run it. Select the Duet from the 'Serial Port' drop down; it will be named something like "usbmodem1411". Baud rate should be 115200, and Local Echo should be ticked. Click 'Connect' to connect.

SerialTools works, but is a bit finicky about input; you have to get the command right, without pressing delete or cursor keys. I think it sends each character to the Duet as you type them (raw mode), rather than when you press return. If you navigate away from SerialTools to another application, and then back, in the middle of writing the command, the command won't work. You should get an 'ok' when a command goes through correctly.

## CoolTerm

CoolTerm is more reliable than SerialTools, but is not on the AppStore. It's available from [freeware.the-meiers.org](https://freeware.the-meiers.org/). 

Turn on the options for ‘line mode’ and ‘local echo’, for more reliable input and response.

## Screen

It is also possible to use the built-in 'screen' terminal application. However, you cannot see the commands you type in, and the responses from the Duet are not formatted nicely on the screen. 

![Screen Mac](/guides/getting_connected/03_connect_to_duet_mac_02.jpg =400x) ![Screen Mac](/guides/getting_connected/03_connect_to_duet_mac_03.jpg =400x)

If you want to use 'screen', open Applications > Utilities > Terminal. Run `screen /dev/tty.usbmodem1411 115200`, replacing "usbmodem1411" with the Serial Port name for your Mac, if different.

Hit enter and you should see OK returned. You are connected successfully.

If you get an error, *"Sorry, could not find a PTY."* unplug the USB, plug it back in and try again.

To exit 'screen', press CTRL-A then CTRL-\ to exit screen and return to terminal.

# Linux

There are many options to connect using Linux. Our favourite is to use CuteCom, which is a GUI serial terminal. Alternatively, you can use a text-based serial terminal application such as Minicom, or the built-in 'screen' terminal.

## CuteCom

![Cutecom](/guides/getting_connected/03_connect_to_duet_lin_01.jpg =600x)

Cutecom is available in most package repositories, or install with `sudo apt install cutecom`. It can also be downloaded from [here](https://gitlab.com/cutecom/cutecom/-/blob/master/README.md). It should automatically detect the correct port (usually /dev/ttyACM0), and is configured correctly out of the box. Click 'Open' to connect.

## Minicom

Install with `sudo apt install minicom`, and run from a Terminal window with `minicom -s`, which starts it in setup mode. Select 'Serial port setup', then 'A', and change the port to '/dev/ttyACM0' (or the port name from step 2). Press return twice, then 'Exit'. The terminal will start. 

![Minicom](/guides/getting_connected/03_connect_to_duet_lin_02.png =600x)

Press CTRL-A then W for linewrap, CTRL-A then U for CR at end of line, and CTRL-A then E for local echo. CTRL-A then X quits Minicom, releasing the port.

## Screen

When running 'screen' you cannot see the commands you type in, and the responses from the Duet are not formatted nicely on the screen. If you want to use 'screen', do this:

Run `screen /dev/ttyACM0 115200`. (Replace "ttyACM0" with the port name if it's different.) Hit enter and you should see OK returned. You are connected successfully.

![Screen Linux](/guides/getting_connected/03_connect_to_duet_lin_03.png =600x)

To exit 'screen': CTRL-A then \ to exit screen and return to terminal. 

  