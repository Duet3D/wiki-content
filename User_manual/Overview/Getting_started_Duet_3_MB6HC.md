---
title: Getting Started With Duet 3 MB6HC
description: 
published: true
date: 2021-12-14T12:29:22.642Z
tags: 
editor: markdown
dateCreated: 2021-11-29T15:18:25.164Z
---

<!-- Migrated not updated! -->

<!-- ![duet_3_mb6hc_getting_connected_01.jpg](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_getting_connected_01.jpg =200x) -->

![duet_3_mb6hc_v0.6_top.jpg](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_v0.6_top.jpg =400x)

# Introduction

Congratulations on your new Duet 3 MB6HC! This page will guide you through getting connected, updating firmware, setting up initial configuration.

First, read the warnings! See [Warnings](/User_manual/Overview/Warnings) and [Fire safety](/User_manual/Overview/Fire_safety) for precautions you must take.

<!-- 
## Package contents

![duet_3_mini_5+_getting_started_03.jpg](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_getting_started_03.jpg =600x)

* 1 x Duet 3 Mini 5+ (WiFi or Ethernet)
* 1 x Connector kit, including:
  * Würth Elektronic WR-WTB series connectors (Molex-KK compatible)
  * Ferrules for power input and bed heater
  * Connectors and crimps for hotend heaters.
* 100 x crimps for connectors
* 1 x SD Card
* 1 x Single Board Computer (SBC) ribbon cable
* 1 x External antenna (WiFi version only)
 -->

## Standalone mode vs SBC mode

The Duet 3 MB6HC is designed to either be directly connected to a network, or to connect to a Single Board Computer (SBC) like a Raspberry Pi. All standard printer functions, different kinematics, the network UI ([Duet Web Control](/User_manual/Reference/Duet_Web_Control_Manual)) etc work in both modes.

SBC mode allows part of the functionality to be handled by the SBC. For example an HDMI or Pi touch screen can be used as the UI, The Pi can act as the network server and plugins for Duet Software Framework on the Pi allow for adding custom features for more advanced control and monitoring.

# Minimum configuration

## {.tabset}

### With Raspberry Pi (SBC)

* Duet 3 MB6HC mainboard
* Raspberry Pi or other compatible Single Board Computer (SBC). Initially this has to be Model 3B or 4 but we may support some other models in the future.
* Ribbon cable to connect the Raspberry Pi to the Duet. If using the version 0.5 Duet 3 main board, this must be 40-pin. If using a version 0.6 and later Duet main board, use 26-pin to 40-pin. Recommended length is 100 to 150mm. Longer cables will degrade the SPI signal between the Raspberry Pi and the Duet.
* Power supply (min. 12V. max 32V, recommended 24V) and cables
* Stepper motors, heaters, thermistors, endstop switches etc. as required
* Supplied SD card with Duet Pi image in the Raspberry Pi
* No SD card in the Duet 3

### Standalone mode (no SBC)

* Duet 3 MB6HC mainboard
* Power supply (min. 12V. max 32V, recommended 24V) and cables
* Stepper motors, heaters, thermistors, endstop switches etc. as required
* Supplied SD card in the Duet 3

## Optional extras

* Duet 3 expansion board(s) and/or tool boards
* RJ11 twisted pair cables to connect the expansion board(s).  When connecting a version 0.5 Duet 3 main board to an expansion board, the wires must be crossed. When connecting a version 0.6 or later Duet 3 main board to an expansion board, or connecting two expansion boards together, the cable wiring must be straight-through. Only a single twisted pair is required, connected to pins 3 and 4 (the middle 2 pins) of the connector. Pins 2 and 6 on the version 0.6  Duet 3 main board are the connections for the secondary CAN bus, but the firmware does not use this yet.
* 2-core red/black cable to daisy chain power to the expansion board(s)

# Getting started

## {.tabset}

### Running with SBC

Here is a photo showing a bench setup comprising a version 0.5 Duet 3 main board, Raspberry Pi 3B+, and one early prototype Duet 3 expansion board. **Caution! Note the orientation of the 40-pin ribbon cable.** The connector on the Raspberry Pi is not keyed, so be very careful to connect the ribbon cable the right way round and in the correct position.  **Caution!** Note the order of the VIN + and - terminals on the Duet 3 main board (the red wire is +ve), which is not the same as on Duet 2!

![duet_3_mb6hc_getting_connected_02_sbc.jpg](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_getting_connected_02_sbc.jpg =750x)

The position of the ribbon cable may appear awkward. However, our intention is that the Raspberry Pi be mounted below or above the Duet, so that the USB and Ethernet connectors on the Raspberry Pi line up with the right hand edge of the Duet. It's also possible to position the Pi on the opposite side of the Duet and run the ribbon cable underneath the Duet, but this requires a 200mm long cable.

On the version 1.0 and 0.6 board, jumpers are provided to allow you to select whether the Duet powers the Pi, the Pi powers the Duet, or neither. [See here for more details and limitations](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6HC_Hardware_Overview#h-5v).

Once physically connected, see the [SBC Setup for Duet 3](/User_manual/Machine_configuration/SBC_setup) to connect over your network.

### Running in standalone mode

Duet 3 main boards^1^ have the ability to run without a connected Single Board Computer (SBC), in standalone mode, by inserting a micro SDHC card in the socket on the Duet and using the local Ethernet port to connect to your router.

An SD card is usually supplied with the Duet 3 mainboard. On boards shipped before May 2020, this was formatted for use in a connected Raspberry Pi, and does not have the files on it to run the Duet 3 in standalone mode. Duet 3 boards shipped after this date should have a dual-format image, with a FAT32 partition and the files to start the Duet 3 in standalone mode, and a partition with the files to boot a connected Raspberry Pi.

To run the Duet 3 in standalone mode, the SD card must be formatted correctly and be populated with configuration files, homing files and web server files; see [SD Card](/User_manual/RepRapFirmware/SD_card) for details on configuring the SD Card. The board will run in standalone mode if the SD card is present and contains a **sys/config.g** file; otherwise it will wait to be contacted by the Raspberry Pi or other single board computer.

^1^ except for some very early prototypes (before version 0.6)

#### Initial USB setup

You will need to initially setup the Duet 3 using a USB cable (USB A to Micro B). Make sure the USB cable is capable of carrying power AND data; some USB cables are power-only. Then follow the guide here: [Getting connected to your Duet](/How_to_guides/Getting_connected/Getting_connected_to_your_Duet).

After following this guide, you should be able to connect to your Duet 3 over your network, and connect to the Duet Web Console (DWC).

# Commissioning

## Familiarise yourself with Duet Web Control

The Duet Web Control user interface is the best way to control your Duet. A comprehensive manual is here: [Duet Web Control Manual](/User_manual/Reference/Duet_Web_Control_Manual)

## Upload machine configuration

So far, the Duet is not configured for your machine. Use the [RepRapFirmware Configuration Tool](https://configtool.reprapfirmware.org/Start) to produce a set of files as a zip file to upload to the Duet. Upload the zip file (no need to extract the files) to the Duet using Duet Web Control, by going to System > Upload System Files. For more information, see [Duet Web Control Manual](/User_manual/Reference/Duet_Web_Control_Manual)

# Connecting hardware

## Wiring

See the [Connecting hardware section of the User manual](/User_manual/Connecting_hardware) for choosing, connecting and testing each component.

See here for the [Duet 3 Mainboard 6HC Wiring Diagram](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6HC_Hardware_Overview)

## Connecting a PanelDue

A PanelDue can be connected to connector IO_0 using a 4-core cable wired like this.

![displays_paneldue_d3_wiring_01.jpg](/manual/displays/displays_paneldue_d3_wiring_01.jpg =400x) ![displays_paneldue_d3_wiring_02.jpg](/manual/displays/displays_paneldue_d3_wiring_02.jpg =400x)

Include ```M575 P1 B57600 S1``` in your config.g to enable the PanelDue.

See [Connecting a PanelDue](/en/User_manual/Connecting_hardware/Display_PanelDue#option-1-4-way-cable) for more information.

## Connecting CAN bus expansion boards

Additional expansion boards add further functionality to your Duet 3 mainboard. These are connected by CAN bus. For an overview of connecting these, see [CAN connection basics](/User_manual/Machine_configuration/CAN_connection).

If you connect one or more expansion boards via CAN (as shown in the earlier photo), you must install the two CAN termination jumpers on the last board in the chain, but not on any additional boards in the middle of the chain.

For a list of expansion boards that can be connect to a Duet 3 mainboard, see the 'CAN expansion' section of the [Duet 3 family](/Duet3D_hardware/Duet_3_family) page.

# Duet 3 and RepRapFirmware 3

## Duet 3 RepRapFirmware notes

Duet 3 runs RepRapFirmware 3, which supports flexible pin allocations so that any output pin can be used to drive a heater, fan or other device. No temperature sensors, heaters, fans, endstop switches or Z probes are defined by default; so you must define the ones you want using appropriate commands in config.g. See [RepRapFirmware overview](/User_manual/RepRapFirmware/RepRapFirmware_overview) and [Migrating from RRF2 to RRF3](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3).

## Updating Duet 3 main board firmware

There are three ways of updating the main board firmware:

1. If using an attached Raspberry Pi SBC, issue these commands on the RPi:
```
sudo apt update
sudo apt upgrade
```
2. If running standalone using the Ethernet port on the Duet to provide the network interface, you can [update the firmware in Duet Web Control using the 'usual procedure'](/User_manual/RepRapFirmware/Updating_firmware#usual-procedure)
3. As a 'fallback' recovery, update firmware via BOSSA on USB, on either Windows or Raspberry Pi (see below)

**NOTE:** When BOSSA is used to flash the SAME70 processor in the MB6HC, the processor uses the USB port in high speed mode (unlike the processors used in other Duets). This means that the length and quality of the USB cable is especially important. USB cables that can be used to send GCode commands and receive replies to/from a Duet MB6HC that is running RepRapFirmware may not be suitable for flashing the firmware using BOSSA.

## {.tabset}

### To update via USB (using a Windows host)

* You will need to install Bossa 1.9.1 from the [Shumatech Github page](https://github.com/shumatech/BOSSA/releases) on your PC.
* Place a jumper across the ERASE pins on the Duet 3 and power it up to erase the firmware. The Bossa port should appear on your PC (if it doesn't, try pressing the Reset button).
* The erase jumper is located in the center of the board near the large CPU chip.
* Remove the ERASE jumper
* Load Bossa and select the correct COM port. It should identify the device as ATSAM70x20.
* Check the Erase All, Boot to Flash and Lock boxes
* Download the latest RRF 3 release [from Github here](https://github.com/Duet3D/RepRapFirmware/releases)
* Browse to the firmware binary, and select Write.
* When that completes, select Verify.
* When that completes successfully, press the Reset button on the Duet 3 to restart the Duet in normal mode.

### To update via USB (using a Raspberry Pi host)

Part 1: Install bossa on the Pi with these commands:

```
sudo apt update && sudo apt install -y libwxgtk3.0-dev libreadline-dev
git clone https://github.com/shumatech/BOSSA
cd BOSSA
make
```

Part 2: Connect the Duet to the Pi.

* Use a data capable USB cable from any port on the Pi to the Duet. 
* It is OK to power the Duet from only the USB (which implies the Pi has its own power)
* It is OK to power the Duet with VIN
* Do one or the other; do not power both ways. 
* If the Pi is part of a Duet 3 with ribbon cable, it is OK to leave this attached. 

Part 3: Obtain firmware

* If the Pi is part of a Duet 3, firmware will be in:
  /opt/dsf/sd/firmware/Duet3Firmware_MB6HC.bin (RRF v3.3 and later) or
  /opt/dsf/sd/sys/Duet3Firmware_MB6HC.bin (RRF v3.2 and earlier)
* If your Pi is not part of a Duet, obtain firmware from the [RepRapFirmware Github repository](https://github.com/Duet3D/RepRapFirmware/releases)

Part 4: Run bossa

* Place a jumper across the ERASE pins on the Duet 3 and power it up to erase the firmware. The Bossa port should appear on your PC (if it doesn't, try pressing the Reset button).
* The erase jumper is located in the center of the board near the large CPU chip.
* Remove the ERASE jumper.
* then send:
`~/BOSSA/bin/bossac -e -w -v -b [file location and name]`
where [file location and name] is where the firmware binary file is saved, eg /opt/dsf/sd/sys/Duet3Firmware_MB6HC.bin

* This should take about one minute to run.  If it hangs, try reset and/or reset erase again on the Duet board. 

## Firmware updating notes

### Updating Duet 3 from RRF3.0 Beta 12 to 3.0 Final via DWC

Occasionally updating the firmware in standalone mode or SBC mode from some of the beta firmware releases will fail with an error message saying **"Error: In-application programming binary "Duet3iap_sd_MB6HC.bin"** not found. Alternatively, if updating in SBC mode, the sudo apt upgrade will fail, or appear to succeed, but the firmware itself is never updated when verified by sending M115.

The easiest way to temporarily get the firmware back in sync, is to use the Bossa flash method described above, or, if USB connection is inconvenient, and you still have network access to the Duet 3, you can use the DWC update method described below.

* Start by downloading the RRF3.0 IAP files listed here and then uploading the files to the /sys folder in DWC:
* [Duet3iap_sd_MB6HC.bin](https://github.com/Duet3D/RepRapFirmware/releases/download/3.0/Duet3iap_sd_MB6HC.bin)
* [Duet3iap_spi_MB6HC.bin](https://github.com/Duet3D/RepRapFirmware/releases/download/3.0/Duet3iap_spi_MB6HC.bin)
* [Duet3_SDiap_MB6HC.bin](https://github.com/Duet3D/RepRapFirmware/releases/download/3.0/Duet3_SDiap_MB6HC.bin)
* Then download the firmware bin file itself and upload that separately as well:
* [Duet3Firmware_MB6HC.bin](https://github.com/Duet3D/RepRapFirmware/releases/download/3.0/Duet3Firmware_MB6HC.bin)
* It should be detected by DWC and update the firmware. The missing IAP files are now in place, and the update should succeed. You can now proceed to upload the 3.2 firmware zip bundle to get up to date.
* [Duet2and3Firmware-3.2.zip](https://github.com/Duet3D/RepRapFirmware/releases/download/3.2/Duet2and3Firmware-3.2.zip)

### Updating Duet 3 expansion board firmware

Unless the bootloader has been corrupted, the expansion board firmware can be updated over CAN.

* Ensure that the correct expansion board firmware binary (for example, Duet3Firmware-EXP3HC.bin for the 3-driver high current expansion board) is present in the /sys folder (on the RPi if using it, or on the SD card if running standalone)
* If the expansion board is already communicating with the main board, send M997 B# where # is the address of the expansion board. The expansion board will commence a firmware update and the DIAG LED will go out for a while. When the update is complete, it will flash and re-sync with the main board.
* Alternatively, set the expansion board address to zero (switches all off) and press the Reset button on the expansion board to commence the firmware update (or power down, change the switches, and power up again). When re-syncing is complete, change the switches back to the correct board address and press Reset again (or power down, change the switches, and power up again).
* You can check the firmware version installed on an expansion board by sending M115 B# where has is the board number.

Note: after updating expansion board firmware, you must restart the main board or at least re-run config.g in order to create any sensors, heaters, fans etc. that you have configured on that board in config.g.

### Error codes

The bootloader flashes the following error codes on the DIAG LED on the expansion board:

* 2 flashes: invalid firmware
* 3 flashes: bad firmware CRC
* 4 flashes: the bootloader requested a firmware data block from the main board, but the main board didn't respond in time
* 5 flashes: the main board reported that it didn't have the correct firmware file in its /sys folder
* 6 flashes: the main board reported that the file offset requested by the bootloader was beyond the length of the file
* 7 flashes: the main board encountered some other error in trying to fetch and return a block of firmware data
* 8 flashes: bootloader internal error (no buffer available)
* 9 flashes: bootloader was unable to initialise the flash memory controller
* 10 flashes: bootloader was unable to unlock flash memory
* 11 flashes: bootloader was unable to erase flash memory
* 12 flashes: bootloader was unable to write flash memory
* 13 flashes: bootloader was unable to lock flash memory

*Note: continuous rapid flashing indicated that the CAN connection is lost.*

### If the bootloader becomes corrupted

Follow the process for [Updating the bootloader on Duet 3 expansion and tool boards](/User_manual/RepRapFirmware/Updating_bootloader)

# Duet Software Framework on Raspberry Pi

Duet Software Framework is the bundle of software programs running on the Raspberry Pi that connects to and controls the Duet 3.

## Setting up the Raspberry Pi

Duet 3 mainboards are supplied with an SD card loaded with the Raspian OS suitable for Raspberry Pi 2, 3B+ or 4. The following page will outline how to get setup initially, and what to do if there are issues.

[SBC Setup for Duet 3](/User_manual/Machine_configuration/SBC_setup)

## DuetPi

DuetPi is the official Debian image based on Raspbian that is customised for the Duet 3. If you are not very familiar with the Raspberry Pi, it is strongly recommended to use it instead of the stock Raspbian image.

There are two versions of DuetPi available, one with and another one without GUI:

* [DuetPi Lite](https://pkg.duet3d.com/DuetPi-lite.zip) (without GUI)
* [DuetPi](https://pkg.duet3d.com/DuetPi.zip) (with GUI)

These versions are targeted at ARMv7 processors like the Raspberry Pi 3 or 4.

Once downloaded, use a flashing application like Win32DiskImager or baleanaEtcher. You can follow [the official Raspberry Pi instructions](https://www.raspberrypi.org/documentation/installation/installing-images/) for further details.

When the image of your choice has been flashed, the Duet 3 can be connected to via the hostname "duet3". It comes preconfigured with mDNS enabled (via avahi), SSH activated and the standard credentials "pi" and "raspberry". To set up WiFi, you can edit "wpa_supplicant.conf" on the SD card (first partition).

As soon as your Raspberry Pi has established an internet connection, it is recommended to install the latest updates. To do so, connect via SSH (e.g. via PuTTY) to your Raspberry Pi or open a terminal and run

```
sudo apt-get update
sudo apt-get upgrade
```

This will install the latest software components and the latest RepRapFirmware version on your Duet 3.

## Raspbian Setup Guide

If you have the latest Raspbian image installed on your Raspberry Pi, follow these instructions to get started with DSF. These steps are obsolete if you are using DuetPi.

### Enabling SPI

To provide a controller base for the Duet, the first SPI port on the Linux controller board is used. If Raspbian operating system is used, the SPI driver can be enabled by doing the following steps:

1. Run

`sudo raspi-config`

<!-- (Alternatively `sudo raspi-config nonint do_spi 0` will enable SPI with a single command) -->

2. Navigate to **Interface options**

3. Go to **P4 SPI**

4. Confirm to enable the SPI interface

This will ensure the SPI driver is loaded and Duet Software Framework can use it for communication to the Duet board.

Once done, run

`ls /dev/spidev*`

and verify that `/dev/spidev0.0` has been created. This device node is required for DuetControlServer. If you wish to use another SPI device, adjust the SPI values in `/opt/dsf/conf/config.json` before starting DuetControlServer.

Please note that DCS uses SPI buffer sizes of 8KiB whereas the default Raspbian buffer size is limited to 4KiB. In order to change it, you can adjust the boot arguments by creating a new file `/etc/modprobe.d/spidev.conf`:

`echo "options spidev bufsiz=8192" | sudo tee /etc/modprobe.d/spidev.conf`

After that, restart your system to apply the new buffer size.

### Software Installation

*Note that the following steps are not required if you are already running DuetPi*

To obtain the required software packages on a Debian-based distribution running an ARMv7 processor (RaspPi 3 or newer) execute the following commands:

```
wget -q https://pkg.duet3d.com/duet3d.gpg
wget -q https://pkg.duet3d.com/duet3d.list
sudo mv duet3d.gpg /etc/apt/trusted.gpg.d/
sudo mv duet3d.list /etc/apt/sources.list.d/duet3d.list
sudo chown root:root /etc/apt/trusted.gpg.d/duet3d.gpg
sudo chown root:root /etc/apt/sources.list.d/duet3d.list
```

If you wish to use the latest unstable DSF components, you can run the following commands instead:

```
wget -q https://pkg.duet3d.com/duet3d.gpg
wget -q https://pkg.duet3d.com/duet3d-unstable.list
sudo mv duet3d.gpg /etc/apt/trusted.gpg.d/
sudo mv duet3d-unstable.list /etc/apt/sources.list.d/duet3d-unstable.list
sudo chown root:root /etc/apt/trusted.gpg.d/duet3d.gpg
sudo chown root:root /etc/apt/sources.list.d/duet3d-unstable.list
```

Please be aware that the unstable repository is meant for beta testers and that it can contain experimental code. For production use, the stable repository is strongly recommended.

In case you were using the stock package feed, make sure to remove it before you run `apt-get`:

`sudo rm /etc/apt/sources.list.d/duet3d.list`

Once this is done, you can install HTTPS support for APT, update the package feeds, and install the DSF bundle:

```
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install duetsoftwareframework
```

#### Downgrading from unstable to stable versions

To downgrade from an installed unstable version to a stable release follow the following steps:

1. Change back unstable to stable in /etc/apt/sources.list.d/duet3d.list:

`sudo bash -c "echo 'deb https://pkg.duet3d.com/ stable armv7' > /etc/apt/sources.list.d/duet3d.list"`

2. Refresh the package lists:

`sudo apt update`

3. Remove potentially left-over RRF packages

`rm -f ./reprapfirmware*.deb`

4. Download the latest stable RepRapFirmware package

`apt download reprapfirmware/stable`

5. Downgrade RepRapFirmware

`sudo dpkg -i --force-depends ./reprapfirmware*.deb`

6. Downgrade DSF:

`sudo apt install -y --allow-downgrades duetsoftwareframework/stable duetcontrolserver/stable duetwebserver/stable duetpluginservice/stable duettools/stable duetruntime/stable duetwebcontrol/stable`

### Duet Control Server

Now you have installed Duet Software Framework, but none of the services has been started yet. As a first step, it is important to start the main application of DSF called Duet Control Server. To do this, make sure you have the Duet 3 connected to your Linux board via the ribbon cable and  run

`sudo systemctl start duetcontrolserver`

This should complete without an error. If it fails, check why it did so by running

`sudo journalctl -u duetcontrolserver`

To make it start automatically on boot, run

`sudo systemctl enable duetcontrolserver`

If you wish to change parameters of the DCS configuration, edit `/opt/dsf/conf/config.json`. To revert to factory defaults, you may delete this file as well and restart DCS. In this case, DCS will restore it automatically.

### Duet Web Server

Once DCS has been started, you can start the web server which provides the web interface. This can be achieved by running

`sudo systemctl start duetwebserver`

If that fails for some reason, run

`sudo journalctl -u duetwebserver`

to figure out why. Once it has been started, you can make it start on boot as well by running 
`sudo systemctl enable duetwebserver`

If you wish to change parameters of the DWS configuration, edit `/opt/dsf/conf/http.json`.

### Duet Web Control

For your convenience, Duet Web Server serves the user interface, Duet Web Control, from the /www folder of the virtual SD card directory (see below). This effectively mimics the old behaviour of previous Duet controllers. Once DWS has been started, open a browser of your choice and go to http://127.0.0.1.  If you do not wish to connect manually every time you do this, you can specify the hostname of your Linux board instead of 127.0.0.1 as well (e.g. rasperrypi on Raspbian).

## Virtual SD card

The first SD card (0:/) is emulated by DCS and its default directory is `/opt/dsf/sd`. It may changed by editing the BaseDirectory value in `/etc/dsf/config.json`.

## Updates

### Firmware update on recent versions (DCS >= 1.0.2.0)

Before you can update DSF, it is important to install the latest RepRapFirmware version first.  If you are using recent DCS (>= 1.0.2.0) and RRF versions, you can put the updated Duet3Firmware.bin file in the /sys directory and run "M997". This will trigger a firmware update. For convenience, you can simply upload Duet3Firmware.bin on the System page of DWC2 and confirm the update.

The latest firmware file can be obtained here: https://github.com/Duet3D/RepRapFirmware/releases

### Firmware update on older versions and fallback method

If you are upgrading from older DSF/RRF versions, you have to put the Duet 3 into flashing mode before you can proceed. To do this, run "M999 PERASE" (without quotes) either from the web interface or from localhost (you may use /opt/dsf/bin/CodeConsole for this). That will put the Duet 3 into flashing mode. You can skip this step by bridging the two pins of ERASE header in the centre of the board.

After that, connect your Duet 3 via microUSB to your computer and install [BOSSA](https://github.com/shumatech/BOSSA/releases). When you launch it, check the "Erase All" and "Boot from Flash" check boxes and select [Duet3Firmware_MB6HC.bin](https://github.com/Duet3D/RepRapFirmware/releases). Next, press the "Flash" button and wait for the operation to complete. When it does, power-cycle your board and it should have the latest firmware installed.

### Updating DSF

Since DSF is provided as a standard Debian package feed, the update is quite trivial.  To update it, run a standard system update on the attached SBC:

```
sudo apt-get update
sudo apt-get upgrade
```

This will automatically install the latest DSF components.

If you wish to upgrade to the unstable package feed, check out the docs on GitHub [here](https://github.com/Duet3D/DuetSoftwareFramework/wiki/SBC-Setup-Guide#unstable-package-feed).

## Reporting issues

Please use the forum to report any issues: [https://forum.duet3d.com/](https://forum.duet3d.com/)