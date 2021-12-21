---
title: USB ground loops
description: When you connect your Duet to a PC using a USB cable, usually this creates a ground loop. This is not unique to the Duet, it is true for almost all 3D printer electronics. This ground loop can be problematic unless precautions are taken.
published: true
date: 2021-11-30T12:02:59.935Z
tags: 
editor: markdown
dateCreated: 2021-11-30T12:02:57.289Z
---

# Introduction

When you connect your Duet to a PC using a USB cable, usually this creates a ground loop. This is not unique to the Duet, it is true for almost all 3D printer electronics. This ground loop can be problematic unless precautions are taken.

# Why is there a ground loop?

* When a PC is plugged into the mains, there is a connection between the ground pin of the mains plug and the ground pin on the USB connector. When you use a laptop with the mains charger connected, this is also usually the case.
* When you power your Duet from a mains-powered 12V or 24V power supply, again there is usually a connection between the ground pin of the mains plug and the negative output of the PSU. This is always the case for ATX power supplies and may be the case for other supplies - or you may have added this connection yourself for safety reasons.
* The negative VIN input to the Duet is connected to the ground pin on the USB connector
* When you connect a USB cable, the cable connects the ground pin of the USB connector on the PCB to the ground pin of the USB connector on the Duet, which completes the loop.

# Problems that a ground loop can cause

* The USB cable and mains ground connections form an alternative path for current to flow between the Duet and the negative power supply output. If the connection between the negative terminal of the Duet's VIN terminal block and the negative output from the power supply is weak, then this alternative path will be used. The USB cable will become hot and you risk damaging the USB ports on the PC and the Duet.
* If the ground connections of the mains supplies of the Duet and the PC are at different potentials, for example because of a mains fault or because other equipment connected to the mains supply has significant ground current, a large current will flow through the USB cable ground wire.
* Other devices powered from the mains may create ground transients. These will flow through the USB cable and may cause the Duet to reset or perform abnormally in other ways.

# Mitigating the problems

* Don't connect the printer to a PC via USB when you don't need to. The Duets all have Ethernet or WiFi connections, so USB is generally used only when debugging.
* If you do need to use a USB connection, power the PC and the Duet's power supply **and nothing else** from either a double mains socket or from a separate mains distribution block.
* Make **very sure** that the cable between the power supply and the negative VIN terminal on the Duet is rated adequately for the current and secure at both ends. Check that the VIN terminal block screws on the Duet remain tight.
* Pass the USB cable a few times through a ferrite ring, or use a USB cable that has a ferrite ring fitted already, or buy a split ferrite bead and clamp it over the lead. This mitigates the effect of ground transients.

# Avoiding USB ground loops

Here are four ways of avoiding a USB ground loop:

1. Don't use a USB connection. In normal use, you don't need one because you can perform all printer operations (including uploading GCode files to print) via the web interface.
1. Connect the Duet via USB to a laptop running off batteries, with the charger not connected.
1. Use a USB isolator module such as this one from [adafruit](https://www.adafruit.com/product/2107) or this one from [Farnell UK](https://uk.farnell.com/olimex/usb-iso/usb-isolator-1000vdc-for-pc-laptop/dp/1795095).
1. Disconnect the power supply negative output from mains ground. **For safety reasons, this is not recommended**.