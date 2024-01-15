---
title: Updating SBC from Buster to Bookworm
description: Guide to update a Single Board Computer (SBC) running Duet Software Framework from Buster to Bookworm
published: true
date: 2024-01-15T16:11:48.467Z
tags: 
editor: markdown
dateCreated: 2024-01-15T16:11:48.467Z
---

# Introduction

Bookworm is the latest version of the Raspberry Pi Operating System, based on Debian. For an overview, see the [Raspberry Pi ne](https://www.raspberrypi.com/news/bookworm-the-new-version-of-raspberry-pi-os/)


# Updating to Bookworm

To update a Buster-based SBC to Bookworm, follow these steps:

1. Enable SFTP via `M568 P1 T1 S1`
1. Load FileZilla and connect via sftp (pi/raspberry)
1. Copy everything from `/opt/dsf/sd` to local PC except for firmware directory
1. Flash new image, possibly with customisations
1. Enable SFTP again (see above)
1. Restore contents from local SD folder
1. Power-cycle everything (reboot Duet+Pi)
1. Optionally turn off SFTP again