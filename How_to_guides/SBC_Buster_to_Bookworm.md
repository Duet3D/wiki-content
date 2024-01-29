---
title: Updating SBC from Buster to Bookworm
description: Guide to update a Single Board Computer (SBC) running Duet Software Framework from Buster to Bookworm
published: true
date: 2024-01-29T12:07:49.089Z
tags: 
editor: markdown
dateCreated: 2024-01-15T16:11:48.467Z
---

# Introduction

As at January 2024, Bookworm is the latest version of the Raspberry Pi Operating System, based on Debian. For an overview, see the [Raspberry Pi news article](https://www.raspberrypi.com/news/bookworm-the-new-version-of-raspberry-pi-os/) on it.

We recommend flashing an SD card with our new Bookworm based version of DuetPi, rather than trying to upgrade from Buster to Bookworm. The latter upgrade path is NOT supported in any way and typically leads to unexpected problems.

# Updating to Bookworm

To update a Buster-based SBC to Bookworm, follow these steps:

1. Enable SFTP via `M586 P1 T1 S1`
2. Load FileZilla and connect via SFTP. The default credentials for SSH/SFTP are user `pi` and password `raspberry`
3. Copy everything but `firmware` and `www` from `/opt/dsf/sd` to local PC except for firmware directory

![buster_bookworm_filezilla.png](/manual/configuration/buster_bookworm_filezilla.png)


4. Flash new image, possibly with customisations
  Use the steps outlined here: [Flash the Image File](https://docs.duet3d.com/en/User_manual/Machine_configuration/SBC_setup#h-1-flash-the-image-file) to flash a few SD card. If possible it is recommended to use a different card so you have the old card to fall back to if you have and issues.

5. To restore the config files, you have two options. If you need to restore your `gcodes` directory as well, the following first option is recommended. 

## Restore backup via SFTP

1. Insert the microSD card in the Raspberry Pi
2. Start your machine, connect to it in DWC and then enable SFTP again via `M586 P1 T1 S1` (see above)
3. Connect via FileZilla, potentially using your new credentials. You may see a warning message when connecting for the first time again due to the different SSH/SFTP certificate. Accept it and continue
2. Restore contents from local SD folder via FileZilla to `/opt/dsf/sd`
3. Power-cycle everything (reboot Duet+Pi)
4. Optionally turn off SFTP again via `M586 P1 T1 S0`

## Restore backup files without SFTP

1. When the image has been flashed, eject the microSD card and insert it in your PC again. When done, enter the first partition which holds `config.txt` etc.
2. Copy your backed up directories (`sys` etc.) to the root directory of that partition
3. Eject the microSD card again
4. Insert it in the Pi
5. Boot the image on the Pi, connect to it in DWC and then Enable SFTP again (see above)
6. Restore contents from local SD folder
7. Power-cycle everything (reboot Duet and Pi)
7. Optionally turn off SFTP again
