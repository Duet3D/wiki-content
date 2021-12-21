---
title: Fire Safety
description: A 3D printer contains heater elements and flammable filament. It is essential that over-temperature situations be prevented. Duet firmware tries to prevent as many cases as possible, but hardware support and appropriate configuration can make it safer.
published: true
date: 2021-12-13T14:47:09.835Z
tags: 
editor: markdown
dateCreated: 2021-11-30T11:54:01.467Z
---

![fire_safety.jpg](/manual/troubleshooting/fire_safety.jpg =x300)

# Introduction

A 3D printer contains one or more heater elements and a large supply of flammable filament. It is therefore essential that over-temperature situations be prevented. The Duet firmware tries to prevent as many cases as possible, but hardware support and appropriate configuration can make it safer.

This page will talk about how to make your printer as safe as possible, but it must be emphasised that the safety of your printer is **your responsibility**. 3d printers do catch fire and people have had rooms destroyed. Worse, fire safety is an issue of how your printer handles faults. A printer can sit there in an unsafe condition for months or years, working fine, only to catch fire when (say) a thermistor falls out of the heater block.

**We do not recommend running a 3d printer unsupervised!**

# Passive safety

The safest approach is to have relatively weak heaters and poorly-insulated hot ends and build plates, so that even if the heater is running flat out the hot parts never reach a dangerous temperature. E3D, for example, provides relatively low-power heater cartridges for its hot ends for just this reason. Heated beds differ dramatically in their available heater power and in how much heat is carried away by the environment. There is also a temptation to use a very high-powered heated bed so that it heats up quickly.

The easiest way to check for passive safety is to run the Duet's PID tuning process. If your heated bed or hot end is capable of reaching a dangerously high temperature it will emit a warning. You can also see for yourself: if you have to restrict the PWM fraction substantially to prevent it exceeding a safe temperature, that's a sign that the heater is capable of becoming dangerously hot.

# Firmware detection of problems

The Duet firmware attempts to detect dangerous heating conditions and shut off the relevant heater. This detection is a somewhat complicated process, and it depends on various configuration settings. This means that on the one hand too-aggressive safety settings can shut down your printer mid-print, but on the other too-lax safety settings can make your printer a fire hazard. This is not a complete description of the protections, but here are some conditions that the Duet firmware detects as a fault:

* Thermistor reads as a dead short or completely open
* Heater is at commanded temperature then moves away from that temperature by more than 10 degrees for an extended time
* Heater is commanded to heat up to temperature but the heating is much slower than the PID model expects for an extended time

If a fault occurs on a heater, that heater will be shut down until the fault is cleared, either by restarting the Duet or with the M562 P*n* G-code. Do check the physical state of the printer before doing this.

# Hard shutdown of heaters

The Duet firmware normally controls the power to its heaters with MOSFETs. These power components are quite reliable but can fail, and one of the ways they can fail is as a short. If this occurs, the Duet firmware may command the heater to shut down, but the failed MOSFET means that the heater is instead supplied at 100%. There may be other failure modes (for example a short of 12/24V to ground) that simply shutting down the affected heater will not solve. If your power supply supports the PS_ON pin, then the firmware can use it to shut down the whole 12/24V circuit, leaving only the low-power 5V circuit. If you have an ATX power supply, then this is simply a matter of connecting the appropriate pin.

A similar problem can arise with heated beds that are controlled through a solid-state relay (SSR). The manufacturers of these parts state that they can fail as a short (see [this link](https://www.omron.com/ecb/products/pdf/precautions_ssr.pdf)), that is, supplying full power to the attached device regardless of the input signal, and they advise against building devices whose only off switch is an SSR in case this occurs. For a heated bed powered this way, it may be a good idea to add either an appropriate thermal cutoff to the bed (see below) or an electromechanical relay attached to PS_ON (so that the MCU can shut down the heater). Electromechanical relays can reliably fail open, and if they are used for switching power to the printer rather than temperature control they can last a very long time.

# Thermal cutoffs

One of the safest options is to attach a thermal cutoff device (fuse or reusable) to each heated object. This will shut off power to the heater regardless of what the firmware thinks is going on. The main way these can fail to work is if they physically fall off the thing whose temperature they're supposed to be monitoring - note that there will be no indication that this has occurred until an over-temperature event occurs and the cutoff device fails to operate.

# Smoke detectors

Optical smoke detectors are cheap and easily available. It may be desirable to arrange for one to shut down the power to the printer if smoke is detected, but be warned that by the time there is enough smoke to trigger a smoke detector the fire may be self-sustaining without any electrical power. Test this for yourself by burning a small piece of filament under safe conditions.

*photo credit [Pascal Volk](https://flic.kr/p/CEQNM9)*