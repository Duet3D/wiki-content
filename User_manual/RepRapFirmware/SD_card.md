---
title: SD card
description: SD card usage notes, specification, rebuilding contents and troubleshooting. 
published: true
date: 2024-11-07T23:56:37.669Z
tags: 
editor: markdown
dateCreated: 2021-12-03T10:11:18.461Z
---

<!--Update card contents list for DWC 3-->

# Introduction

This guide deals with SD cards for use in the Duet SD card socket, for all Duets including Duet 2 WiFi/Ethernet/Maestro, and Duet 3 running in standalone mode. For Duet 3 boards connected to a Raspberry Pi or other Single Board Computer (SBC), the SD card is put in the SBC, **not** in the Duet 3; see the Duet 3 documentation for using the Duet 3 in this configuration: [Getting Started With Duet 3](/User_manual/Overview/Getting_started_Duet_3_MB6HC)

# General Notes

All Duets have a built-in micro SDHC card socket. **Duet 2 WiFi/Ethernet** are supplied with a compatible SD card. **Duet 3** is also supplied with an SD card, formatted with two partitions; one for standalone mode, and one for use in a Raspberry Pi in SBC mode.

In normal use, you should never need to remove the SD card from its socket. You can transfer files to/from the SD card over the web interface.

**Caution!** Do not use an SD extender cable. Such cables do not generally work well at the high SD card transfer speeds used by the Duet. Additionally, some types of SD card extender cable have been found to damage the SD card socket. **Damage to the SD card socket from using an extender cable is not covered by the warranty.**

It is possible to connect a second, low-speed SD card socket to most Duets (Duet 3 MB6HC from RRF 3.4, [see here](/User_manual/Connecting_hardware/Display_PanelDue#duet-3-mb6hc-using-ribbon-cable)), either by connecting a [PanelDue](/User_manual/Connecting_hardware/Display_PanelDue), a [12864 LCD with SD card socket](/User_manual/Connecting_hardware/Display_12864), or an [external SD card reader](https://forum.duet3d.com/topic/7406/).

# Specification

If you need to replace the micro SDHC card, we recommend you choose:

* A branded card with a speed rating of Class 4 at a minimum, Class 10 preferred. 
* Capacity is not important (128GB cards have been tested, and work, theoretically up to 2TB cards will work), it just needs to be formatted as FAT32. RepRapFirmware does not support SD cards formatted in exFAT format. 
* Larger capacity cards can be used, but RepRapFirmware can only read the first volume on the card.
* Consider a microSD card with an A1 or A2 rating, particularly for SBC. They're better suited for SBC operation and improve overall responsiveness of the SBC.
* Consider an 'industrial' microSD card, which generally have higher endurance, reliability, extended temperature range, and longevity.

# Formatting

If you need to reformat the micro SDHC card:

* If the capacity of the card is 4GB or lower, use FAT16 format
* If the capacity is more than 4GB, use FAT32 format
* All cards should be formatted with 512 byte sectors
* For best upload speed choose the largest cluster size available, which is normally 64kB for FAT16 and 32kB for FAT32.
* You can use the [official SD Card formatting tool](https://www.sdcard.org/downloads/formatter/index.html){target=_blank} for best results with cards up to 32GB.
* **DO NOT** use Windows 'Quick Erase'. This generally does not create a clean FAT32 partition.
* You can format SD cards larger than 32GB, up to 2TB, as FAT32.
  * **Windows 11** apparently now allows you to format FAT32 to any size SD card (not tested). In the past, it was limited to 32GB, and any larger cards were formatted as ExFAT. So users of older Windows versions may need a utility such as: [http://ridgecrop.co.uk/index.htm?guiformat.htm](http://ridgecrop.co.uk/index.htm?guiformat.htm). 
  * **MacOS** and **Linux** can format any size card as FAT32 natively. 
  * For more information on formatting large SD cards, see [this forum thread](https://forum.duet3d.com/topic/36414/using-sd-cards-with-capacity-32gb){target=_blank}.

# SD card structure

## Duet 3, Duet 2 WiFi / Ethernet / Maestro

These Duets use the following folder structure:

`/filaments` holds the profiles for any user-defined filaments. See [User manual: Filaments](/User_manual/Reference/DWC_filaments)

`/firmware` RepRapFirmware v3.3 and later. This holds firmware files ready for installation, and the in-app programmer (IAP) binary. In RRF v3.2 and earlier, firmware and IAP files are stored in /sys.

`/gcodes` is used to hold g-code files for printing. You can use subfolders of /gcodes to organize these files. G-code files can also be located on an external SD card if one is connected.

`/macros` is used to hold used-defined macro files. The names of these files appear as menu entries in DuetWebControl and on PanelDue. You can use subfolders of /macros to organize these files. See [Macros](/User_manual/Tuning/Macros) for examples of useful user macros.

`/menu` This holds the files that define the menu layouts for 128x64 pixel monochrome displays. Compatible with Duet 3 mini 5+ and Duet 2 Maestro. For details, see [User manual: 12864 display menu system](/User_manual/Connecting_hardware/Display_12864_menu)

`/sys` is used to hold system configuration files and, in all firmware versions before RRF 3.3, firmware update files ready for installation. It should contain at least the following files:

* **config.g** holds the firmware configuration script, which is executed at startup.
* **config-override.g** holds the configuration parameters that were saved when you last ran M500. Your config.g file should normally include command M501 near the end, to load these saved values at startup and override any similar commands earlier in config.g.
* **homex.g**, **homey.g**, **homez.g** and **homeall.g** are the homing scripts for a Cartesian or CoreXY printer. For a delta printer there is **homedelta.g** instead.
* **bed.g** holds the script for probing the bed and calculating bed compensation on a Cartesian or CoreXY printer, or doing delta calibration on a delta printer.
* **pause.g** is run when you pause a print.
* **resume.g** is run when you resume a print.
* **cancel.g** is optional, but if present is run when you cancel a paused print.
* **start.g** is optional, but if present is run whenever you start a job from the SD card
* **stop.g** is optional, but if present is run when a SD card print finishes normally with a M0 command at the end of the print job.
* **trigger2.g**, **trigger3.g**, ... are optional files that can be configured to run when particular endstop pins are triggered, for example by an emergency-stop button.
* If your Z probe needs to be deployed and retracted, the script files **deployprobe.g** and **retractprobe.g** are used. They are invoked automatically and by the [M401](/User_manual/Reference/Gcodes/M401) and [M402](/User_manual/Reference/Gcodes/M402) commands.

`/www` folder and its subfolders hold the files served by the web server. If you are setting up a new SD card, populate the **/www** folder by extracting the contents of the DuetWebControl.zip file to it.

NOTE: the folders **/filaments**, **/firmware**, **/gcodes**, **/macros**, **/menu**, **/sys**, and **/www** must be named exactly as shown. Sometimes the distribution of configuration files for a specific printer platform will come with the folder named **/sys-<printer_type>** , so the **-<printer_type>** must be removed.

### Duet 2 with firmware 1.18.2 or earlier

The Duet 2 WiFi **with pre-1.19 firmware** requires only /gcodes, /macros and /sys folders on the internal SD card. These folders have the same function as described earlier on this page. The web server files are stored in file DuetWebControl.bin which is uploaded to the WiFi module.

## Creating the file structure

* To create a new SD card that will act as the Duet's primary card, first format a suitable SD card as above.
* Use the [RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/Start){target=_blank} to create a configuration for your printer.
* On the 'Finish' page, if you select "Get the latest stable Duet Web Control version" the config.zip file you download will have a /www folder with the latest version of DWC in it.
* On the 'Finish' page, if you select "Get the latest stable RepRapFirmware version", the config.zip file you download will have the latest versions of the firmware and IAP file (In-App Programmer, needed to update the firmware) for your board in it. Note that you do not need these files on the SD card to run the Duet, only to update it. The Firmware Version you selected in the Config Tool > General page will determine where these are stored:
  * 3.3 or later - a /firmware folder is created and firmware and IAP files placed in it.
  * 3.2 and earlier - firmware and IAP files are placed in the /sys folder.
* Download the configuration, unzip the file and copy the folder(s) to the SD card. 
* If you didn't get the latest DWC version, or are creating a new SD card for a Duet running older firmware, get a compatible version of DWC for the firmware you are using and place that in /www/, see [Firmware Overview](/User_manual/RepRapFirmware/RepRapFirmware_overview) for more details on the firmware versions.
* Add a /gcodes folder for your print files. These can be uploaded over the web interface.
* Add a /macros folder for scripts to automate tasks. See [Macros](/User_manual/Tuning/Macros) for some inspiration.
* Add /filaments and /menu folders, if needed.
* This should give you a basic config.g and web interface files. The SD card structure should be similar to this, though the exact file names may vary:

```
.
├── filaments
├── firmware
│   ├── Duet2CombinedFirmware.bin
│   └── Duet2_SDiap32_WiFiEth.bin
├── gcodes
├── macros
├── sys
│   ├── bed.g
│   ├── config.g
│   ├── config.json
│   ├── homeall.g
│   ├── homex.g
│   ├── homey.g
│   ├── homez.g
│   ├── pause.g
│   ├── resume.g
│   ├── sleep.g
│   ├── stop.g
│   ├── tfree0.g
│   ├── tpost0.g
│   └── tpre0.g
└── www
    ├── DuetAPI.xml
    ├── css
    │   ├── Accelerometer.5c60999b.css.gz
    │   ├── GCodeViewer.9597b317.css.gz
    │   ├── HeightMap.4d390d72.css.gz
    │   ├── ObjectModelBrowser.c5e13b42.css.gz
    │   ├── OnScreenKeyboard.7f43fe4b.css.gz
    │   └── app.ce075a4e.css.gz
    ├── favicon.ico.gz
    ├── fonts
    │   ├── materialdesignicons-webfont.147e3378.woff
    │   ├── materialdesignicons-webfont.147e3378.woff.gz
    │   ├── materialdesignicons-webfont.174c02fc.ttf.gz
    │   ├── materialdesignicons-webfont.64d4cf64.eot.gz
    │   ├── materialdesignicons-webfont.7a44ea19.woff2
    │   └── materialdesignicons-webfont.7a44ea19.woff2.gz
    ├── img
    │   └── icons
    │       ├── android-chrome-192x192.png.gz
    │       ├── android-chrome-512x512.png.gz
    │       ├── android-chrome-maskable-192x192.png.gz
    │       ├── android-chrome-maskable-512x512.png.gz
    │       ├── apple-touch-icon-120x120.png.gz
    │       ├── apple-touch-icon-152x152.png.gz
    │       ├── apple-touch-icon-180x180.png.gz
    │       ├── apple-touch-icon-60x60.png.gz
    │       ├── apple-touch-icon-76x76.png.gz
    │       ├── apple-touch-icon.png.gz
    │       ├── favicon-16x16.png.gz
    │       ├── favicon-32x32.png.gz
    │       ├── msapplication-icon-144x144.png.gz
    │       ├── mstile-150x150.png.gz
    │       └── safari-pinned-tab.svg.gz
    ├── index.html.gz
    ├── js
    │   ├── Accelerometer.4a8a402b.js.gz
    │   ├── Accelerometer.4a8a402b.js.map.gz
    │   ├── GCodeViewer.95d3cd80.js.gz
    │   ├── GCodeViewer.95d3cd80.js.map.gz
    │   ├── HeightMap.a082b8e6.js.gz
    │   ├── HeightMap.a082b8e6.js.map.gz
    │   ├── ObjectModelBrowser.0b9b2798.js.gz
    │   ├── ObjectModelBrowser.0b9b2798.js.map.gz
    │   ├── OnScreenKeyboard.a880bca8.js.gz
    │   ├── OnScreenKeyboard.a880bca8.js.map.gz
    │   ├── app.24acd1b2.js.gz
    │   └── app.24acd1b2.js.map.gz
    ├── manifest.json.gz
    ├── precache-manifest.a4ab8f573fcc203b798fa7d11eee39d5.js.gz
    └── service-worker.js.gz
```

# Troubleshooting SD Card issues

## SD Card

A faulty SD card can cause a wide range of strange behaviour: inaccessible DWC, config.g not run at startup, slow downs printing, network disconnections, slow file transfer, corrupted files and almost any other problem that relies on accessing the SD card.

### Check the SD card interface

Send `M122` to the Duet (via the web console, or connect via USB using YAT or similar serial terminal software) and look for the 'SD Card' entries. A normal response should look something like:

```
SD card 0 detected, interface speed: 20.0MBytes/sec
SD card longest block write time: 5.1ms, max retries 0
```

If it responds with "SD card detected" in M122, it indicates that the Card Detect pin of the SD card is working (this is also the pin whose soldering causes the most trouble; see below).

The interface speed in the same line gives an indication of whether the processor is able to communicate with the SD card at all. It should be 20MB/sec on a Duet WiFi/Ethernet, 15MB/sec on a Duet Maestro, 25MB/sec on Duet 3 MB6HX or MB6XD in standalone mode, and 22.5MB/sec on a Duet 3 Mini in standalone mode. Other numbers, eg 12MBs/sec, are odd and suggest an issue with the SD card. If the socket or processor is faulty but the card is detected, it usually drops to 0.2MB/sec.

### Check you have space on the SD card

A card with very little space will cause file fragmentation, and make reading the card slow. Send [`M39`](/User_manual/Reference/Gcodes/M39) to report the SD card information:

```
M39
SD card in slot 0: capacity 3.97GB, free space 3.81GB, speed 20.00MB/sec, cluster size 32kB
```

### Run a speed test

You can run a speed test on the SD card by sending:

`M122 P104 S[file size in MB]`

For example (Duet 3 Mini 5+ using RRF 3.4.2):

```
M122 P104 S10
Testing SD card write speed...
SD write speed for 10.0MByte file was 2.91MBytes/sec
Testing SD card read speed...
SD read speed for 10.0MByte file was 1.58MBytes/sec
```

Speeds reported should usually be between 2 and 2.5MBytes/sec for the write speed, and lower for the read speed. For example, Duet 2 WiFi - 2.23MBytes/sec, Duet Maestro 2.42MBytes/sec for a 10MB file. The read speed is expected to be lower than the write speed. This is because RRF uses a large write buffer (usually 8kB) to speed up file uploading. Reading doesn't normally need to be as fast, so the buffer is only 512b.

### Stuttering during printing

Stuttering during printing can be caused by the SD card not supplying the motion planner with data quickly enough, another sign that the SD card may be under-performing. Run `M122` from the console during a print, and look for "Underruns" in this line:

```
## DDARing
Scheduled moves: 1295584, completed moves: 1295544, StepErrors: 0, LaErrors: 0, Underruns: 80, 300
```

The first value isn't a warning, just an indication that the lookahead function couldn't do something with the time given. It doesn't slow down the print, but is likely not ideal. The second number is a 'prepare move' underrun, which means that the move could not be prepared in time and so the movement must wait. This is much worse than the first one. Try replacing the SD card if you get these.

### Force unmount and mount

You can force the SD card to unmount and mount, using [M22](/User_manual/Reference/Gcodes/M22) to unmount, [M21](/User_manual/Reference/Gcodes/M21) to mount. Any error message generated may be helpful in determining if the card is being read at all or not.

## SD Card Diagnostic Test

You can run a diagnostic test on your SD card using [H2Testw (Windows only)](https://www.heise.de/download/product/h2testw-50539) ([link to download page](https://www.heise.de/download/product/h2testw-50539/download))

```
Warning: Only 4461 of 7428 MByte tested.
The media is likely to be defective.
4.3 GByte OK (9136127 sectors)
0.5 KByte DATA LOST (1 sector)
Details:0 KByte overwritten (0 sectors)
0 KByte slightly changed (< 8 bit/sector, 0 sectors)
0.5 KByte corrupted (1 sector)
0 KByte aliased memory (0 sectors)
First error at offset: 0x000000008123ff80
Expected: 0x04c122acee44af80
Found: 0x27a7a1cd8943af80
Reading speed: 66.8 MByte/s
```

## SD Card Socket

Some boards have poorly soldered SD cart sockets, with the pins on the back of the socket making poor or no contact, causing erratic behaviour or unable to read the SD card at all. Boards with this issue will be replaced under warranty. The picture below shows the pins on the right are not making contact.

![sd_card_faulty_soldering.png](/manual/configuration/sd_card_faulty_soldering.png =600x)

Check if the SD card socket is getting hot. This can be caused by a short from VIN (12 or 24V) to the 5V or 3.3V rail or a failure of the 5V or 3.3V regulator. This is usually terminal for the Duet, as the WiFi board (if fitted) and main processor may also be getting hot, due to over-voltage short circuit damage. The SD card may also be damaged, and the Duet will likely be unresponsive. See [What to do if your Duet won't respond](/User_manual/Troubleshooting/Duet_wont_respond) and [How to destroy your Duet](/User_manual/Troubleshooting/How_to_destroy_your_Duet)

## Network disconnections after uploading large files

After you have uploaded a large file to the SD card, you may find that Duet Web Control disconnects with a timeout error whenever you try to switch to the GCode Files page.

This issue is triggered by the combination of a large GCode file with a small cluster size. Duet Web Control attempts to extract details about the print from the end of the file, and the nature of the FAT filesystem used on SD cards can make this a very slow process.

A workaround is to reformat the SD card to use a larger cluster size. Here's how:

1. Check the existing cluster size. If you are running firmware 1.21 or later, then you can run [M39](/User_manual/Reference/Gcodes/M39) from the command line to find this. Otherwise, move the card to a PC and examine the disk properties.
1. If the cluster size is already 64kB (for cards smaller than 4GB) or 32kB (for cards larger than 4GB) then the following won't help; although defragmenting the large file that is causing the problem may help.
1. Move the SD card to a PC
1. Create a temporary folder on your PC to hold the contents of the card
1. Copy all files and subfolders on the SD card into your temporary folder
1. Reformat the SD card, see [SD card page here](/User_manual/RepRapFirmware/SD_card#formatting). If it is 4GB or smaller, specify FAT16 format, with cluster size 64kB if you are given a choice. If it is larger than 4GB then FAT16 will not be available, so use FAT32 and specify 32kB (or 64kB if available) cluster size.
1. Copy all the files and folders form your temporary folder back on to the SD card
1. Eject or safely remove the SD card from the PC and put it back in the Duet
1. Restart the Duet

## Slow network upload of GCode job files to the SD card

The speed of uploading to the SD card over the network depends on both the network speed and the SD card write speed. If upload speeds are slower than expected:

1. Run a speed test on the SD card using M122 P104 (see "Run a speed test" above).
1. To test the network upload speed, take a large (at least 10Mb) GCode file on your PC, rename the extension to ".dummy", and upload it on the Jobs page of Duet Web Control. RepRapFirmware will accept the upload but omit writing it to the SD card, so that the speed reported by Duet Web Control reflects just the time taken to send the file to the Duet.

The results will tell you whether the SD card or the network is the bottleneck. With a strong WiFi signal, the WiFi upload speed should be around 0.7MBytes/sec or a little better. Using Ethernet, the speed may be in excess of 4MBytes/sec on Duet 3 boards.