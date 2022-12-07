---
title: Duet Software Framework (DSF) on Raspberry Pi
description: Duet Software Framework is the bundle of software programs running on the Raspberry Pi that connects to and controls the Duet 3.
published: true
date: 2022-12-07T10:08:38.757Z
tags: 
editor: markdown
dateCreated: 2022-01-25T12:15:12.719Z
---

# Introduction

Duet Software Framework (DSF) is the bundle of software programs running on the Raspberry Pi that connects to and controls the Duet 3. This is refered to running in "SBC" mode, as opposed to running in "standalone" mode wihtout an SBC. Running in SBC mode is optional, it brings with it the potential to use the SBC to handle the networking and UI functions  of the system. In addition it provides an [API](https://github.com/Duet3D/DuetSoftwareFramework/wiki/API-Overview){target=_blank} on the SBC to build plugins and other applications that work with the Duet controlled system.

Duet 3 mainboards are supplied with an SD card loaded with the Raspberry Pi OS suitable for Raspberry Pi 3 or 4. For a standard setup, the [SBC Setup for Duet 3](/User_manual/Machine_configuration/SBC_setup){target=_blank} page gives an outline how to get setup initially, and what to do if there are issues.

# Prerequisites

Duet Software Framework utilises .NET 6 so an ARMv7 processor is required for operation. This means a Raspberry Pi 3 or newer is required. Support for the Raspberry Pi Zero 2W will be added as part of the upcoming 3.4 software release.

* DuetPi (Raspberry Pi 3 or newer)

  DuetPi is the official Debian image based on Raspberry Pi OS that is customised for the Duet 3. If you are not very familiar with the Raspberry Pi, it is strongly recommended to use this instead of the stock Raspbian image. See [SBC Setup for Duet 3](/User_manual/Machine_configuration/SBC_setup){target=_blank}.

* Stock Raspberry Pi OS and other SBCs

  If you have the latest Raspbian image installed on your Raspberry Pi or if you are using another SBC, follow [these](/User_manual/Machine_configuration/DSF_Other){target=_blank} instructions to get started with DSF.

# Updates

It is a good idea to keep your system up-to-date to eliminate potential bugs and security issues. The following steps describe how this can be achieved. 

## Installing Updates

Since DSF is provided as a standard Debian package feed, the update is quite trivial. To update it, run a standard system update on the attached SBC using a Linux console:

```
sudo apt update
sudo apt dist-upgrade
```

This will automatically install the latest DSF components. You may achieve the same remotely using [SSH](https://www.raspberrypi.com/documentation/computers/remote-access.html){target=_blank}.

## Switch to unstable packages

This assumes that the unstable packages are a later version than the currently installed stable packages.

1. Change stable to unstable in /etc/apt/sources.list.d/duet3d.list:
   ```
   sudo rm -f /etc/apt/sources.list.d/duet3d.list
   sudo bash -c "echo 'deb https://pkg.duet3d.com/ unstable armv7' > /etc/apt/sources.list.d/duet3d-unstable.list"
   ```
   
1. Update your system:
   ```
   sudo apt update
   sudo apt dist-upgrade
   ```

## Switch to stable packages

In a similar process to the above, this assumes that the stable packages are a later version than the currently installed unstable packages.

1. Change stable to unstable in /etc/apt/sources.list.d/duet3d.list:
   ```
   sudo rm -f /etc/apt/sources.list.d/duet3d-unstable.list
   sudo bash -c "echo 'deb https://pkg.duet3d.com/ stable armv7' > /etc/apt/sources.list.d/duet3d.list"
   ```
   
1. Update your system:
   ```
   sudo apt update
   sudo apt dist-upgrade
   ```

## Downgrade packages

> These steps are only applicable when downgrading beta/rc versions from the **unstable** package feed. They do not have an effect if you are already on the latest **stable** version. {.is-warning}

> Downgrading **stable** packages is **NOT** recommended. If you encounter problems after an upgrade, create a new thread on the [forum](https://forum.duet3d.com) instead. If you do need to downgrade anyway, skip the following step 1 and replace `/stable` with `=X.Y.Z` (`=.X.Y.Z-1` for RepRapFirmware) where `X.Y.Z` equals your desired *stable* package version (e.g. `3.4.4`). Be aware that successive `apt upgrade` calls will upgrade everything to the latest version again. {.is-info}

To downgrade from an installed version to a earlier release requires a few extra steps to remove files newer than the release version. This example demonstates downgrading from an unstable release to a stable release, the process is similar for downgrading to an unstable release.  Follow these steps:

1. Change back unstable to stable in /etc/apt/sources.list.d/duet3d.list:
   ```
   sudo rm -f /etc/apt/sources.list.d/duet3d-unstable.list
   sudo bash -c "echo 'deb https://pkg.duet3d.com/ stable armv7' > /etc/apt/sources.list.d/duet3d.list"
   ```

1. Refresh the package lists:
   ```
   sudo apt update
   ```

1. Remove potentially left-over RRF packages
   ```
   rm -f ./reprapfirmware*.deb
   ```

1. Download the latest stable RepRapFirmware package
   ```
   apt download reprapfirmware/stable
   ```

1. Downgrade RepRapFirmware
   ```
   sudo dpkg -i --force-depends ./reprapfirmware*.deb
   ```

1. Downgrade DSF
   ```
   sudo apt install -y --allow-downgrades duetsoftwareframework/stable duetcontrolserver/stable duetwebserver/stable duetpluginservice/stable duettools/stable duetruntime/stable duetwebcontrol/stable duetpimanagementplugin/stable
   ```

# Virtual SD card

The first SD card (0:/) is emulated by DCS and its default directory is `/opt/dsf/sd`. It may changed by editing the BaseDirectory value in `/opt/dsf/conf/config.json`.

# Third-Party Plugins

Duet3D provides some sample plugins for DSF which are available on [GitHub](https://github.com/Duet3D/DSF-Plugins/releases){target=_blank}.
Other third-party plugins are available on the [forum](https://forum.duet3d.com/category/41/plugins-for-dwc-and-dsf){target=_blank}.

If you want to get involved in plugin development, check out the [DSF](https://github.com/Duet3D/DuetSoftwareFramework/wiki/Third-Party-Plugins){target=_blank} and [DWC](https://github.com/Duet3D/DuetWebControl/wiki/Third-Party-Plugins){target=_blank} plugin guides.

# Reporting issues

Please use the forum to report any issues: [https://forum.duet3d.com/](https://forum.duet3d.com/){target=_blank}
