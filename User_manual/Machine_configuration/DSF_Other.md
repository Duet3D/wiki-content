---
title: Duet Software Framework (DSF) on other boards
description: Duet Software Framework is the bundle of software programs running on the Raspberry Pi that connects to and controls the Duet 3.
published: true
date: 2026-06-28T15:35:07.815Z
tags: 
editor: markdown
dateCreated: 2022-02-15T10:49:14.406Z
---

# Introduction

Duet Software Framework (DSF) is the bundle of software programs running on the Raspberry Pi that connects to and controls the Duet 3. This page covers installing DSF on a custom Debian board/installation (such as plain Raspberry Pi OS on a Raspberry Pi or Armbian on other boards).

# Installation Steps

Please follow these steps to prepare your board for a DSF link.

## Requirements

In order to use DSF, you must have a board that supports ARMv7 instructions. Make sure the board has a 32 or 64-bit Debian-based distribution installed before you continue.

> DSF has inofficial support for RPM and Arch-compatible packages but they are not actively maintained, which means you have to build them on your own if you wish to use them. See the [build instructions](https://github.com/Duet3D/DuetSoftwareFramework/wiki/Build-Instructions) on how this can be done. {.is-info}

## Enabling SPI

DSF uses an SPI link to communicate with a Duet. Before you can enable the communication between both devices, you must ensure an SPI channel can be opened.

### Setup on Raspberry Pi

To provide a controller base for the Duet, the first SPI port on the Linux controller board is used. If Raspbian operating system is used, the SPI driver can be enabled by doing the following steps:

1. Run

   ```
   sudo raspi-config
   ```
   
   (Alternatively `sudo raspi-config nonint do_spi 0` will enable SPI with a single command)

1. Navigate to **Interface options**

1. Go to **P4 SPI**

1. Confirm to enable the SPI interface

This will ensure the SPI driver is loaded and Duet Software Framework can use it for communication to the Duet board. To confirm that this step worked, run

`ls /dev/spidev*`

and verify that `/dev/spidev0.0` has been created.

### Setup on other boards

Please check the documentation of your board about how to enable SPI. This usually varies per vendor. When enabled, keep track of the SPI device node, it will be required later.

## Software Installation

*Note that the following steps are not required if you are already running DuetPi.*

> Apart from the following two package feeds, there are a few more version-dependent feeds available. See the [dev wiki](https://github.com/Duet3D/DuetSoftwareFramework/wiki/SBC-Setup-Guide#package-installation) for further information. {.is-info}

### Installation from stable feed

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

### Installation from unstable/beta feed

If you wish to use the latest unstable/beta DSF components, you can run the following commands instead:

```
wget -q https://pkg.duet3d.com/duet3d.gpg
wget -q https://pkg.duet3d.com/duet3d-unstable.list
sudo mv duet3d.gpg /etc/apt/trusted.gpg.d/
sudo mv duet3d-unstable.list /etc/apt/sources.list.d/duet3d.list
sudo chown root:root /etc/apt/trusted.gpg.d/duet3d.gpg
sudo chown root:root /etc/apt/sources.list.d/duet3d.list
```

Once this is done, install HTTPS support for APT, update the package feeds, and install the DSF bundle:

```
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install duetsoftwareframework
```

Please be aware that the unstable repository is meant for beta testers and that it can contain experimental code. For production use, the stable repository is strongly recommended.

### Installation/updating from .deb packages

If your SBC is not connected to the internet, you can download the .deb packages for DSF and install them offline.

* Download all the .deb packages for the version of DSF you want to install from [the DSF package repository](http://pkg.duet3d.com/dists/stable/armv7/binary-armhf/) (navigate the folder structure for unstable and/or x64 versions). 
* Put the .deb files on a USB drive, and plug it into your SBC. Make sure these are the only .deb files in the installation folder, so you don't install old versions or other software by mistake. 
* Install them with `dpkg -i *.deb` from the directory where the .deb files are located. 
* Alternatively copy them to the first partition of your microSD card and install them using `sudo dpkg -i /boot/*.deb`.
* If there are any missing dependencies, `dpkg` will tell you. 
* If updating DuetPi-lite you must mount the USB drive using the console first.

(See [this forum thread](https://forum.duet3d.com/topic/27928/) for more info.)

## Configuring SPI

DSF needs support for bigger transfers than the default maximum 4096 bytes. For this reason the buffer size of the SPI module has to be increased. This can be achieved by creating a new file `/etc/modprobe.d/spidev.conf` using the following command:

`echo "options spidev bufsiz=8192" | sudo tee /etc/modprobe.d/spidev.conf`

It is required to reboot the device once this step is complete.

If you are using a different board than the Raspberry Pi and if your SPI device node is not `/dev/spidev0.0`, you must change it next. To do so, open `/opt/dsf/conf/config.json` and edit the value of SpiDevice accordingly.

## GPIO Transfer Ready Pin

The link between Duet and SBC requires an extra GPIO pin so that the Duet can flag when it is ready to exchange data. This port defaults to [pin 25](https://pinout.xyz/pinout/pin22_gpio25#) on the Raspberry Pi 3 and 4. Furthermore, DSF uses `libgpiod` to access this pin.

*The following steps are not required if a Raspberry Pi is used.*

### Identifying the right pin

Most Linux distributions provide a toolkit for the *gpiochip* module in a package called `gpiod`.
You must have this package installed (containing the `gpioinfo` program) before you can continue.

In order to identify the GPIO device and pin number for DSF, run the `gpioinfo` command to get a list of available pins.
On a Raspberry Pi, the output will provide something like

```
gpiochip0 - 58 lines:
...
        line  23:     "GPIO23"       unused   input  active-high
        line  24:     "GPIO24"       unused   input  active-high
        line  25:     "GPIO25"       unused   input  active-high
        line  26:     "GPIO26"       unused   input  active-high
        line  27:     "GPIO27"       unused   input  active-high
...
```

In this default scenario, [GPIO25](https://pinout.xyz/pinout/pin22_gpio25#) corresponds to pin 22 of the expansion header on the Raspberry Pi. That means DSF can use pin 25 from `/dev/gpiochip0`.

Note that the output of `gpioinfo` may vary and for other SBCs it may not be as verbose as seen here. Refer to the manufacturer documentation of your SBC for further details. When you connect +3.3V to the pin of your choice, it may help to use the `gpioget` utility to find the actual chip device and pin number:

```
pi@Duet3:~ $ gpioget gpiochip0 25
1
```

where `1` corresponds to a high level (+3.3V) and `0` to a low level (GND).

### DSF Configuration

When you have identified the correct chip device and pin number, open `/opt/dsf/conf/config.json` and change the values of `GpioChipDevice` and `TransferReadyPin` accordingly.

It already defaults to pin 25 for the Raspberry Pi, so you can skip this step if you are using one.

## AppArmor Configuration

DSF 3.3 and later allow the installation of third-party plugins on the SBC. To prevent regular plugins from performing malicious actions, they are confined using AppArmor security profiles.

This means AppArmor support must be enabled before third-party plugins can be installed.
To achieve this on a Raspberry Pi, perform the following:

1. Install the `apparmor` package
1. Edit the kernel command line and append `lsm=apparmor` to it (for Raspberry Pi this is saved in `/boot/firmware/cmdline.txt`)
1. Reboot the machine
1. Check if the AppArmor module is enabled (run `aa-status` and check the output)

> Note it is also possible to disable the AppArmor requirement for Linux distributions that do not support it (like RedHat-based distros), but this is not encouraged and a potential security hazard. To do this, edit `/opt/dsf/conf/plugins.json` and set `DisableAppArmor` to `true`. {.is-warning}

# Services

This section describes the required services/components for DSF.

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

> Note: The webserver defaults to ports 80 (and 443 if TLS is enabled, see [M586](/User_manual/Reference/Gcodes#m586-configure-network-protocols)). You must change them in `/opt/dsf/conf/http.conf` if you wish to use different port numbers. {.is-info}

### Duet Web Control

For your convenience, Duet Web Server serves the user interface, Duet Web Control, from the `/www` folder of the virtual SD card directory (see below). This effectively mimics the old behaviour of previous Duet controllers. Once DWS has been started, open a browser of your choice and go to http://127.0.0.1. If you do not wish to connect manually every time you do this, you can specify the hostname of your Linux board instead of 127.0.0.1 as well (e.g. rasperrypi on Raspbian).

### Duet Plugin Services

If you wish to use the [DuetPiManagementPlugin](https://github.com/Duet3D/DuetSoftwareFramework/tree/master/src/DuetPiManagementPlugin) that provides support for several extra M-codes for system management, you must start the plugin services next. You can make them auto-start on boot by running: 

```
sudo systemctl enable duetpluginservice
sudo systemctl enable duetpluginservice-root
```

and to start them right away:

```
sudo systemctl start duetpluginservice
sudo systemctl start duetpluginservice-root
```

After these you should be able to manage and install third-party plugins using DWC.

# Further Steps

When your setup is complete, have a look at the [regular guide](/User_manual/Machine_configuration/DSF_RPi) for further information about upgrades and the virtual SD card.

# Troubleshooting

Please see the [SBC Setup Guide](https://docs.duet3d.com/en/User_manual/Machine_configuration/SBC_setup#h-9-troubleshooting) for further information.
