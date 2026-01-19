---
title: Duet3D DuetScreen
description: 
published: false
date: 2026-01-19T15:13:33.920Z
tags: 
editor: markdown
dateCreated: 2026-01-16T14:53:50.598Z
---

[Image of DuetScreen]




# Introduction

The Duet3D DuetScreen is a colour touch screen controller for the Duet ...

# Features

## Hardware Specification

## Operating limits

## Firmware notes

## Open Source

# Physical properties

## Dimensions

## Mounting

## 3D model and enclosure

[Enclosure from Andy E?]

# Connecting a DuetScreen

## Powering the Duet3D screen

> All the power methods are **NOT** isolated. This means that if you connect the screen to a mainboard, the mainboard must be powered by the same power supply as the screen. If you do not do this, you may damage the screen or the mainboard.
{.is-warning}

> The screen is designed to be powered by a 5V power supply. If you are using a 12V or 24V power supply, you will need to use a buck converter to step down the voltage to 5V. 
> **You will likely damage the screen and any connected devices if you do not do this**.
{.is-warning}

The Duet3D screen can be powered in the following ways:
- **5V_IN**: This is the recommended method. 
- **USB-C**: Relies on the connected host/device to supply sufficient power.
- **UART5**: This is a legacy method for compatibility with PanelDue wiring. It is not recommended for new installations.

## USB Ports
The Duet3D screen has two USB ports:

**USB-A**: This port is also a host port. 
- It can be used to connect to a Duet3D mainboard, wifi modules, or USB flash drives.

**USB-C**: This port can be a host or device port.
- It can be used to connect to a Duet3D mainboard, wifi modules, or USB flash drives in host mode.
- It can be used to connect to a PC in device mode for software debugging.
- It can be used to power the screen in either mode (assuming the attached device/host is able to supply power).


> A Duet3D mainboard CANNOT provide power to the screen via the USB-C. Always power the screen via the 5V_IN port when using USB
{.is-warning}

USB hubs are supported **if they are NOT smart**. A smart hub is one that requires a driver to work. This includes most USB-C hubs. If you are using a USB-C hub, make sure it is a dumb hub. A dumb hub is one that does not require a driver to work. This includes most USB-A hubs. If in doubt, use a USB-A hub.

## Connecting to a Duet mainboard

Multiple methods are available to connect the DuetScreen to a mainboard. The recommended method is to use a USB cable. This allows for the best performance and is the easiest to set up.

## Tabs {.tabset}

### USB
1. Connect the DuetScreen to the mainboard using a USB cable.
    - **Either** the **USB-A** and **USB-C** ports on the screen can be used.
    - If using the **USB-C** port, make sure to set the screen to USB **host mode**.
2. In the GUI, select the USB connection method.


> When the Duet3D screen detects a USB connection to a Duet3D mainboard, it will automatically send `M575 P0 S0` to configure the mainboard for USB communication.
{.is-info}

### WiFi

* The screen has a built-in WiFi module, it also supports external WiFi modules with the `RTL8188FU` chipset.
* If using the built-in WiFi module, the USB-C port must be set to `Internal WiFi`, this is done in the “Settings” page in the GUI.
* If using an external WiFi module, connect it to the USB-A port on the screen, or use the USB-C port and set it to `USB-C Host`.
* There are multiple variants of the `RTL8188` chipset. Currently the screen only supports `RTL8188FU`. Other variants are unlikely to work.

> You might need to reboot the screen after enabling the WiFi module.
{.is-info}



#### Connect the DuetScreen to a WiFi network

There are a couple of methods to connect the Duet3D screen to a WiFi network.

1. You can connect to a network using the Settings > Connections page in the GUI.
This method is useful if you are setting up a single screen and you do not know the WiFi credentials in advance.

2. You can copy a file called `wpa_supplicant.conf` to the root of the microSD card. This file should be placed on the microSD card after it has been flashed. This method is the easiest if you are setting up multiple screens, or you know the WiFi credentials in advance.
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

1. Ensure the Duet3D screen is connected to the same WiFi network as the mainboard.
2. In the GUI, select the WiFi connection method.
4. Enter the IP address of the mainboard.

### UART

> This method is for legacy support only to provide an easy upgrade path for PanelDue users. It is not recommended for new installations.
{.is-info}


1. Connect the Duet3D screen to the mainboard using a UART cable.
    - Use connector `UART Duet` on the screen.
2. In the GUI, select the UART connection method.
3. Set the baud rate on the mainboard to `115200`. use `M575 P1 S1 B115200` in config.g, this is similar to connecting a PanelDue, other than the default baud rate is 115200

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

[![duetscreen-dashboard-status-01.png](/hardware/duetscreen/duetscreen-dashboard-status-01.png =50%x){.align-right}](/hardware/duetscreen/duetscreen-dashboard-status-01.png){target=_blank} Text

<p style="clear:both"></p>

[![duetscreen-dashboard-status-02.png](/hardware/duetscreen/duetscreen-dashboard-status-02.png =50%x){.align-right}](/hardware/duetscreen/duetscreen-dashboard-status-02.png){target=_blank} Text

<p style="clear:both"></p>

[![duetscreen-dashboard-status-03.png](/hardware/duetscreen/duetscreen-dashboard-status-03.png =50%x){.align-right}](/hardware/duetscreen/duetscreen-dashboard-status-03.png){target=_blank} Text

<p style="clear:both"></p>

[![duetscreen-dashboard-status-04.png](/hardware/duetscreen/duetscreen-dashboard-status-04.png =50%x){.align-right}](/hardware/duetscreen/duetscreen-dashboard-status-04.png){target=_blank}Text

<p style="clear:both"></p>

[![duetscreen-dashboard-status-05.png](/hardware/duetscreen/duetscreen-dashboard-status-05.png =50%x){.align-right}](/hardware/duetscreen/duetscreen-dashboard-status-05.png){target=_blank}Text

<p style="clear:both"></p>

### Control

![duetscreen-control-move-01.png](/hardware/duetscreen/duetscreen-control-move-01.png)

<p style="clear:both"></p>


### Files

### Console

### Settings


# DuetScreen firmware

## Flashing a new DuetScreen
1. Download the latest `sdcard.img` from the release page.
2. Flash a microSD card with the image
   - use [balenaEtcher](https://www.balena.io/etcher/) on Windows and MacOS
   - use `dd` on Linux
        - ```bash
            sudo dd if=sdcard.img of=/dev/sdX bs=4M
            ```
3. Insert the microSD card into the Duet3D screen and power it on.


## Updating DuetScreen

Several methods are available to update the Duet3D screen.

{.is-info}
> Occasionally, an update may require the whole microSD card to be reflashed. This will be indicated in the release notes.
> In this case, follow the instructions in the [Flashing a new Duet3D screen](#flashing-a-new-duet3d-screen) section above.

### Using the GUI
1. Copy the update file (`DuetScreen.tar.gz`) to the root directory of a USB flash drive.
1. Insert the USB flash drive into the Duet3D screen.
1. In the GUI you will be prompted to update the screen.
1. If the update is successful, the screen will automatically reboot. This will appear as a brief flash and the GUI will return to the home screen.
1. The update will create an empty file called `upgraded` in the root directory of the USB flash drive. This file is used to indicate that the update was successful.

### Force Update
1. If the GUI is not working, you can force an update by renaming the update file to `update.tar.gz` and placing it in the **root directory of the USB flash drive OR microSD** card.
1. Insert the USB flash drive or microSD card into the Duet3D screen.
1. *(If using a microSD card)* Power on the screen and it will automatically update.
1. The update will have succeeded if the `update.tar.gz` file is removed from the root directory of the flash drive or microSD card.

### Fallback
1. If the screen is still not working, you will have to reflash the microSD card with the latest image, see "Flashing a new DuetScreen" above.

# Version History

# Tabs {.tabset}

## Version 1


## Version 0.x 

