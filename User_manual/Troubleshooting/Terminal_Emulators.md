---
title: Terminal Emulators
description: There is no requirement to connect to your Duet hardware over USB for normal operation however for certain trouble shooting steps the use of a terminal emulator is required. This page provides some information on getting connected with one.
published: true
date: 2021-12-16T23:32:10.574Z
tags: yat, terminal emulator, troubleshooting
editor: markdown
dateCreated: 2021-10-31T18:10:59.107Z
---

# Introduction

It is not necessary to use USB with a Duet board for normal operation however there are a number of troubleshooting instructions that require connecting to the Duet over USB (normally when connecting over the network is not working for some reason).

Different operating systems have different terminal emulator support built in. In windows it is necessary to instal a terminal emulator. Most Linux distributions have one built in, however a program with a GUI like CuteCom may make using it easier for people who prefer GUIs. CuteCom should also work on MacOS, other people may prefer [SerialTools](https://apps.apple.com/gb/app/serialtools/id611021963).

## YAT - terminal emulator for Windows ##

There are many terminal emulators available for windows, the simplest and most reliable we have found is Yet Another Terminal (YAT). You can install it from [Sourceforge here](https://sourceforge.net/projects/y-a-terminal/)

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
  
## CuteCom - GUI terminal editor for Linux \& Mac OS
  
Details to follow, can be downloaded [here](https://gitlab.com/cutecom/cutecom/-/blob/master/README.md)

  