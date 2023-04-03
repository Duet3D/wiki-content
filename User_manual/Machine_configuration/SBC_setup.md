---
title: Single Board Computer (SBC) setup for Duet 3
description: Duet 3 mainboards are supplied with an SD card loaded with the Raspberry Pi OS suitable for Raspberry Pi 3B+ or 4. This page will outline how to get setup initially, and what to do if there are issues. 
published: true
date: 2022-09-15T12:16:31.699Z
tags: 
editor: markdown
dateCreated: 2021-11-30T22:13:44.507Z
---

# Introduction


> This guide describes how to setup an **optional** Single Board Computer (SBC) with Duet 3, allowing the Duet to run in 'SBC mode'. If you don't have, or don't want to connect, an SBC to your Duet, it is not necessary; you can run your Duet in 'standalone' mode, and ignore this guide.
{.is-info}

[![sbc_setup_03.jpg](/manual/configuration/sbc_setup_03.jpg =50%x){.align-right}](/manual/configuration/sbc_setup_03.jpg){target=_blank}Duet 3 mainboards support connecting a Single Board Computer (SBC) such as a Raspberry Pi. The SBC runs its operating system (Raspberry Pi OS in the case of Raspberry Pi), with the Duet Software Framework (DSF) running on the Raspberry Pi OS. DSF is the bundle of software programs that connects to and controls the Duet 3.

SBC mode allows part of the functionality to be handled by the SBC. This offers a number of benefits:
- Faster network transfer speeds
- Support of plugins requiring more than RRF/DWC
- Usage of external HDMI or DSI screens
- Easy software and firmware update via package manager
- Optional webcam integration
- Optional HTTPS support

Duet 3 mainboards are supplied with an SD card pre-loaded with the Raspberry Pi OS + DSF, suitable for use with Raspberry Pi 3 or 4. This guide assumes you are using a Raspberry Pi and the SD card image specifically set up for Duet 3. In the future other SBCs may be officially supported. 

Most of the time you can start at Step 2 below, as you will receive an SD card pre-flashed with the Raspberry Pi OS + DSF. If your SD card supplied with the Duet 3 mainboard becomes damaged or lost, start at step 1.

If you want to install DSF on an existing Raspberry Pi OS installation, or on a custom Debian board/installation, please see this page: [Duet Software Framework (DSF) on other boards](/User_manual/Machine_configuration/DSF_Other){target=_blank}

# 1. Flash the image file

> If you are planning to use the SBC for more than plain 3D printing or if you have an SBC with little RAM + external display, it is HIGHLY recommended to obtain a **class A1- or A2-rated microSD** card instead of the shipped SD card. If your SD card speed is insufficient, you may experience occasional problems when data between the SBC and Duet is exchanged. 
{.is-warning}

![sbc_setup_01.png](/manual/configuration/sbc_setup_01.png =50%x){.align-right}

1. You will need at least an 8Gb class 10 SD card.
1. There are multiple programs to write an image file to an SD card. If there are any issues with this step, try following the [Raspberry Pi documentation for flashing OS images](https://www.raspberrypi.org/documentation/installation/installing-images/){target=_blank}
1. You need to use the latest Raspberry Pi image **setup for Duet 3** that is available from the Duet 3D software site. You may choose the lite image for a GUI-less setup and the full variant for setups where a display is attached:
[https://pkg.duet3d.com/DuetPi-lite.zip](https://pkg.duet3d.com/DuetPi-lite.zip){target=_blank}
[https://pkg.duet3d.com/DuetPi.zip](https://pkg.duet3d.com/DuetPi.zip){target=_blank}
1. Under Windows, MacOS and Linux, flash the file using  using [balenaEtcher](https://www.balena.io/etcher/){target=_blank}
1. Select the image, select the SD card. Click flash. Will take about 5 minutes.
1. Once this is complete Windows may prompt you to format the disk. **Do not do this**, it is because Windows does not recognise one of the partitions on the image.

# 2. Setup wifi

1. This step is optional if you are going to connect to the SBC using an Ethernet network, or if you are going to plug a monitor, keyboard and mouse into the SBC.

1. Navigate to the “boot” partition on the SD card *(note on Windows the other partition will be inaccessible and give an error if you try and access it, this is normal as it is formatted in a filesystem that Windows does not support).*

1. Create a [new file named “wpa_supplicant.conf”](https://www.raspberrypi-spy.co.uk/2017/04/manually-setting-up-pi-wifi-using-wpa_supplicant-conf/) (exact naming is critical, do not have a ".txt" at the end of the file).

1. In a text editor add the following:
```
country=GB
update_config=1
ctrl_interface=/var/run/wpa_supplicant

network={
 ssid="SSID_OF_NETWORK"
 psk="PASSWORD_OF_NETWORK" 
 }
```
5. Replacing the SSID_OF_NETWORK with your wifi SSID and PASSWORD_OF_NETWORK with your password.

1. Save and safely eject the SD card.

# 3. Connect Single Board Computer (SBC)

## Tabs {.tabset}

### Duet 3 Mainboard 6HC
[![sbc_setup_02.jpg](/manual/configuration/sbc_setup_02.jpg =50%x){.align-right}](/manual/configuration/sbc_setup_02.jpg){target=_blank}[![sbc_setup_03.jpg](/manual/configuration/sbc_setup_03.jpg =50%x){.align-right}](/manual/configuration/sbc_setup_03.jpg){target=_blank}
1. Put the SD card in the SBC, connect the ribbon cable to the Duet 3, and connect a USB cable to the Duet 3 for power. Ensure there is NO SD card in the Duet itself.
1. Note the orientation of the red stripe on the ribbon cable indicating pin 1.
1. It is acceptable, especially with longer ribbon cables than shown here, to route the cable around the back of the SBC, however pin 1 must connect as shown.
1. Ensure the USB supply is sufficient to power the peripherals attached to the Pi, especially screens that do not have a separate power supply. [See here for more details on 5v power jumper position selection](https://docs.duet3d.com/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6HC_Hardware_Overview#h-5v). 
1. Using a Pi4 may require a separate power supply for both the Duet and Pi4 due to power draw. The same holds true if using peripherals that draw power via USB.

<p style="clear:both"></p>

### Duet 3 Mainboard 6XD
[![duet_3_mb6xd_getting_connected_sbc.jpg](/duet_boards/duet_3_mb6xd/duet_3_mb6xd_getting_connected_sbc.jpg =50%x){.align-right}](/duet_boards/duet_3_mb6xd/duet_3_mb6xd_getting_connected_sbc.jpg){target=_blank}
1. Put the SD card in the SBC, connect the ribbon cable to the Duet 3, and connect a USB cable to the Duet 3 for power. Ensure there is NO SD card in the Duet itself.
1. Note the orientation of the red stripe on the ribbon cable indicating pin 1.
1. Ensure the USB supply is sufficient to power the peripherals attached to the Pi, especially screens that do not have a separate power supply. [See here for more details on 5v power jumper position selection](https://docs.duet3d.com/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6XD_Hardware_Overview#h-5v). 
1. Using a Pi4 may require a separate power supply for both the Duet and Pi4 due to power draw. The same holds true if using peripherals that draw power via USB.

<p style="clear:both"></p>

### Duet 3 Mini 5+

[![sbc_setup_09.jpg](/manual/configuration/sbc_setup_09.jpg =50%x){.align-right}](/manual/configuration/sbc_setup_09.jpg){target=_blank}
1. Put the SD card in the SBC, connect the ribbon cable to the Duet 3. Note the orientation of the red stripe on the ribbon cable indicating pin 1.
1. The Duet 3 Mini+ cannot power the Pi. You must power the Pi separately. The Raspberry Pi is sensitive to the input voltage, and many smartphone chargers or other USB power supplies cannot supply sufficient voltage. Therefore, we strongly recommend that you use the official Raspberry Pi PSU, or another PSU specifically designed to power a Raspberry Pi. If the red LED on the Pi is not continuously illuminated, the power supply is insufficient.
1. Connect a USB cable to the Duet 3 for power. Ensure there is NO SD card in the Duet itself.

<p style="clear:both"></p>

# 4. First Boot

1. Turn on power to Duet and SBC.

1. If you have a screen attached when power is applied you will see the SBC boot up. Once bootup is complete the Chromiun browser will launch DuetWebControl in full screen. press F11 if you want to exit fullscreen.

1. If you are connecting over the network then go to [`http://duet3.local/`](http://duet3.local/) *note the SBC takes about 1 minute to boot for the first time and about 30 seconds subsequently.*

If [`http://duet3.local/`](http://duet3.local/) does not show Duet Web Control (DWC) then follow these trouble shooting steps:

## Troubleshooting

*Note that these steps assume the hostname of your SBC is "duet3" if you have changed it then the mDNS resolution will be the new hostname, not "duet3".  Also note that you cannot use the gcode command M550 to set your printer hostname.*

### Duet 3 and SBC not communicating

* If the Duet is not showing up on the Pi when doing a lsusb it may be a power supply issue. Try powering the Duet and SBC separately and see if the problem is resolved.
* Use of Pi cases that extend the GPIO pins or use the pins for external buttons or powering PWM fans will not work with the Duet.

### Duet 3/SBC not on the network (http://duet3.local does not work)

![sbc_setup_04.png](/manual/configuration/sbc_setup_04.png =50%x){.align-right}If you do not have a screen/keyboard attached to your SBC and your browser cannot connect over wifi all, you will get a message in your browser similar to the one on the right.
* Log into your router and confirm that the SBC has received an IP address. If it is not shown at all move to the next section: wifi not working at all.
* If an IP address is shown for the SBC the enter that address in the browser e.g. http://192.168.1.123 If this works then its possible that your network or PC does not like the mDNS service used to provide the http://nnnnnn.local names. The best way around this is to reserve the IP address in your router so the SBC always receives the same address.

### WiFi not working at all

* You may have mis-configured the wpa_supplicant.conf. If possible plug the SBC into your router using an Ethernet cable temporarily to reconfigure Wifi. If that is not possible then try adding the wpa_supplicant.conf file to the "boot" partition of the SD card again.
* If neither work then try temporarily connecting a screen and keyboard to the SBC to see whats going on.
* If you are using a screen and keyboard temporarily, then follow [this guide to get wifi  running](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md).
* If you are using Ethernet temporarily then follow Step 5 - Accessing the SBC through SSH/VNC, below to get command line/VNC access to your SBC, then you can [follow this guide to get wifi running](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md).

### DWC does not appear correctly (500 error)

![sbc_setup_05.png](/manual/configuration/sbc_setup_05.png =50%x){.align-right}If you get a "HTTP ERROR 500 - This page is not working" response either when going to [`http://duet3.local/`](http://duet3.local/) or to the IP address, then the SBC is on the network, but Duet Software Framework (the Duet3 software that runs on the SBC) is not operating correctly.

* The most likely cause of this is that it cannot connect to the Duet 3 Mainboard. 
* Check the ribbon cable is installed correctly.
* Check that the mainboard has firmware installed (follow the "Updating Duet 3 mainboard firmware via USB" part of [this documentation](/User_manual/Overview/Getting_started_Duet_3_MB6HC)).
* If none of these steps work see the [Troubleshooting - SBC communications](https://github.com/Duet3D/DuetSoftwareFramework/wiki/SBC-Setup-Guide#troubleshooting) documentation.

### Gcode commands not being accepted

Ensure there is no SD card in the Duet itself. In order to run in SBC connected mode, the Duet SD card slot must be empty. If an SD card is present in the Duet, it will attempt to start in standalone mode.

# 5. Accessing the SBC through SSH/VNC

*Note this is optional if you have a screen and keyboard attached to the SBC*

In the future it is the intention that all the required interaction between a user and the Duet 3, including the SBC, can be done through Duet Web Control. At this point in time it is necessary to have either a ssh command line, or VNC connection, in order to update the Duet Software Framework software that runs on the SBC.

# Tabs {.tabset}

## Connecting via SSH

DuetPi lite (no GUI) has SSH enabled by default and wpa_supplicant.conf is present on the boot partition, because usually you don't have a display attached to those setups.

DuetPi (with GUI) does not have SSH enabled by default, but you can enable it by putting an empty "ssh" file on the boot partition.

Under **Windows** the easiest way is via “Putty”:

* Download & Install [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).
* Enter duet3.local or your SBC IP address and select ssh. 
![sbc_setup_07.png](/manual/configuration/sbc_setup_07.png)

* Agree to the security alert - its Putty Saying it has never connected to this SSH server before.
* Login with username “pi” and password “raspberry”

Under **macOS and Linux** the "ssh" program is accessible from the command line. [macOS guide here.](http://osxdaily.com/2017/04/28/howto-ssh-client-mac/)

* open a Terminal window and send the following ssh command:
  `ssh pi@duet3.local`
* You will then be presented with a password prompt; use 'raspberry'.

**For all systems**, once connected, you will be presented with a command line:
`pi@duet3:~ $`

To update the Duet Software Framework and other system components type:
`sudo apt update`
(the sudo password is raspberry)

Then
`sudo apt upgrade`

## Connecting via VNC

If you prefer a desktop environment to a command line then you can use the realVNC client to connect.

* First follow the steps above in "Connecting via SSH" until you are at a command line on the pi.
* type 
  `sudo raspi-config`

* Navigate to Interfacing Options.
  * Scroll down and select VNC > Yes.
  * Choose advanced options -> resolution and select the resolution you would like to use (you may find better performance using a medium resolution, not the highest)
  * Save and reboot when prompted

* Install the [realVNC client](https://www.realvnc.com/en/connect/download/viewer/) on the machine you want to use to connect. You can use the free version non commercially.

* Once installed open the realVNC client, go to "file" -> "new connection" and use duet3.local or the IP address of your SBC to connect. the username and password are the same as for ssh.

# 6. Update firmware

Once your Raspberry Pi has established an internet connection, it is recommended to install the latest updates. To do so, connect via SSH or VNC (see above) to your Raspberry Pi or open a terminal (if you have keyboard and monitor connected) and run:

```
sudo apt update
sudo apt dist-upgrade
```

This will install the latest software components and the latest RepRapFirmware version on your Duet 3. You can do this regularly to update the system as new firmware is released.

To switch between stable/release package feed and unstable/beta package feed, see [DSF installation](https://docs.duet3d.com/User_manual/Machine_configuration/DSF_RPi#software-installation).

# 7. Configuring system files

By this step you should have Duet Web Control showing when you go to [`http://duet3.local/`](http://duet3.local/) or the IP address of your SBC.

![sbc_setup_06.png](/manual/configuration/sbc_setup_06.png =800x)

The default SD card image does not come with the system files configured for your printer - there are many different types of machines that are supported  so **you need to generate a config.g, homing files, and other system macro files that are required**.

The online [RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/) has been updated to support the generation of config files for RRF3. Though you may still need to manually implement some of the more advanced features. 

* If you have a set of files for your printer for Duet 2 use those as the start point for the values used in the config tool.
* If you do not have a set of Duet 2 config files then use the config tool to generate a set with everything as closely matched to your printer as possible, and then carefully commision the printer to verify proper operation.

The changes required to create a set of Duet 3 config files is outside the scope of this guide. Please see the [Migrating from RRF2 to RRF3](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3) guide for a list of the gcodes that need changing to get a config.g setup for duet 3.

Once you have a set of system files they can be uploaded via the "system" tab in DWC.

# 8. Changing the SBC hostname

This is an optional step if you only have a single duet3 on your network. It is required if you have more than one duet 3 as each duet 3 on a network needs a unique host name.

The name of the printer is its hostname on the network, you will need to connect to the SBC over SSH or VNC (as described above) in order to run the Raspberry Pi configuration utility and change the hostname. In future there will be an easier way to set the hostname within DuetWebControl. *Note that you cannot currently use the gcode command M550 to set your printer hostname*

* Connect via ssh or VNC.
* At a command prompt type

`sudo raspi-config`

* Select “System Options” -> Hostname-> “OK”-> and set the new printername/hostname.
* Note the hostname must confirm to [certain limitations to](http://man7.org/linux/man-pages/man7/hostname.7.html) be valid.
  * Valid characters for hostnames are letters from a to z,
  * the digits from 0 to 9, 
  * the hyphen (-).
* Select “Finish” and reboot.

When you next boot with a screen, or log in with VNC, Chromium may give you the following warning. Select “unlock Profile and Relaunch”.

![sbc_setup_08.png](/manual/configuration/sbc_setup_08.png)