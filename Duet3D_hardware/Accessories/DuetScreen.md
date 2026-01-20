---
title: Duet3D DuetScreen
description: 
published: false
date: 2026-01-20T13:30:02.111Z
tags: 
editor: markdown
dateCreated: 2026-01-16T14:53:50.598Z
---

![duetscreen1_medium.jpg](/hardware/duetscreen/duetscreen1_medium.jpg)

# Introduction

The Duet3D DuetScreen is a family of colour touch screen controllers for Duet 2 and Duet 3 Controllers. It is also compatible with other Controllers running RepRapFirmware. 

The current hardware implementation is the DuetScreen 7, other screen sizes are planned in the future.

The DuetScreen application is built on LVGL and provides a modern machine control UI,  initially focused on 3d printers. Its modular design allows for future versions for other machine types.

# Features

## Hardware Specification

### DuetScreen 7

| HARDWARE SPECIFICATION ||
|---|---|
| **Processor** | Allwinner T113-S3|
| **Processor features** | Dual-core ARM Cortex-A7, 1.2GHz, 128Mb RAM |
| **Screen Resolution** | 1024*600 |
| **Touch Screen** | 	Capacitive |
| **Networking** | Onboard WiFi module, option for internal or external antenna |
| **USB** | 2x USB 2.0 ports |
| **Serial** | 3x UART, 1x USART ^1^|
| **Other Comms** | 2x I2C ^1^ |
| **Buzzer** | On Board Piezo Buzzer |
| **Speaker** | Header to connect an 8 Ohm mono speaker |

Note: ^1^ Not populated in version 1.0 hardware or explicitly supported in the DuetScreen software.

## Operating limits

|:--|:--|
| **Input voltage** | 5V to 5.3V|
| **Input current** | 250-500mA, depending on enabled feature and brightness, excluding external devices on USB ^2^|
| **5V current on USB** | 500mA total ^2^ |
| **Maximum ambient temperature** | 70°C |

Note: ^2^ See the [[powering the screen]](/Duet3D_hardware/Accessories/DuetScreen#powering-the-duetscreen) section below.

## Software and Firmware notes

### DuetScreen 
The DuetScreen runs a lightweight version of linux based on [[Buildroot]](https://buildroot.org/), this is designed for embedded systems and allows for fast startup. The UI is built on [[LVGL]](https://lvgl.io/).

More information for developers is available on our github repositories (note currently private, will be made public in due course):
[DuetScreen](https://github.com/Duet3D/DuetScreen)
[buildroot-duetscreen](https://github.com/Duet3D/buildroot-duetscreen)

# Physical properties

## Dimensions

![duetscreen_7v1.0_dimensions.png](/hardware/duetscreen/duetscreen_7v1.0_dimensions.png)

## Mounting

The DuetScreen 7 has 6 mounting posts, these are M3 tapped, 10mm high posts soldered into the board. In many cases only the outer 4 will be required.

> Do not over torque screws into these mounting posts. They are rated to 0.5 Nm, however to mount the screen there is no need to go above 0.2 Nm
{.is-info}


## 3D model

A zip of the STEP file for the screen is here: [duetscreen_7_1.0_step.zip](/hardware/duetscreen/duetscreen_7_1.0_step.zip)

## Enclosure

### Duet3D reference enclosure
This enclosure is designed by [chrishamm](https://forum.duet3d.com/user/chrishamm), The Zip file contains both the STEP file and a FreeCAD 1.0 project.
[case_duetscreen_v1.0.zip](/hardware/duetscreen/case_duetscreen_v1.0.zip)

Tony has made some minor tweaks to Christians design which are available here: [case_duetscreen_v1.0_tony_mod.zip](/hardware/duetscreen/case_duetscreen_v1.0_tony_mod.zip)

# Connecting a DuetScreen

## Powering the DuetScreen

> All the power inputs are **NOT** isolated. This means that if you connect the DuetScreen to a mainboard via USB or UART, the screen and the mainboard should share a common ground. The easiest way to do this is to power the screen from the mainboard. If you do not do this, you may damage the DuetScreen or the mainboard.
{.is-warning}

The DuetScreen can be powered in the following ways:
- **5V_IN**: This is the recommended method when connecting over USB or WIFI. 
- **USB-C**: This is fine when using WIFI.
- **UART5**: This is a legacy method for compatibility with PanelDue wiring. It is not recommended for new installations.

## USB Ports
The DuetScreen has two USB ports:

**USB-A**: This port is a host port. 
- Its primary purpose is for USB Flash drives to be connected, currently for software upgrades and potentially for print files in the future.
- It is possible to use it to connect to a Duet3D mainboard, or external wifi module as well.

**USB-C**: This port can be a host or device port.
- It can be used to connect to a Duet3D mainboard in host mode
- It is possible to use it to add and external wifi modules or USB flash drives in host mode (although the USB A port may be easier)
- It can be used to connect to a PC in device mode for software debugging.
- It can be used to power the DuetScreen in either mode (assuming the attached device/host is able to supply power).


> A Duet3D mainboard CANNOT provide power to the DuetScreen via the USB-C. Always power the DuetScreen via the 5V_IN port when using USB
{.is-warning}

> USB hubs are supported **if they are NOT smart**. A smart hub is one that requires a driver to work. This includes most USB-C hubs. If you are using a USB-C hub, make sure it is a dumb hub. A dumb hub is one that does not require a driver to work. This includes most USB-A hubs. If in doubt, use a USB-A hub.{.is-info}

## Connecting to a Duet mainboard

Multiple methods are available to connect the DuetScreen to a mainboard. The recommended method is to use a USB cable. This allows for a consistent connection, not reliant on a wifi network and is the easiest to set up.

The Connection choice is made on the Settings->Connection screen:
![duetscreen-settings-connection-0.1.png](/hardware/duetscreen/duetscreen-settings-connection-0.1.png)

## Tabs {.tabset}

### USB
1. Connect the DuetScreen to the mainboard using a USB cable.
    - **Either** the **USB-A** and **USB-C** ports on the DuetScreen can be used.
    - If using the **USB-C** port, make sure to set the DuetScreen to USB **host mode**.
2. On the Settings->Connection screen, select the USB connection method.

![duetscreen-settings-connection-usb-0.1.png](/hardware/duetscreen/duetscreen-settings-connection-usb-0.1.png)

> When the DuetScreen detects a USB connection to a Duet3D mainboard, it will automatically send `M575 P0 S0` to configure the mainboard for USB communication.
{.is-info}

### WiFi

* The DuetScreen has a built-in WiFi module. 
  * If using the built-in WiFi module, set the Connection Method to Network and the USB-C port to `Internal WiFi`, this is done in the Settings > Connection page in the GUI.
* The DuetScreen also supports external WiFi modules with the `RTL8188FU` chipset. 
  * If using an external WiFi module, set the Connection Method to Network and connect it to the USB-A port on the DuetScreen, or use the USB-C port and set it to `USB-C Host`.
  * There are multiple variants of the `RTL8188` chipset. Currently the DuetScreen only supports `RTL8188FU`. Other variants are unlikely to work.

> You might need to reboot the DuetScreen after enabling the WiFi module.
{.is-info}

#### Connect the DuetScreen to a WiFi network

There are a couple of methods to connect the DuetScreen to a WiFi network.

1. You can connect to a network using the Settings > Connections page in the GUI.
This method is useful if you are setting up a single DuetScreen and you do not know the WiFi credentials in advance.

2. You can copy a file called `wpa_supplicant.conf` to the root of the microSD card. This file should be placed on the microSD card after it has been flashed. This method is the easiest if you are setting up multiple DuetScreens, or you know the WiFi credentials in advance.
This file should contain the WiFi credentials in the following format:
```
ctrl_interface=/var/run/wpa_supplicant
update_config=1
ap_scan=1

network={
    ssid="your-SSID"
    psk="your-PASSWORD"
    key_mgmt=WPA-PSK
}
```

#### Connect the DuetScreen to a Duet Mainboard

1. Ensure the DuetScreen is connected to the same WiFi network as the mainboard.
2. In the GUI, select the WiFi connection method.
4. Enter the IP address of the mainboard.

![duetscreen-settings-connection-wifi-0.1.png](/hardware/duetscreen/duetscreen-settings-connection-wifi-0.1.png)

### UART

> This method is for legacy support only to provide an easy upgrade path for PanelDue users. It is not recommended for new installations.
{.is-info}


1. Connect the DuetScreen to the mainboard using a UART cable.
   - Use connector `UART Duet` on the DuetScreen.
2. In the GUI, select the UART connection method, the USB-C Mode selection is irrelevant in this case.
3. Set the baud rate on the mainboard to `115200`. Use `M575 P1 S1 B115200` in config.g, this is similar to connecting a PanelDue, other than the default baud rate is 115200


![duetscreen-settings-connection-usb-0.1.png](/hardware/duetscreen/duetscreen-settings-connection-uart-0.1.png)

#### Wiring
For a Duet3 IO0 port for UART is as follows:

| Duet3 Mainboard - IO0 Connector     | DuetScreen - UART Duet connector    |
| ------------- | ------------- |
| 5V | 5V |
| io0.out | U5-R |
| GND | GND|
| io0.in | U5-T |


# Using a DuetScreen

## Tabs {.tabset}

### Dashboard

[![duetscreen-dashboard-status-01.png](/hardware/duetscreen/duetscreen-dashboard-status-01.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-dashboard-status-01.png){target=_blank} Text

<p style="clear:both"></p>

[![duetscreen-dashboard-status-02.png](/hardware/duetscreen/duetscreen-dashboard-status-02.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-dashboard-status-02.png){target=_blank} Text

<p style="clear:both"></p>

[![duetscreen-dashboard-status-03.png](/hardware/duetscreen/duetscreen-dashboard-status-03.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-dashboard-status-03.png){target=_blank} Text

<p style="clear:both"></p>

[![duetscreen-dashboard-status-04.png](/hardware/duetscreen/duetscreen-dashboard-status-04.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-dashboard-status-04.png){target=_blank}Text

<p style="clear:both"></p>

[![duetscreen-dashboard-status-05.png](/hardware/duetscreen/duetscreen-dashboard-status-05.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-dashboard-status-05.png){target=_blank}Text

<p style="clear:both"></p>

### Control

[![duetscreen-control-move-01.png](/hardware/duetscreen/duetscreen-control-move-01.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-control-move-01.png){target=_blank}Text

<p style="clear:both"></p>

[![duetscreen-control-tooltemp-01.png](/hardware/duetscreen/duetscreen-control-tooltemp-01.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-control-tooltemp-01.png){target=_blank}Text

<p style="clear:both"></p>

[![duetscreen-control-heightmap-01.png](/hardware/duetscreen/duetscreen-control-heightmap-01.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-control-heightmap-01.png){target=_blank}Text

<p style="clear:both"></p>

[![duetscreen-control-fan-01.png](/hardware/duetscreen/duetscreen-control-fan-01.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-control-fan-01.png){target=_blank}Text

<p style="clear:both"></p>


### Files

[![duetscreen-files-macros-01.png](/hardware/duetscreen/duetscreen-files-macros-01.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-files-macros-01.png){target=_blank}Text

<p style="clear:both"></p>


### Console

[![duetscreen-console-01.png](/hardware/duetscreen/duetscreen-console-01.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-console-01.png){target=_blank}Text

<p style="clear:both"></p>


### Settings

[![duetscreen-settings-general-01.png](/hardware/duetscreen/duetscreen-settings-general-01.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-settings-general-01.png){target=_blank}Text

<p style="clear:both"></p>

[![duetscreen-settings-connection-01.png](/hardware/duetscreen/duetscreen-settings-connection-01.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-settings-connection-01.png){target=_blank}Text

<p style="clear:both"></p>

[![duetscreen-settings-display-01.png](/hardware/duetscreen/duetscreen-settings-display-01.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-settings-display-01.png){target=_blank}Text

<p style="clear:both"></p>

[![duetscreen-settings-developer-01.png](/hardware/duetscreen/duetscreen-settings-developer-01.png =70%x){.align-right}](/hardware/duetscreen/duetscreen-settings-developer-01.png){target=_blank}Text

<p style="clear:both"></p>


# DuetScreen Software

## Writing DuetScreen Software to the SD card
1. Download the latest `sdcard.img` from the [release page on Github](https://github.com/Duet3D/DuetScreen/tags).
2. There are multiple programs to write an image file to an SD card:

    - We recommend using the [Raspberry Pi Imager](https://www.raspberrypi.com/software/), which has versions for Windows, MacOS, Linux and Raspberry Pi.
    - or use [balenaEtcher](https://www.balena.io/etcher/) on Windows, Linux and MacOS
    - or use `dd` on Linux and MacOS
   ```
   sudo dd if=sdcard.img of=/dev/sd# bs=4M
   ```
3. Insert the microSD card into the DuetScreen and power it on.


## Updating DuetScreen

Several methods are available to update the DuetScreen.

> Occasionally, an update may require the whole microSD card to be reflashed. This will be indicated in the release notes.
> In this case, follow the instructions in the [Flashing a new DuetScreen](#flashing-a-new-duetscreen) section above.
{.is-info}

The (`DuetScreen.tar.gz`) will be available with all releases that do not require a full SD card image reflash.

### Using the GUI
1. Copy the update file (`DuetScreen.tar.gz`) to the root directory of a USB flash drive.
1. Insert the USB flash drive into the DuetScreen.
1. In the GUI you will be prompted to update the DuetScreen.
1. If the update is successful, the DuetScreen will automatically reboot. This will appear as a brief flash and the GUI will return to the Dashboard screen.
1. The update will create an empty file called `upgraded` in the root directory of the USB flash drive. This file is used to indicate that the update was successful.

### Force Update
1. If the GUI is not working, you can force an update by renaming the update file to `update.tar.gz` and placing it in the **root directory of the USB flash drive OR microSD** card.
1. Insert the USB flash drive or microSD card into the DuetScreen.
1. *(If using a microSD card)* Power on the DuetScreen and it will automatically update.
1. The update will have succeeded if the `update.tar.gz` file is removed from the root directory of the flash drive or microSD card.

### Fallback
1. If the DuetScreen is still not working, you will have to reflash the microSD card with the latest image, see [Flashing a new DuetScreen](#flashing-a-new-duetscreen) above.

# Version History

# Tabs {.tabset}

## Version 1.0
- No significant changes from v0.3

## Version 0.3
- Change non required connectors to DNP

## Version 0.2
- Initial internal development version


