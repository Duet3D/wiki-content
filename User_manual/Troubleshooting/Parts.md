---
title: Connector and spare part numbers
description: This is a growing list of connectors and spare part numbers. If a spare part is required that is not on the list please check the source on github or ask on the forums 
published: true
date: 2024-06-13T19:40:26.183Z
tags: 
editor: markdown
dateCreated: 2021-12-05T22:43:02.549Z
---

![connectors_01.jpg](/manual/troubleshooting/connectors_01.jpg =300x)

# Duet 3

## Connectors used on main and on most expansion boards

2.54mm Molex compatible connectors are used for everything except stepper motor and medium-current outputs. These are widely available from the usual electronic component distributors, and on eBay.

* 2-way shell: 22-01-2025 or 22-01-3027 (or Würth Elektronik 61900211621)
* 3-way shell : 22-01-2035 or 22-01-3037 (or 61900311621)
* 4-way shell : 22-01-2045 or 22-01-3047  (or 61900411621)
* 5-way shell : 22-01-2055 or 22-01-3057  (or 61900511621)
* Crimp pins for all of the above: 08-50-0032 (for 22-01-20x5 series shells), 08-50-0113 or 08-50-0114 (for 22-01-30x7 shells), 61900113722DEC for the Würth Elektronik shells

Please note, the Molex KK compatible shells and crimp pins that we supply with Duets are usually made by Würth Elektronik. The crimp pins made by Würth should only be used with shells made by Würth.  In particular, they will not lock into Molex 22-01-20x5 shells.

Stepper motor (4-pin) and medium-current (2-pin) outputs: JST VH series or compatible:

* 2 way shell VHR-2N (JST) or VH396-02H (Valcon)
* 4 way shell VHR-4N (JST) or VH396-04H (Valcon)
* Crimps for the above: BVH-21T-P1.1B (JST) or VH396T-L (Valcon)

6-pin SWD connector (JST ZH) : ZHR-6 (note it is normally easier to buy a pre crimped tail and JST ZH are quite fiddly to crimp)

The fork connectors for the power supply are a generic "blue" max  2.5mm^2 like [this version from RS](https://uk.rs-online.com/web/p/fork-terminals/0534626/)  (many alternatives are available, for example from suppliers on eBay).

## Connectors used on Duet 3 Toolboard 1LC

**Power in**: 2-pin JST VH (see above)

**CAN**: 4-pin JST ZH. These are difficult to crimp because they are so small, so we supply pre-terminated connectors. You can connect longer wires to them by hand soldering or using small size (white) solder sleeves.

**Stepper motor**: 4-pin JST PH

**OUT_0**:  Suitable sized Ferrules (we supply 0.5mm^2 white ferrules)

### Version 1.0 tool boards

**Other screw terminals**: These are not high current so fitting the wires directly into the screw terminals is fine. Using small ferrules is also fine.

**TEMP_1**: 2-pin JST ZH

**IO_1** and **IO_2**: 3-pin JST ZH

### Version 1.1 tool boards

**OUT1** and **IO_0**: 4-pin JST PH

**OUT2**, **IO_1** and **IO_2**: 3-pin JST PH

**TEMP_0 and TEMP_1**: 2-pin JST PH

## Connectors used on Duet 3 Roto Toolboard

**CAN and power**: XT30 2+2

**Stepper motor**: 4-pin Molex Microfit 2x2

**OUT_0, TEMP_0, TEMP_1**: 2-pin Molex Microfit

**OUT_2**: 3-pin Molex Microfit

**IO_1, IO_2, LED**: 3-pin JST PH2.0 	

**OUT_1, IO_0**: 4-pin JST PH2.0 	

**IO_3**: 4-pin footprint to mount ITR20001/T reflective optical sensor

**SZP, I2C**: 4-pin 0.5mm pitch FFC

## Voltage regulators

| Duet 3 | 3.3V internal | 3.3V external | 5V | 12V |
|:---|:---|
| Mainboard 6HC | AP7361C-33E-13 | AP2127K-ADJTRG1 | AOZ1284PI | TS30041-M000QFNR |
| Mainboard 6XD | AP7361C-33E-13 | AP2127K-ADJTRG1 | AOZ1284PI | TS30041-M000QFNR |
| Expansion 3HC | AP2127K-ADJTRG1 | AP2127K-ADJTRG1 | TS30041-M000QFNR | TS30041-M000QFNR (up to version 1.01a) |
| Toolboard 1LC | AP7366-W5-7 || SPX1117M3-L-5-0/TR | TS30041-M000QFNR |
| Expansion 1XD | - | - | MAX15062CATA+T | - |
| Expansion 1HCL | - | - | TS30041-M000QFNR | MAX15062CATA+T |
| Mini 5+ | AP7366-W5-7 || TS30041-M000QFNR | TS30041-M000QFNR |

# Duet 2

## Molex Style Connectors

The connectors for the stepper motor,  endstop switch, thermistors and fans on the Duet 2 are Molex KK type. The Dupont-style connectors typically used with RAMPS and other electronics will also fit them. However, for maximum reliability we recommend using the correct Molex female connectors or compatible connectors, because they lock in place and will only fit one way round.

Molex KK connectors are widely available from the usual electronic component distributors, and on eBay.  There are compatible connectors available from Würth Elektronik and JYK.  The commonly available KF2510 connector kits also fit quite well and include extra pieces for future expansion.

Please note that the shells and crimp pins that supplied with Duets are usually made by Würth Elektronik. The crimp pins made by Würth should only be used with shells made by Würth.  In particular, they will not lock into Molex 22-01-20x5 shells.  In general you should use crimp pins and shells from the same manufacturer to ensure compatibility.

Here are the compatible part numbers:

| Part | Used for | Molex P/N (current) | Molex P/N (legacy) | Würth P/N | JYK P/N |
|:---|:---|
| 2-way shell | Thermistors and fans | 22-01-3027 | 22-01-2025 | 61900211621 | H2500-02 |
| 3-way shell | Endstops | 22-01-3037 | 22-01-2035 | 61900311621 | H2500-03 |
| 4-way shell | Stepper motors, PanelDue and Z-probe | 22-01-3047 | 22-01-2045 | 61900411621 | H2500-04 |
| 5-way shell | Z-probe on Duet 2 Maestro | 22-01-3057 | 22-01-2055 | 61900511621 | H2500-05 |
| 8-way shell | Smart Effector | 22-01-3087 | 22-01-2085 | 61900811621 | H2500-08 |
| Crimp pin |  | 08-50-0114 | 08-50-0032 | 61900113722DEC | T2500-02 |

## Ferrules

Ferrules are made in different colours to identify different wire thickness compatibility, however we originally supplied in the different colours for ease of identifying +/- wires.

Note there are different colour schemes for ferrule colouring so check that the ferrule is appropriate for the wire thickness you intend to use.

There are five sizes of ferrules supplied:

| Wire size (mm²) | Wire size (AWG) | Wire count | Ferrule type |
|:---|:---|
| 0.5 | 20 | 1 | Single white ferrule |
| 1.0 | 17 | 1 | Single red ferrule |
| 1.5 | 16 | 1 | Single black ferrule |
| 1.0 | 17 | 2 | Double red ferrule |
| 1.5 | 16 | 2 | Double black ferrule |

## Screw Terminals

The large terminals (6.35mm spacing) used for VIN and bed are:

Kaifeng KF635-6.35-2P

this is compatible with the [Metz connect 6.35mm equivalent](http://www.farnell.com/datasheets/1838863.pdf?_ga=2.22065778.853098835.1519491893-2089443813.1483525834&_gac=1.140126599.1516717201.EAIaIQobChMIja-556Pu2AIVz7DtCh0OLAshEAQYASABEgJWWvD_BwE)

The smaller terminals  (3.5mm spacing) used for the heater terminals other than the bed are:

KF128L-3.50

this is compatible with the [Metz connect 3.5mm equivalent](http://www.farnell.com/datasheets/2336353.pdf)

## Replacement parts

### Fan mosfet

If you short out a PWM fan, the fan driver mosfet will burn out. The original part number on most Duets is **PMV40UN2** or **PMV40UN2R** which is available from the usual electronic component distributors such as Farnell/Newark/element14, Digikey, Mouser and RS. You can also use type **PMV20XNER** or **AO3400A** or **AOSS32334C** which have a higher peak current rating and are fitted as standard in recent production Duets.

The component identifiers on the board are:

* Fan 0 mosfet: TR5
* Fan 1 mosfet: TR9
* Fan 2 mosfet: TR1

To remove the old mosfet, you will need either a hot air desoldering tool with a small nozzle, or low melting point solder such as ChipQuik . If using hot air, the hot air is likely to melt or at least discolour the white plastic parts of the adjacent connectors.  You can use a shield made from e.g. corrugated cardboard covered with Kapton tape to shield them from the hot air.  Alternatively, pull the white plastic part off the pins using pliers (this is easier if you heat the pins to 180C with hot air first), and replace it afterwards. The new mosfet can be soldered in place using a fine-tipped soldering iron, or by putting a little no-clean flux on the pads, placing the new mosfet on top, and using hot air again. Hot air soldering/desoldering is much easier if you heat the whole board to about 100-125C on an electric hotplate.

![connectors_02.jpg](/manual/troubleshooting/connectors_02.jpg =300x)

*Example of damaged mosfets*

### Heater MOSFETs

Note when removing any of the heater MOSFETs there is significant heat-sinking into the copper of the board. A combination of hot air and a heated plate will be needed to remove one successfully.

#### Bed Heater

TR2: this is an IPD036N04LGBTMA1

#### E0, E1 Heaters

1.04a and later, TR3, TR4: this is an AOD4184A (you an also use IPD036N04LGBTMA1)

Prior to version 1.04a it was a IPD036N04LGBTMA1

### VSSA fuse

If you get a short in your wiring between your heater or fan wiring and the VSSA side of a thermistor connection, and you have a Duet 2 board revision 1.0 or 1.01 or a white Duet 2 Wifi pre-production board, then fuse F1 will blow. The symptoms are:

* All thermistors will show as open circuit;
* The resistance between a VSSA pin on a thermistor connector and a GND pin on an endstop connector, which should normally read less than 5 ohms, will be very high (the pins are labelled on the underside of the Duet).

If this happens to your Duet, you should first identify and fix the short. After that, do one of the following:

1. Replace the original fuse with any fast acting 0603 size fuse having a rated current of between 125 and 250mA and a resistance of between 2 and 4 ohms. Suitable part numbers are Kamaya **FCC16151AD-B** and Littlefuse **0438.250WR**. If you choose the Littlefuse part and the old fuse is also the Littlefuse part (recognisable by its metal end caps), you may be able to solder the new fuse on top of the old fuse. Otherwise, you will need hot air soldering equipment to remove the old fuse.
1. Bypass the original fuse with an external cartridge fuse, fitted between one of the VSSA connections and one of the ground connections.
1. Bypass the original fuse with a self resetting fuse as is fitted on Duet 2 board revisions 1.02 and later. Suitable part numbers for the self-resetting fuse are Multicomp **MC36207** from Farnell, Bourns **MF-MSMF014** and Bel Fuse **0ZCJ0020FF2E**. Connect the fuse between the E0 thermistor VSSA pin and the E1 endstop ground pin. Optionally, connect a 10K resistor between the TCK pin of the JTAG pads and the VSSA pin of the E1 thermistor connector, which will allow the firmware to warn you when there is a short to VSSA. Note: the labels on the JTAG pads are wrong, the two columns should be swapped. So the TCK pin is the one labelled GND next to the one labelled TCK. Here is a photograph showing this modification.

![connectors_03.jpg](/manual/troubleshooting/connectors_03.jpg =300x)

The small green component bearing the letters 'bF' is the self-resetting fuse, and the component with stripes is the optional 10K resistor. A piece of Kapton tape was put on the board first to provide additional insulation.

### Blade fuses

PCB revisions 1.03 and later have a 1A mini auto blade fuse protecting the fan circuits when the fan voltage selection jumper is in the VIN position. You can increase the fuse rating to 2A or even 3A if your fans, air pumps etc. draw more than 1A in total.

PCB revision 1.04 has the same 1A fuse for the fan circuits and also a 15A fuse for the bed heater circuit and a 7.5A fuse for everything else. These are mini blade fuses. The 1A can be a little hard to find but the other ratings are widely available.

### Voltage regulators

U2 - 3.3V Duet 2 WiFi / Ethernet  (v1.04 and later) and Maestro: AP7361C-33E-13

U2 - 3.3V Duet 2 WiFi / Ethernet (v1.03 and earlier): TC2117-3.3VDBTR

U3 - 5V Duet 2 WiFi / Ethernet / Maestro: A4403GEUTR-T (tricky to solder QFN chip)

# Common and other parts

## PanelDue TFT panel backlight inverter IC

If the TFT panel of a **non-integrated** PanelDue assembly suddenly refuses to light up, chances are that the backlight inverter chip has failed. This is a 6-pin chip marked L6CE or L6EN or L6GE or similar (the Pin 1 marking bar can easily be mistaken for a letter I in front of the L). The part number is MP3202DJ. It is available from RS Components, Digikey and Mouser, and also on eBay and Ali Express.

The backlight inverter of the integrated PanelDues rarely fails, but if it does then the part number is on most of them is PT4110 in SOT-89-5 package. One batch of PanelDue 7i was made using MP3302 instead due to component shortages; these must be replaced with tne same type because another change was made to accommodate them.

## SD card socket

Used on all Duet 2 and 3 main boards. The recommended part is Würth Elektronik 693071020811

## Micro USB socket

Used on all Duet 2 and 3 main boards except MB6XD, and PanelDue v2 and v3 (but not 5i and 7i). The recommended part is Amphenol FCI 10103594-0001LF

For PanelDue 5i and 7i, a compatible part is Würth Elektronik 614105150721