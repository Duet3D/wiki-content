---
title: Duet Software Framework (DSF) on Raspberry Pi
description: Duet Software Framework is the bundle of software programs running on the Raspberry Pi that connects to and controls the Duet 3.
published: true
date: 2022-01-25T16:37:22.948Z
tags: 
editor: markdown
dateCreated: 2022-01-25T12:15:12.719Z
---

# Introduction

Duet Software Framework (DSF) is the bundle of software programs running on the Raspberry Pi that connects to and controls the Duet 3.

Duet 3 mainboards are supplied with an SD card loaded with the Raspian OS suitable for Raspberry Pi 3B+ or 4. For a standard setup, the [SBC Setup for Duet 3](/User_manual/Machine_configuration/SBC_setup) page gives an outline how to get setup initially, and what to do if there are issues.

This page covers installing DSF on a stock Raspbian image.

# DuetPi

DuetPi is the official Debian image based on Raspbian that is customised for the Duet 3. If you are not very familiar with the Raspberry Pi, it is strongly recommended to use it instead of the stock Raspbian image. See [SBC Setup for Duet 3](/User_manual/Machine_configuration/SBC_setup).

# Raspbian Setup Guide

If you have the latest Raspbian image installed on your Raspberry Pi, follow these instructions to get started with DSF. These steps are obsolete if you are using DuetPi.

See also [DSF github wiki here](https://github.com/Duet3D/DuetSoftwareFramework/wiki/SBC-Setup-Guide).

## Enabling SPI

To provide a controller base for the Duet, the first SPI port on the Linux controller board is used. If Raspbian operating system is used, the SPI driver can be enabled by doing the following steps:

1. Run

`sudo raspi-config`

(Alternatively `sudo raspi-config nonint do_spi 0` will enable SPI with a single command)

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

## Software Installation

*Note that the following steps are not required if you are already running DuetPi*

### Installing from stable feed

To obtain the required software packages on a Debian-based distribution running an ARMv7 processor (RaspPi 3 or newer) execute the following commands:

```
wget -q https://pkg.duet3d.com/duet3d.gpg
wget -q https://pkg.duet3d.com/duet3d.list
sudo mv duet3d.gpg /etc/apt/trusted.gpg.d/
sudo mv duet3d.list /etc/apt/sources.list.d/duet3d.list
sudo chown root:root /etc/apt/trusted.gpg.d/duet3d.gpg
sudo chown root:root /etc/apt/sources.list.d/duet3d.list
```

Once this is done, install HTTPS support for APT, update the package feeds, and install the DSF bundle:

```
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install duetsoftwareframework
```

### Installing from unstable/beta feed 

If you wish to use the latest unstable/beta DSF components, you can run the following commands instead:

```
wget -q https://pkg.duet3d.com/duet3d.gpg
wget -q https://pkg.duet3d.com/duet3d-unstable.list
sudo mv duet3d.gpg /etc/apt/trusted.gpg.d/
sudo mv duet3d-unstable.list /etc/apt/sources.list.d/duet3d-unstable.list
sudo chown root:root /etc/apt/trusted.gpg.d/duet3d.gpg
sudo chown root:root /etc/apt/sources.list.d/duet3d-unstable.list
```

Once this is done, install HTTPS support for APT, update the package feeds, and install the DSF bundle:

```
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install duetsoftwareframework
```

Please be aware that the unstable repository is meant for beta testers and that it can contain experimental code. For production use, the stable repository is strongly recommended.

### Changing from stable to unstable feed

1. Remove the stable package feed, make sure to remove it before you run `apt-get`:
   ```
   sudo rm /etc/apt/sources.list.d/duet3d.list
   ```
1. Then install the unstable package feed:
   ```
   wget -q https://pkg.duet3d.com/duet3d-unstable.list
   sudo mv duet3d-unstable.list /etc/apt/sources.list.d/duet3d-unstable.list
   sudo chown root:root /etc/apt/sources.list.d/duet3d-unstable.list
   ```
1. And update the installation:
   ```
   sudo apt-get update
   sudo apt-get install duetsoftwareframework
   ```

### Changing from unstable to stable feed

To downgrade from an installed unstable version to a stable release requires a few extra steps, to remove files newer than the release version. Follow the following steps:

1. Change back unstable to stable in /etc/apt/sources.list.d/duet3d.list:
   ```
   sudo bash -c "echo 'deb https://pkg.duet3d.com/ stable armv7' > /etc/apt/sources.list.d/duet3d.list"
   ```

2. Refresh the package lists:
   ```
   sudo apt update
   ```

3. Remove potentially left-over RRF packages
   ```
   rm -f ./reprapfirmware*.deb
   ```

4. Download the latest stable RepRapFirmware package
   ```
   apt download reprapfirmware/stable
   ```

5. Downgrade RepRapFirmware
   ```
   sudo dpkg -i --force-depends ./reprapfirmware*.deb
   ```

6. Downgrade DSF:
   ```
   sudo apt install -y --allow-downgrades duetsoftwareframework/stable duetcontrolserver/stable duetwebserver/stable duetpluginservice/stable duettools/stable duetruntime/stable duetwebcontrol/stable
   ```

## Starting services

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
