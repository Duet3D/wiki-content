---
title: Installing and Updating Firmware
description: Instructions to update the main firmware on Duet 3 MB6HC and Duet 3 Mini 5+ in standalone mode, Duet 2 WiFi, Ethernet and Maestro, Duet Web Control (DWC) and the WiFi firmware on Duet 3 Mini 5+ WiFi and Duet 2 WiFi boards.
published: true
date: 2024-06-13T15:08:09.330Z
tags: 
editor: markdown
dateCreated: 2021-11-30T12:57:13.348Z
---

# Notes before you start

<!-- * These instructions are focussed on updating firmware on current boards, and to RepRapFirmware 3.x. If you need instructions for older boards or older versions of the firmware, see [Installing and Updating Firmware (Legacy) **UPDATE LINK**]() -->
> **For updating the firmware on a Duet board in SBC mode, see [SBC setup for Duet](/User_manual/Machine_configuration/SBC_setup).**
The instructions below are for updating the firmware on Duet 3 (all boards, in standalone mode), Duet 2 WiFi, Ethernet and Maestro boards only. Using them to update a Duet board using a Single Board Computer (eg Raspberry Pi) may leave your board in an unresponsive state. 
{.is-warning}
* Check that you have [downloaded the correct firmware](https://github.com/Duet3D/RepRapFirmware/releases) before installing it. See section below regarding which files are needed for each board.
* Make sure you download and save the files as bin or zip and check the files sizes match.
* Backup your SD card and download a copy of your current firmware version in case you need to revert the firmware.
* Some updates to the main firmware on a Duet 2 WiFi or Duet 3 Mini 5+ WiFi may require an update to a compatible WiFi server firmware version as well, and vice versa. See the 'updating WiFi firmware' below which explains how to do this.
* **If you download more than just the main firmware (DuetWiFiFirmware.bin/Duet2CombinedFirmware.bin) then please read the 'updating more than one firmware at a time' section below before proceeding as the firmware may need to be updated in a specific order.**
* For RepRapFirmware 3, please ensure you read the [RepRapFirmware 3.x Changelog](https://github.com/Duet3D/RepRapFirmware/wiki/Changelog-RRF-3.x) notices for the new version of the firmware as it will contain information on changes that affect your configuration.
* For RepRapFirmware 2, please ensure you read the [RepRapFirmware 1.x and 2.x Changelog](https://github.com/Duet3D/RepRapFirmware/wiki/Changelog-RRF-1.x-&-2.x) notices for the new version of the firmware as it will contain information on changes that affect your configuration.

## Duet boards and supported firmware versions

Duet 3 MB6HC - RepRapFirmware 3.x
Duet 3 MB6XD - RepRapFirmware 3.4 and later
Duet 3 Mini 5+) - RepRapFirmware 3.2 and later
Duet 2 (WiFi, Ethernet, Maestro) - RepRapFirmware 2.x, 3.x
Legacy Duet 0.6 and 0.85 - RepRapFirmware 1.x

Currently, the latest version of RepRapFirmware 2.x is 2.05.1. These files and the RepRapFirmware 1.26.1 files for legacy Duets are available [here](https://github.com/Duet3D/RepRapFirmware/releases/tag/2.05.1).

## Downloading RepRapFirmware files

The latest release, along with beta, release candidates (RC) and previous versions of RepRapFirmware are available at [https://github.com/Duet3D/RepRapFirmware/releases](https://github.com/Duet3D/RepRapFirmware/releases)

Our general advice is to use the [‘Latest release‘ version from GitHub](https://github.com/Duet3D/RepRapFirmware/releases/latest), unless you have a specific reason to use a 'beta' or 'RC' release. Beta and RC versions are marked ‘Pre-release’ on GitHub.

Each 'release' version has a large zip file that contains all the files needed to update all Duet boards, e.g. in RRF 3.3 it is 'Duet2and3Firmware-3.3.zip'. Download this, without unzipping it. Individual binary files can also be downloaded, as needed. 

'Beta' and 'RC' releases are supplied as individual files, not as a convenient zip file, and need to be downloaded and installed individually.

## Required files

Each Duet board needs up to four files to fully update the firmware and software. These are:

**Firmware binary** - This is the main firmware for the Duet boards, and there is a specific version for each Duet board. The main firmware for all boards except Duet 3 Mini 5+ is supplied as a .bin file; for the Duet 3 Mini 5+ it is a .uf2 file.

**In-App programmer (IAP) binary** - This file is needed on the SD card and does the job of programming the firmware. There is a specific version for each Duet board. They don’t change with every firmware release, but new versions often accompany major firmware releases. You will get a message if it is missing, with the filename it expects, and (in DWC 3.3 and later) the location it expects to find it. If it is missing, check the firmware version your Duet is currently using, and download the iap file from the matching firmware version on the [Duet3D Github repository](https://github.com/Duet3D/RepRapFirmware/releases/). If the IAP file isn't included in the 'Assets' list of that firmware release, it should be in the .zip file for that release.

**WiFi firmware binary** - Required for the ESP8266-based WiFi board on the Duet 2 WiFi and Duet 3 Mini 5+ WiFi. Use the same version that is supplied with the firmware you are flashing.

**Duet Web Control (DWC) zip** - Web-browser-based user interface for communicating and controlling all Duet boards. Use the same version that is supplied with the firmware you are flashing.

## Firmware file naming

Below is the firmware files, and file names, required for each Duet board. Filenames are correct for RRF 3.3 and 3.4. Older versions of the firmware will have similar file names, but they have changed over time. Note that these files are for Standalone mode, NOT SBC mode (except for Duet 2 WiFi/Ethernet in SBC mode). See [SBC setup for Duet 3](/User_manual/Machine_configuration/SBC_setup) for updating a Duet with connected SBC.

| | **Firmware binary** | **IAP binary** | **WiFi firmware binary** | **Duet Web Control zip** |
|---|---|
| **Firmware .zip** | Duet2and3Firmware-[X.X.X].zip | Large .zip file that contains all the files needed to update all Duet boards.<br>[X.X.X] is the firmware version, e.g. '3.4.0'. Upload direct to Duet in DWC,<br>which will use and keep only the files it needs. ||||
| **Duet 3 Mainboard 6HC** | Duet3Firmware_MB6HC.bin | Duet3_SDiap32_MB6HC.bin |  | DuetWebControl-SD.zip |
| **Duet 3 Mainboard 6XD** | Duet3Firmware_MB6XD.bin | Duet3_SDiap32_MB6XD.bin |  | DuetWebControl-SD.zip |
| **Duet 3 Mini 5+** | Duet3Firmware_Mini5plus.uf2 | Duet3_SDiap32_Mini5plus.bin | DuetWiFiServer.bin | DuetWebControl-SD.zip |
| **Duet 3 Expansion 1HCL** | Duet3Firmware_EXP1HCL.bin |  |  |  |
| **Duet 3 Expansion 1XD** | Duet3Firmware_EXP1XD.bin |  |  |  |
| **Duet 3 Expansion 3HC** | Duet3Firmware_EXP3HC.bin |  |  |  |
| **Duet 3 Toolboard 1LC** | Duet3Firmware_TOOL1LC.bin |  |  |  |
| **SAMMY C21** | Duet3Firmware_SAMMYC21.bin |  |  |  |
| **Duet 2 WiFi/Ethernet** | Duet2CombinedFirmware.bin | Duet2_SDiap32_WiFiEth.bin | DuetWiFiServer.bin | DuetWebControl-SD.zip |
| **Duet 2 WiFi/Ethernet - SBC** | Duet2Firmware_SBC.bin | | | |
| **Duet 2 Maestro** | DuetMaestroFirmware.bin | Duet2_SDiap32_Maestro.bin |  | DuetWebControl-SD.zip |

When using a Duet 3 mainboard as an expansion board, the /firmware folder on the SD card in the mainboard-as-expansion-board needs to contain the CAN IAP file. Otherwise it won't be possible to update the expansion board over CAN.

| | **IAP binary** |
|---|---|
| **Duet 3 Mainboard 6HC** | Duet3_CANiap32_MB6HC.bin |
| **Duet 3 Mainboard 6XD** | Duet3_CANiap32_MB6XD.bin |
| **Duet 3 Mini 5+** | Duet3_CANiap32_Mini5plus.bin |

## Updating from older firmware versions

If you are updating from an old version of the firmware, there may be additional steps you need to take. Send [M115](/User_manual/Reference/Gcodes/M115) to your Duet via USB or in the DWC console to check the current firmware version.

### Updating without firmware erase

We generally recommend that firmware updates should be done in incremental steps, especially if you are updating from a very old version. We usually suggest updating in the following order:
**RRF v1.x** to **RRF v2.05.1** to **RRF v3.0** to **RRF v3.3** to **the latest stable release**.
You don't need to downgrade your firmware; just update to the next version on from your current version, eg if you are on RRF 3.1.1, update to v3.3, then the latest stable release.
See the tabs below for notes on updating to specific releases.

### Tabs {.tabset}

#### Updating from RRF 3.3

If you are updating from RRF 3.3 or later, you should be able to update by downloading the Duet2and3Firmware-3.x.zip file (where 3.x is the version number) from the latest stable release. This contains all the firmware and software files for all Duet boards. There’s no need to unzip it. You can download individual files as necessary, too. See the section later about updating individual files. Firmware files will be stored in the '0:/firmware/' directory.

Download file Duet2and3Firmware-X.X.zip file from the latest stable release, which is available from [the Duet3D Github repository here](https://github.com/Duet3D/RepRapFirmware/releases/latest). Follow the 'Usual procedure' instructions below to update the firmware.

#### Updating from RRF 3.0 - 3.2.2

If you are updating from an early version of RRF 3.x, before RRF 3.3, we recommend updating to RRF 3.3 before updating to later firmware versions. This is because:
* The location where the firmware files are stored was changed. Versions previous to RRF 3.3 stored them in the ‘0:/sys/’ directory, but from version 3.3 they are stored in the ‘0:/firmware/’ directory. When you upload the version 3.3 files in an older RRF version, they will be stored in the /sys directory. Once RRF 3.3 has been installed, subsequent firmware uploads will cause the /firmware directory to be created, and the firmware files will be stored in there.
* From RRF 3.3, the version number of DWC was aligned with the version number of RRF. It is now easier to check if the versions of DWC and RRF are compatible; they should match. Older versions of the firmware don't work well with newer versions of DWC, and vice versa.

Download file Duet2and3Firmware-3.3.zip from [the Github repository here](https://github.com/Duet3D/RepRapFirmware/releases/tag/3.3). Follow the 'Usual procedure' instructions below to update the firmware. We recommend updating to the latest stable version of RRF next. See the previous tab.

#### Updating from RRF 1.x or 2.x

**Important!** When upgrading from 1.x or 2.x firmware to 3.x you will need to make substantial changes to your config.g file. See [Migrating from RRF2 to RRF3](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3).

**Upgrading ANY Duet 2 from RRF 1.x or 2.x to 3.0 or later requires installing RRF 3.0 first.**

The version 3.01 and later binaries are too large to be installed by the IAP program supported by version 1.x and 2.x firmware. Therefore you cannot upgrade directly from version 1.x or 2.x firmware to 3.01 or later firmware. This is the procedure:

* Make sure you have an 'iap4e.bin' (for Duet 2 WiFi/Ethernet) or 'iap4s.bin' (for Duet 2 Maestro) file in your SD card /sys folder. This is the 'In-App Programmer' that flashes the firmware, and without it the firmware update will fail. See the 'Required files' section above for details where to download this if it is missing.
* Download file Duet2and3Firmware-3.0.zip from [the Github repository here](https://github.com/Duet3D/RepRapFirmware/releases/tag/3.0)
* Follow the 'Usual procedure' instructions below to update the firmware.
* Send M115 and check the response to confirm that you are running version 3.0 firmware
* You can then upgrade to version 3.01 or later firmware. We recommend updating to version 3.3 next. See the previous tab.
* If you are updating from v1.x, you may want to update to v2.05.1 first. Though we have tested back to RRF v1.21 that you can go straight to RRF 3.0.

If you are updating a Duet 2 WiFi from RRF 1.18.2 or earlier, you will also need to manually update the WiFi firmware via USB or PanelDue. See 'Updating WiFi firmware' instructions below. And you will need to unzip the Duet Web Control zip file into a folder called 'www' on the SD card. See [SD card](/User_manual/RepRapFirmware/SD_card) for the current SD card structure.

### Updating with firmware erase

While it is possible to update from older versions of RepRapFirmware by uploading firmware releases via the web interface, you may have to do this multiple times depending on the firmware version. If you're planning on jumping to the latest RRF 3.x version, it may be more convenient to simply erase the firmware and flash the latest version with BOSSA or SAM-BA; see 'Fallback procedure #2' instructions below.

## File locations on the Duet SD card

* With all firmware versions up to RRF v3.2.2, all firmware update files are stored in the ‘0:/sys/’ directory. 
* From RRF v3.3, to avoid too many files in this folder, all firmware update files are stored in ‘0:/firmware/’ directory. 

This means if you have upgraded from, for example, RRF v3.2.2 to RRF v3.3beta1 (the firmware, IAP and DWC files will be put in the ‘sys’ directory), then update to RRF v3.3beta2 by just uploading the firmware binary (which it will put in the ‘firmware’ directory), you’ll get a message that the IAP file is missing, because it expects to find it in ‘firmware’. Upload the IAP file from the v3.2.2 release, and it should put it in the ‘firmware’ folder.

Should you need to recreate the SD card see the [SD card page](https://docs.duet3d.com/User_manual/RepRapFirmware/SD_card) for a full description of the structure and contents, .

# Usual procedure

> **For updating the firmware on a Duet board in SBC mode, see [SBC setup for Duet](/User_manual/Machine_configuration/SBC_setup).**
The instructions below are for updating the firmware on Duet boards, in standalone mode. Using them to update a Duet board using a Single Board Computer (eg Raspberry Pi) may leave your board in an unresponsive state. 
{.is-warning}
1. Choose which version of RRF you want to use to update your Duet. Download the firmware zip bundle (named Duet2and3Firmware-X.X.zip, where X.X is the firmware version number), or individual binary files, from the [Duet3D Github repository](https://github.com/Duet3D/RepRapFirmware/releases).
For the latest stable release, use [https://github.com/Duet3D/RepRapFirmware/releases/latest](https://github.com/Duet3D/RepRapFirmware/releases/latest)
1. Connect to DuetWebControl.
1. In DWC, go to the System page and click the "Upload System Files" button.  
1. Browse to the new firmware zip file or individual firmware binary file you have downloaded.
1. After uploading, Duet Web Control will ask you whether you want to install the new firmware. If you are updating using the firmware zip bundle, this will update firmware, IAP, WiFi firmware and DWC all at once, so click **Yes**.
1. If you are only updating the main firmware, answer **Yes**. If you are updating WiFi firmware as well, answer **No** so that you can upload the new WiFi firmware too.
1. If your machine is idle when you upload the file but Duet Web Control does not offer to install it, then it doesn't recognise the filename as being a firmware binary. Check that you have uploaded the correct file.

# Fallback procedure #1

If you are unable to connect to your Duet from a browser, but you can connect to the Duet via USB using YAT or other serial terminal:

**Use caution when connecting your Duet to a PC via USB while VIN is still connected as this may lead to a ground loop situation. When connecting USB, and VIN power is not required, disconnect the PSU that supplies VIN power from the mains.** See here for more details: [USB ground loops](/User_manual/Overview/USB_ground_loops)

1. Take the on-board SD card out and put it in a computer
1. Delete any existing firmware files in /sys
1. Copy the new firmware binary file to the **/firmware** (if RRF 3.3beta1 or later is installed) or **/sys** folder (for earlier firmware versions)
1. Also copy the relevant IAP file (see Required Files, above) into **/sys**
1. If your board is a Duet WiFi, also copy the new DuetWiFiServer.bin file into /sys
1. Eject/safely remove the SD card from the PC
1. Replace the on-board SD card in the Duet 2 WiFi
1. Restart the Duet 2 WiFi and connect with Pronterface or YAT
1. Send command **M997 S0** to install the firmware.
1. Press the Disconnect button in Pronterface, then when the USB port disappears and reappears about 20 seconds later, press Connect.
1. Send M115 to confirm the new firmware version
1. If the board is a Duet WiFi, send **M997 S1** to install the WiFi module firmware

Note: when you run M997 S0, if your board is a Duet WiFi or Ethernet running old firmware, you may get an error message telling you that file DuetWiFiFirmware.bin or DuetEthernetFirmware.bin was not found. If that happens, copy or rename the Duet2CombinedFirmware.bin file to that filename and try again.

Duet 3 expansion board firmware can be manually updated with M997 Bn, where n is the CAN bus address of the expansion board.

# Fallback procedure #2

**Only use this if the firmware on the Duet has been corrupted, or if you have accidentally pressed the Erase button, so you can’t connect over USB with YAT..**

**Use caution when connecting your Duet to a PC via USB while VIN is still connected as this may lead to a ground loop situation. When connecting USB, and VIN power is not required, disconnect the PSU that supplies VIN power from the mains.** See here for more details: [USB ground loops](/User_manual/Overview/USB_ground_loops)

Before proceeding, connect the Duet to your PC using a USB cable and check whether the Duet is visible in the port list. Under Windows you can do this by going to **Device Manager** and expanding **Ports (COM and LPT)**. If **Duet 3D printer electronics** is listed, you should be able to connect using Pronterface or YAT unless some other program is already using the port. If **Bossa Port** is listed then the firmware has already been erased. If neither is listed:

* Try a different USB cable
* Try a different USB port

If you get the **Duet 3D printer electronics** port to appear, then firmware is already running on the Duet and you should use fallback procedure #1 or #2 instead. If you get **Bossa Port**, read on.

## Duet 3 Mini 5+ WiFi/Ethernet

The Duet 3 Mini 5+ supports an easy mechanism for updating the firmware directly.

* Connect the Duet to a PC (Windows, Mac or Linux) using a USB cable
* Press the Reset button twice quickly in succession. The Status LED should cycle slowly between dim and bright. This indicates that the bootloader is active.
* A new Mass Storage device should appear on the PC. Identify its drive letter (Windows) or mount path (Linux).
![duet_3_mini_5+_firmware_uf2.png](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_firmware_uf2.png)
* A directory listing of this mass storage device should show three virtual files. File CURRENT.UF2 is the existing firmware on the Duet.
* If you don't want to update the firmware at this time, you can press reset once or cycle power to reboot the Duet with the existing firmware.
* Copy the new Duet3Firmware-Mini5plus.uf2 file to the mass storage device
* The Status LED will flicker as the firmware is being written, then the Duet will reboot using the new firmware. The mass storage device will disappear from your PC and the Duet 3 COM port will appear.

## All other Duet boards

You will need a Windows, Apple Macintosh or Linux PC (can be Raspberry Pi).  
* Download the main firmware file from [https://github.com/Duet3D/RepRapFirmware/releases](https://github.com/Duet3D/RepRapFirmware/releases){target=blank}
* Verify that the downloaded firmware file has the correct size, as shown on the page you downloaded it from.
* Duet 3 MB6XD pre-production (green) boards only: if the Driver Enable Polarity Select jumper is fitted and in the position closest to the microcontroller, temporarily move it to the other position (the position closest to the edge of the board)
* Connect the Duet to your PC via USB.
* Verify that the 5V and 3.3V LED are on before continuing.
* Erase the firmware on the Duet by either:
  * Pressing the **Erase** button (next to the reset button on older Duets); or
  * Jumpering the erase jumper
  
  ![firmware_update_02_erase_pins.jpg](/manual/configuration/firmware_update_02_erase_pins.jpg =400x)
* Remove the erase jumper after a couple of seconds. 
  * On the MB6HC board it will either light up very dimly or be off. 
  * On the MB6XD board it will not light up at all, so just wait a few seconds before removing it.
  * On Duet 2, the Diag LED will light up. 
* Then press the **Reset** button (if one is present).
![firmware_update_01_reset_switch.jpg](/manual/configuration/firmware_update_01_reset_switch.jpg =400x)
* Now use either Bossa or SAM-BA to flash the firmware to the Duet (see below).
* Duet 3 MB6XD pre-production (green) boards only: if you moved the Driver Enable Polarity Select jumper, move it back to the original position.

## {.tabset}

### Bossa

Bossa is available for Windows, Apple macOS and Linux. 

**macOS users:** please see [this forum thread](https://forum.duet3d.com/topic/11445/flashing-firmware-on-mac-os-x) to resolve some issues with Bossa.

**Duet 3 MB6HC and MB6XD:** When BOSSA is used to flash the SAME70 processor in the MB6HC, the processor uses the USB port in high speed mode (unlike the processors used in other Duets). This means that the length and quality of the USB cable is especially important. USB cables that can be used to send GCode commands and receive replies to/from a Duet MB6HC/MB6XD that is running RepRapFirmware may not be suitable for flashing the firmware using BOSSA.

**Duet 3 MB6XD:** You must use the Duet3D version of BOSSA. If the Bossa port does not appear on the PC, check that the Driver Enable Polarity jumper is not in the wrong position (see earlier).

#### Tabs {.tabset}

##### Windows and macOS

* Download one of:
  * [Duet3D version of Bossa](https://github.com/Duet3D/BOSSA/releases/tag/1.9.1a-duet3d) (Windows only .exe file; supports 6HC/6XD)
  * [ShumaTech Bossa v1.9.1](https://github.com/shumatech/BOSSA/releases/tag/1.9.1) (Windows/Mac installer, does not support 6HC/6XD).
* Load Bossa. It usually detects the correct COM port automatically.
* Check boxes *Erase all*, *Lock*, and *Boot to flash*
* Browse to the firmware binary file to be installed, then press Write.
![paneldue_firmware_flashing.jpg](/hardware/paneldue/paneldue_firmware_flashing.jpg =500x)
*Note: picture of BOSSA above shows PanelDue firmware flashing, not RepRapFirmware flashing!*
* When the write completes, press *Verify*
* It is also possible to use the command line version (bossac) with this command line (replace COMxx by the correct COM port number): 
  `bossac --port=COMxx -b -U -e -w -v path\Duet2CombinedFirmware.bin -R`

##### Linux and DuetPi (after March 2022)

DuetPi releases newer than March 2022 contain a precompiled `bossac` binary that may be used with the Duet 2 or Duet 3 MB 6 HC/XD. To flash the firmware again using a Linux terminal, run:
```
bossac -e -w -v -b [file location and name]
```
where [file location and name] is where the firmware binary file is saved, eg `/opt/dsf/sd/firmware/Duet3Firmware_MB6HC.bin`

##### Linux and Raspberry Pi 

* Install Bossa with the following commands:
  ```
  sudo apt update && sudo apt install -y libwxgtk3.0-dev libreadline-dev
  git clone https://github.com/shumatech/BOSSA
  cd BOSSA
  make
  ```
* then send:
  ```
  ~/BOSSA/bin/bossac -e -w -v -b [file location and name]
  ```
  where [file location and name] is where the firmware binary file is saved, eg `/opt/dsf/sd/firmware/Duet3Firmware_MB6HC.bin`

##### Linux and Debian/Ubuntu

You must be on a recent Debian/Ubuntu release (`bullseye`/`bionic` respectively) in order to be able to install a BOSSA version that is capable of flashing the processor of the Duet 3 MB 6 HC or XD. 

* Install BOSSA first:
  ```
  sudo apt install bossa-cli bossa
  ```
* Launch it:
  ```
  bossa
  ```
* Check boxes *Erase all*, *Lock*, and *Boot to flash*
* Browse to the firmware binary file to be installed, then press Write.
![paneldue_firmware_flashing.jpg](/hardware/paneldue/paneldue_firmware_flashing.jpg =500x)
*Note: picture of BOSSA above shows PanelDue firmware flashing, not RepRapFirmware flashing!*
* When the write completes, press *Verify*
* It is also possible to use the command line version (bossac) with this command line (use `--port=/dev/ttyACM0` or similar if bossac fails to auto-detect the right serial port): 
  `bossac -b -U -e -w -v path\Duet2CombinedFirmware.bin -R`

### SAM-BA

SAM-BA is available for Windows and Linux.

* Download and install SAM-BA version 2.17 from [Microchip](http://www.microchip.com/en-us/development-tool/SAM-BA-IN-SYSTEM-PROGRAMMER)
* Load SAM-BA. It usually detects the correct COM port automatically.
  * For Duet WiFi/Ethernet, select **at91sam4e8-ek**. For Maestro, select **at91sam4s8-ek**. Press **Connect**. A connection error will be reported if the wrong board is selected.

![firmware_update_03_samba1.jpg](/manual/configuration/firmware_update_03_samba1.jpg =500x)

* In the Send File Name box enter or browse to the firmware binary file to be installed, then press **Send File** (leave the **Address** at the default of 0x400000). On completion it will invite you to lock the region; press **Yes**.
  * If desired, press **Compare sent file with memory**.
  * Press the **Execute** button next to the **Boot from Flash** option in the Scripts box. Then exit SAM-BA.

![firmware_update_04_samba2.jpg](/manual/configuration/firmware_update_04_samba2.jpg =500x)

* Press **Reset** on the board or on boards that don't have this button, remove and restore 5V power (usually provided by the USB cable).
* You should now be able to connect via USB/Pronterface or YAT.

# Updating individual firmware components

## Updating board firmware

* Upload the firmware binary file via DWC. You will be prompted if you want to install it, and the Duet will reboot after installation.

## Updating WiFi firmware

**Note**, this applies to Duet 2 WiFi and Duet 3 Mini 5+ WiFi only.

* Check the current version of the WiFi firmware by sending M122, and checking the WiFi section at the end for the WiFi firmware version.
* Upload the new DuetWiFiServer.bin file to the /sys directory (RRF 3.2) or /firmware directory (RRF3.2.2 or later) on the on-board SD card, by either:
  * If WiFi is working, via the System page (click the "Upload System Files" button) of DuetWebControl
  * or by temporarily moving the on-board SD card to a PC. If you copy it directly to the SD card on a PC, you must remove the version number from the filename so that its name on the SD card is exactly DuetWiFiServer.bin. If you upload it through the Settings page of DuetWebControl, the renaming will be done automatically.
* If DWC doesn’t ask if you want to install it, send command M997 S1 to install the new firmware. If you send this command from YAT or PanelDue, the update status will be reported.
* If you sent the M997 S1 command via the web interface, it will lose the connection and you should allow a minute or so for the firmware installation to complete before you press the Connect button.

## Updating DuetWebControl files

* Upload the new **DuetWebControl.zip** file by going to the DWC System page and clicking the "Upload System Files" button. Select the file and DWC will extract the files, put them in the right place, and refresh the browser to start using the new files immediately.
* If the web interface is not working, then move the SD card to a PC, delete or rename any existing /www folder, create a new one, and extract the contents of DuetWebControl.zip into it. Do not unzip the individual .gz files, leave them as they are on the SD card.

## Updating more than one firmware at a time

If you want to update the Duet firmware and WiFi firmware at the same time:

* Upload both firmware files via DWC System page (it will put them in the right place) but decline any offers to install them
* If you’re editing the SD card on a PC, upload the files to /firmware or /sys (depending on firmware version) on the on-board SD card, then replace the card in the Duet.
* The M997 command will install firmware files, and can be run from DWC or via a serial terminal (eg YAT) connected by USB. Specifying which firmwares you want to update in the S parameter. For example, M997 S0:1 will update the mainboard and WiFi firmwares, provided that the firmware files and IAP binary are present in /firmware or /sys (depending on firmware version) on the SD card.