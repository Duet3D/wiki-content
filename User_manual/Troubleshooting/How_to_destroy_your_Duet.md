---
title: How to destroy your Duet 2
description: 
published: true
date: 2021-12-17T00:27:06.129Z
tags: 
editor: markdown
dateCreated: 2021-12-05T22:49:28.038Z
---

<!-- Migrated not updated! 

[summary_image|1034]-->

# Introduction

Needless to say, **doing anything described here will invalidate your warranty!** But if you have discovered a hobby that is even more [enthralling/frustrating]^1^ than 3D printing, and you really want to say goodbye to your 3D printer and all its component parts, read on.

We who designed the Duet 2 are quite fond of our creation. So we hope you will be understanding, and forgive us for making it harder to destroy your board than you might want.

^1^delete as applicable

# Methods that probably won't destroy your Duet

**We can't guarantee that these techniques will not destroy your Duet 2** because they rely to some extent on protection mechanisms within the ICs we use that are not guaranteed by the manufacturers. But you would have to be lucky for them to achieve the dead Duet that you are aiming for.

1. Create a short between a hot end thermistor connection and an extruder heater or fan connection. On many electronics - including RAMPS and Duet 0.6 - this will kill the processor. However, this type of short is surprisingly common in 3D printers, so you're out of luck because we've taken precautions against it. The most damage you are likely to do is to blow the VSSA fuse. Although that fuse can be tricky to replace, it's quite easy to bypass with an external fuse. So if you are trying to destroy your Duet, blowing the VSSA fuse doesn't really count. Oh, and to spoil your fun, in PCB version 1.02 and later we use a self-resetting fuse.
1. Create a short between your bed heater thermistor and your 12V or 24V bed heater. Same considerations as above. OTOH if you use an AC mains voltage bed heater, you may be on to a winner, especially if your mains is 230V or greater.
1. Feed 5V into the Z probe input. Even better, feed 12V or 24V into it. This is easy to do if you have a PNP inductive or capacitive Z probe that you run from 12V or 24V, because these sensors provide an output at the voltage you supply them with. So you just need to 'forget' to use a potential divider to reduce the output voltage. If you have an NPN inductive or capacitive probe instead, it's not quite so easy to produce excessive voltage at the output, but you can probably still do it by sabotaging the ground connection to the sensor. Unfortunately for you, we realised there were ways of feeding excessive voltage into the Z probe input, so it has protection (again, not guaranteed) against excessive input voltage.
1. Short the endstop supply voltage pin to ground. This is a particular favourite of Arduino/RAMPS users, because on those boards it is very easily achieved by plugging in the 3-pin endstop connector the wrong way round. Doing so when using NC endstop switches will short 5V to ground, which is almost guaranteed to fry the 5V regulator on the Arduino. But we thought of that. First, we use polarised Molex-style connectors - although you can subvert that quite easily by plugging un-polarised Dupont-style receptacles into them (you'll need to throw away the polarised mating connectors that we supply with Duets). Second, we changed the pinout, so that the common case of reversing the connector has no effect when a simple microswitch (the most common type of endstop switch) is used. You can get around that by pretending that the only endstop connectors you have are the ones left over from when you were using RAMPS, and you 'forgot' to re-pin them. But third, we used a voltage regulator chip that tolerates short-circuits. We even added a circuit to ensure that the heaters are turned off if you short the +3.3v supply (the supply provided on the endstop connectors) to ground.
1. Short the output of the internal 5V regulator to ground. So you've tried harder, because the 5V rail is only made available on the expansion connector and on the PanelDue connector. But the 5V regulator is also short-circuit protected, so you've not tried hard enough.
1. Adjust the current while the motors are running. This is a favorite way for users of RAMPS and similar boards to destroy plug-in drivers. But it won't work on the Duets, because the current it set in firmware and it is safe to change it while the motors are powered.

Score so far: 2/10. So far you've made only a feeble attempt to destroy your Duet.

# Methods that might work, or only impact some functions of your Duet

1. Reverse the VIN power connections. Preferably, use an old-fashioned unregulated power supply - basically just a transformer, bridge rectifier and smoothing capacitor - without any form of current limiting. That should guarantee that you at least fry all the stepper drivers. Don't be tempted to do this using a modern switching power supply, because these generally have foldback current limiting - the lower the output voltage, the more the current is limited - so the current may be too low to fry the drivers. Remember, there are 5 drivers connected across the VIN supply, which will share the current. You need to supply enough current to fry all of them together. Even if you do fry the stepper drivers, you probably won't fry much else, because the 5V regulator is protected by a diode. Stepper driver chips can be replaced, so although frying them all is a good start, it's not a way to completely write-off your Duet.
1. Disconnect stepper motors when VIN power is applied and the motors have been powered up. If you do this a very many times, you might possibly destroy the stepper motor driver chip, although the TMC2660 drivers are quite tough and I failed to destroy any drivers when I last tried this, even using 24V VIN and motor currents set to 2.4A. You could be a little more subtle by having a bad crimp connection in your stepper motor cable instead of blatantly disconnecting motors while they are under power. But you will need to do this on all 5 stepper driver outputs if you want to destroy all the drivers.
1. Apply more than the recommended 25V to your Duet 2. The TMC2660 stepper driver chips are rated at 30V when passing current. So you could try cranking up the voltage to 36V or so in order to fry all of them at once. But it turns out that the drivers have a higher rating (60V) when not passing current. The Duet's power monitor checks the VIN voltage every millisecond, and if it detects more than 29V it turns the drivers off. So to use this method, you need to start with a voltage that is within the permitted range (10V to 29V) so that the firmware turns the drivers on, then ramp it up to well over 30V in less than a millisecond so that the firmware doesn't have a chance to turn the drivers off before the damage is done. If you fail to achieve this, then as a consolation prize you may be able to make most of the electrolytic capacitors explode if you apply more voltage to VIN than their rated 35V.

Score so far: 5/10. You've maybe destroyed some or all of your stepper driver chips, but the microcontroller is probably still talking to you. Must try harder.

# Methods that are very likely to write-off your Duet

1. Short +3.3V to +12V or +24V. This is quite tricky to achieve if you want to make it look like an accident. However, if you are using a 3.3V Z probe such as the mini IR sensor, then you have a 3.3V power wire going to the hot end. So you could 'accidentally' mis-wire your hot end to swap a heater or fan wire with the 3.3V supply to the Z probe, and hey presto you have fed 12V or 24V into the 3.3V power rail. Several devices run from 3.3V including the SAM4 processor and the WiFi module. Even better, excessive voltage on the 3.3V rail will back-feed into the 5V rail too, so you will destroy even more chips, and with luck the backlight inverter on your TFT panel too if you have one. A board with so many component failures is uneconomic to repair. Only a very small number of Duet users have managed to achieve this, so you will be joining a select group.
1. If all else fails, use a blowtorch or a chainsaw.

Score: 10/10 if you managed to produce blue smoke, otherwise 8/10.