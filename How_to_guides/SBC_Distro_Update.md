---
title: Updating SBC distribution (buster/bookworm to trixie)
description: Guide to update a Raspberry Pi Single Board Computer (SBC) running Duet Software Framework from Buster/Bookworm to Trixie
published: true
date: 2026-06-23T08:01:28.058Z
tags: 
editor: markdown
dateCreated: 2024-01-15T16:11:48.467Z
---

# Introduction

As at June 2026, Trixie (Debian 13) is the latest version of the Raspberry Pi Operating System. For an overview, see the [Raspberry Pi documentation](https://www.raspberrypi.com/documentation/) on operating system releases.

We recommend flashing an SD card with our new Trixie based version of DuetPi, rather than trying to upgrade an existing installation in place. The in-place upgrade path described further below works for both the 32-bit and 64-bit Bookworm images but can still lead to unexpected problems; flashing a fresh image is the cleanest route.

# Updating to Trixie by flashing a fresh image

To move an existing SBC to a freshly flashed Trixie image, follow these steps:

1. Enable SFTP via `M586 P1 T1 S1`
2. Load FileZilla and connect via SFTP. The default credentials for SSH/SFTP are user `pi` and password `raspberry`
3. Copy everything but `firmware` and `www` from `/opt/dsf/sd` to local PC

![buster_bookworm_filezilla.png](/manual/configuration/buster_bookworm_filezilla.png)


4. Flash new image, possibly with customisations
  Use the steps outlined here: [Flash the Image File](https://docs.duet3d.com/en/User_manual/Machine_configuration/SBC_setup#h-1-flash-the-image-file) to flash a new SD card. If possible it is recommended to use a different card so you have the old card to fall back to if you have any issues.


## Restoring the backup

To restore the config files, you have two options. If you need to restore your `gcodes` directory as well, the following first option, via SFTP, is recommended. 

Note in either case external plugins will need to be re-installed.

### Restore backup via SFTP

1. Insert the microSD card in the Raspberry Pi
2. Start your machine, connect to it in DWC and then enable SFTP again via `M586 P1 T1 S1` (see above)
3. Connect via FileZilla, potentially using your new credentials. You may see a warning message when connecting for the first time again due to the different SSH/SFTP certificate. Accept it and continue
4. Restore contents from local SD folder via FileZilla to `/opt/dsf/sd`
5. Power-cycle everything (reboot Duet+Pi)
6. Optionally turn off SFTP again via `M586 P1 T1 S0`

### Restore backup without SFTP

1. When the image has been flashed, eject the microSD card and insert it in your PC again. When done, enter the first partition which holds `config.txt` etc.
2. Copy your backed up directories (`sys` etc.) to the root directory of that partition
3. Eject the microSD card again
4. Insert it in the Pi
5. Boot the image on the Pi. All the files should be restored automatically. **Note that this works only on the first start of the new image!**

# Upgrading from Bookworm to Trixie in place

If you do not want to reflash, a Bookworm installation can be upgraded to Trixie in place by pointing apt at the Trixie repositories and running a full upgrade. This works for both the 64-bit (arm64, Debian based) and the 32-bit (armhf, Raspbian based) Bookworm images: an in-place upgrade keeps the current architecture, so a 32-bit install becomes 32-bit Trixie and a 64-bit install becomes 64-bit Trixie.

> This does **not** work for old Buster releases. Buster is three Debian releases behind Trixie (buster 10, bullseye 11, bookworm 12, trixie 13), and skipping releases in a single dist-upgrade is not supported. On Buster the only supported route to Trixie is flashing a fresh image as described above.
{.is-warning}

Make a backup first (see the SFTP steps above) and ideally keep the old SD card so you can fall back to it.

1. Connect to the Pi over SSH (user `pi`, default password `raspberry`).
2. Make sure the current system is fully up to date and reboot:
   ```
   sudo apt update
   sudo apt full-upgrade
   sudo reboot
   ```
3. Point the apt sources at Trixie. On both the 32-bit and 64-bit Bookworm images the distribution and Raspberry Pi sources are in `/etc/apt/sources.list` and `/etc/apt/sources.list.d/raspi.list` (the 32-bit image points at the Raspbian mirror, the 64-bit image at the Debian mirror; the codename to swap is the same either way). Replace every occurrence of `bookworm` with `trixie`:
   ```
   sudo sed -i 's/bookworm/trixie/g' /etc/apt/sources.list /etc/apt/sources.list.d/raspi.list
   ```
   The Duet3D repository in `/etc/apt/sources.list.d/duet3d.list` uses the `stable` suite rather than a Debian codename, so it needs no change.
4. Refresh the package lists and perform the distribution upgrade:
   ```
   sudo apt update
   sudo apt full-upgrade
   ```
   Answer any prompts about changed configuration files. Unless you have customised a file yourself, keeping the package maintainer's version is usually the safe choice.
5. Remove packages that are no longer needed and reboot:
   ```
   sudo apt autoremove --purge
   sudo reboot
   ```

After the reboot you can confirm the new release with `cat /etc/os-release`, which should report Trixie (Debian 13). External plugins may need to be re-installed.
