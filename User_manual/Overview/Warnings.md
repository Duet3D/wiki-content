---
title: Warnings
description: We want your Duet to give you years of service. Here are some important do's and don'ts to avoid damaging your Duet.
published: true
date: 2021-12-04T22:10:45.735Z
tags: 
editor: markdown
dateCreated: 2021-11-30T11:45:31.097Z
---

We want your Duet to give you years of service. Here are some important do's and don'ts to avoid damaging your Duet.

* **Never connect or disconnect anything to your Duet when there is power applied to it!** 
Exception: you may connect or disconnect the USB cable with power applied, but see the important notes on using a USB connection.
* **Take great care to connect the + and - leads from the power supply the right way round to the VIN terminal block!**
* **Do not exceed 25V (Duet 3 Mini 5+ and Duet 2) or 32V (Duet 3 MB6HC) VIN input voltage!**
* **When the stepper motors are connected to the Duet, do not move any parts by hand that make the motors rotate rapidly!** 
For example, if you need to move the print head by hand, or the carriages of a delta printer by hand, do it s-l-o-w-l-y. Likewise, if you need to rotate the extruder gear by hand, do it slowly. Rapid rotation of the stepper drivers will generate enough voltage to power them up in an uncontrolled fashion, and could generate enough to exceed their rated voltage.
* **Do not insert an SD extender cable into the built-in SD card socket!** 
These cables frequently damage the contacts inside the SD card socket. Even when they don't, they do not work well at the high SD card transfer speeds used by the Duet.
* **When mounting the Duet, if you use metal screws then make sure that they don't touch any of the electronic components or solder pads on the Duet!** 
Use the Nylon washers supplied with your Duet under the screw heads.
* **Be very careful if you use a multimeter to measure voltages on the board, especially on the fan connectors!** 
The probes may slip and short against each other or to other components. If you short the two pins of a fan connector together, you **will** damage your Duet.
* **Do not rely solely on the Duet electronics and firmware to guard against excessive temperatures in your 3D printer!** 
Despite the advanced protection features in Duet firmware, electronics and firmware can fail in unexpected ways. Read the [Fire Safety](/User_manual/Overview/Fire_safety) page.
* **If you use a USB connection to the Duet, take care to mitigate the effect of ground loops!** 
See [USB ground loops](/User_manual/Overview/USB_ground_loops).