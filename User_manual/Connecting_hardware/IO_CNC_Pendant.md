---
title: CNC Pendant
description: 
published: true
date: 2021-11-15T12:26:23.297Z
tags: 
editor: markdown
dateCreated: 2021-11-15T12:24:39.042Z
---

# Introduction

This is a DIY project to interface a popular style of CNC pendant to the PanelDue port of a Duet 3 or Duet 2 WiFi/Ethernet/Maestro.

# Parts needed

![cnc_pendant_01.jpg](/manual/sensors/cnc_pendant_01.jpg =400x)

* One wired CNC pendant similar to the image above. These are available in 4-axis versions (illustrated) and 6 axis versions (with the axis selector switch having additional position 5 and 6). Either will work.
* One Arduino Micro or Arduino Pro Micro
* One 10K wire-ended resistor
* One 6.8K wire-ended resistor
* 4-core unshielded cable, as long as you want. You could instead re-use the 18- or 20-core cable that comes with the pendant, but the 4-core cable is thinner. If you do not need the PanelDue pass through feature, then 3-core cable is sufficient.
* 4- or 5- way Molex KK connector shell and crimp pins to plug into the PanelDue port of the Duet (you should have received some of these with your Duet)
* Small cable ties

# Tools needed

* Soldering iron + solder
* Wire cutters and strippers
* No. 2 Phillips screwdriver
* Crimp tool suitable for Molex KK connectors (e.g. Engineer PA21)
* Micro USB cable (for programming)
* Wooden dowel and heat gun (if you want to curl the 4-core cable)

# Programming the Arduino Pro Micro

Install Arduino 1.8.1 on your PC and create a new sketch called CNC-pendant. Then download all the files from the /src folder of [https://github.com/Duet3D/CNC-Pendant-Firmware](https://github.com/Duet3D/CNC-Pendant-Firmware) and put them in your sketch folder, overwriting the file CNC-pendant.ino file already there. Select board type Arduino Micro, connect the Arduino to your PC via USB, and program the sketch in it.

# Cable

If you are replacing the 18-or 20-core cable supplied with the pendant by 3- or 4-core cable, cut the 3- or 4-core cable to length and curl it if desired using the heat gun and a wooden dowel. Remove the back of the pendant and cut the 18- or 20-core cable close to the body of the pendant, making sure that you leave enough length for the individual wires to reach the Arduino, which will be installed in the top of the pendant. Remove the cut cable from the cable gland and feed the 4-core cable though it instead. Use tape or heatshrink on the outside of the 4-core cable to make it big enough for the cable gland to grip.

# Wiring

![cnc_pendant_02.jpg](/manual/sensors/cnc_pendant_02.jpg =600x)

The cut ends of the original 18- or 20 core cable, and the cores of the 4-core cable,  must be connected to the Arduino Pro Micro as listed at [https://github.com/Duet3D/CNC-Pendant-Firmware/blob/master/README.md](https://github.com/Duet3D/CNC-Pendant-Firmware/blob/master/README.md). As there are several wires that need to be connected to ground, it's easiest if you connect them together at the ground terminal of the rotary encoder, then run a single ground wire from there to the Arduino. That way you don't need to connect more than one wire to each pad of the Arduino, except that you need to connect both +5V from the 4-core cable and +5V for the encoder to the VCC terminal.

*TO DO: add schematic*

![cnc_pendant_03.jpg](/manual/sensors/cnc_pendant_03.jpg =600x)

If you do not intend to use a PanelDue at the same time as the pendant, it's best to leave the green wire of the 4-core cable (the one that would be connected to DOUT on the PanelDue) not connected to the Arduino RX0 pin, otherwise it might pick up noise.

The Arduino can then be tucked inside the top of the pendant like this.

Before refitting the back of the pendant, put some Kapton tape or insulating tape over the top two magnets, to prevent them from possibly shorting out connections on the Arduino.

# PanelDue pass through

To connect a PanelDue to the port as well as the pendant, the DOUT signal from the PanelDue (which is usually the blue wire in a 4-core cable) must be connected to the RX0 pin of the Arduino in the pendant (which in the wiring list I have given uses the green wire in the 4-core pendant cable). So connect the blue wire from PanelDue to the green wire of the pendant. Connect the DIN (green) PanelDue wire to the UTXD pin of the PanelDue connector on the Duet (or IO_0_OUT if using a Duet 3). The +5V pin on the Duet goes to the +5V (red) wire of both the pendant and the PanelDue, likewise the ground connection goes to the yellow wires of both.

![cnc_pendant_04.jpg](/manual/sensors/cnc_pendant_04.jpg =600x)

I used solder sleeves to connect the PanelDue and pendant cables to a short length of 4-core cable that plugs into the Duet, as shown here before I shrunk the heatshrink over the joints.