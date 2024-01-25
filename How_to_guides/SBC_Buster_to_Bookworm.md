---
title: Updating SBC from Buster to Bookworm
description: Guide to update a Single Board Computer (SBC) running Duet Software Framework from Buster to Bookworm
published: true
date: 2024-01-25T17:23:50.338Z
tags: 
editor: markdown
dateCreated: 2024-01-15T16:11:48.467Z
---

# Introduction

As at January 2024, Bookworm is the latest version of the Raspberry Pi Operating System, based on Debian. For an overview, see the [Raspberry Pi news article](https://www.raspberrypi.com/news/bookworm-the-new-version-of-raspberry-pi-os/) on it.

We recommend flashing an SD card with our new Bookworm based version of DuetPi, rather than trying to upgrade from Buster to Bookworm.


# Updating to Bookworm

To update a Buster-based SBC to Bookworm, follow these steps:

1. Enable SFTP via `M568 P1 T1 S1`
2. Load FileZilla and connect via sftp (pi/raspberry)
3. Copy everything from `/opt/dsf/sd` to local PC except for firmware directory

![buster_bookworm_filezilla.png](/manual/configuration/buster_bookworm_filezilla.png)


4. Flash new image, possibly with customisations
  Use the steps outlined here: [Flash the Image File](https://docs.duet3d.com/en/User_manual/Machine_configuration/SBC_setup#h-1-flash-the-image-file) to flash a few SD card. If possible its recommednded to use a different card so you have the old card to fall back to if you have and issues.
5. Boot the image on the Pi, connect to it in DWC and then Enable SFTP again (see above)
6. Restore contents from local SD folder
7. Power-cycle everything (reboot Duet+Pi)
7. Optionally turn off SFTP again