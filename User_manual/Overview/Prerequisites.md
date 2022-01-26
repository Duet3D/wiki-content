---
title: Prerequisites for using the Duet
description: 
published: true
date: 2022-01-17T13:49:44.874Z
tags: 
editor: markdown
dateCreated: 2021-11-29T14:34:28.583Z
---

In order to set up and use a Duet mainboard and control it via the network, you will need the following equipment:

* **For WiFi Duets:** A network which includes a Wifi access point.
  * The Duet 2 Wifi operates at 2.4Ghz **not 5 Ghz**. Most Wifi networks have both these frequencies, sometimes as separate network names.
* **For Ethernet Duets:** A network which includes an Ethernet switch or hub with a free port. Alternatively a network cable can be used directly between a computer and the Duet 2 Ethernet. 
  * If a cable is used to link an Ethernet Duet mainboard directly to a computer then the computer network interface must support [Auto MDI-X](https://en.wikipedia.org/wiki/Medium-dependent_interface#Auto_MDI-X) or an Ethernet crossover cable is required.
* A computer connected to the network, with an available USB port, and a micro USB cable (supplied with the Duet).
* A computer, tablet or smartphone with WiFi client capability (this could be the same as the previous item, or a different device).
  * This computer could be connected to the network via Wifi or Ethernet. After the initial setup over USB then any device connected to the network that runs a compatible web browser can be used (e.g a smartphone or tablet) to control the Duet.
  * Chrome and Firefox work well with Duet Web Control. Microsoft Internet Explorer and older versions of Edge and Safari may have problems ([see here for details](/User_manual/Reference/Duet_Web_Control_Manual#browser-compatibility)).
* A CNC machine, e.g. 3D printer, laser cutter, or CNC mill/lathe, with accessible cabling, for the Duet to control.
