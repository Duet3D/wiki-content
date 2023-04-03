---
title: Duet3D Documentation
description: Introduction to the Duet3D range of control electronics
published: true
date: 2023-01-24T12:34:10.610Z
tags: 
editor: markdown
dateCreated: 2021-04-20T11:08:57.551Z
---

# Introduction

Welcome to the Documentation for Duet3D control electronics hardware, firmware and software. 

If you are not familiar with Duet3D products then see the brief summary below with links to the start points in the documentation.

# Overview of Duet3D hardware

Duet3D control [hardware](/Duet3D_hardware) is split into the [Duet 3 Family](/Duet3D_hardware/Duet_3_family), [Duet 2 Family](/Duet3D_hardware/Duet_2_family) and various [accessories](/Duet3D_hardware/Accessories). Duet 2 hardware is well established and used in a wide range of machines. Duet 3 hardware are newer, more extendable and have a greater choice of stepper drivers. Duet 3 also allows for connecting a single board computer for additional networking, user interface and third party program options (See the description of Duet Software Framework below). All Duet mainboards are designed to be controlled over a network, either directly in standalone mode or via Duet Software Framework. In general accessories work with both Duet 2 and Duet 3 hardware.

# Overview of Duet3D firmware and software

Duet3D control hardware runs advanced firmware called [RepRapFirmware](/User_manual/RepRapFirmware). This runs directly on the microprocessors on Duet 2 and Duet 3 mainboards, along with Duet 3 expansion boards. RepRapFirmware is extremely configurable and flexible, supporting a wide range of machine kinematics, large numbers of axes, tools and ancillaries. RepRapFirmware is actively under development and so is constantly improving with new features added each release. See the [RepRapFirmware github project](https://github.com/Duet3D/RepRapFirmware/wiki/Changelog-RRF-3.x) for all the recent changes.

<!-- TODO add a Duet Software Framework Overview -->
When using Duet 3 mainboards it is possible to connect a Single Board Computer (SBC) such as a Raspberry Pi directly to the Duet over a high speed SPI bus. With DuetSoftwareFramework running on the SBC, you can use a directly connected display to show the User interface, make use of the SBC's networking capabilities and make use of a powerful API to interface third-party applications and plugins as tightly as needed to the machine control flow.

All Duet3D mainboards support networking and [Duet Web Control](/User_manual/Reference/Duet_Web_Control_Manual) provides a fully featured web browser based user interface that runs in all modern browsers. It is responsive and looks great on a phone, tablet, laptop, monitor or TV. DWC supports plugins that provide further extentability and customisation of the UI.


# How to use this documentation
<!-- TODO broaden this section a bit. -->
The documentation is split into hardware reference pages, a Software manual and some supporting guides and other pages.

The hardware reference pages have a detailed pinout to aid in wiring, along with pin descriptions and limitations. Please familiarise yourself with the Duet3D hardware you are using, especially poarities and voltage limits before connecting power and other elements of your machine. 

The software user manual contains information about how to configure RepRapFirmware, DuetWebControl and DuetSoftwareFramework. the firmware is extremely configurable so we suggest starting with the [Getting Started](https://docs.duet3d.com/en/User_manual/Overview) page and using the [Config Tool](https://configtool.reprapfirmware.org/Start) Config Tool to get a baseline configuration.

# How to get support

We provide support through our forum, please sign up and post any questions there: [https://forum.duet3d.com/](https://forum.duet3d.com/)

