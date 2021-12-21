---
title: Connecting and configuring fans
description: 
published: true
date: 2021-12-15T22:24:30.665Z
tags: 
editor: markdown
dateCreated: 2021-09-24T16:04:06.507Z
---

# Scope

**This document is relevant to:** all Duet boards
**Firmware versions:** all firmware versions

# Introduction

Duet mainboards support a variety of fans; depending on the Duet board, two-, three- and four-wire PWM-capable fans, running at voltages of 5V, 12V, VIN or an externally-supplied voltage. Fans can be always-on, thermostatically-controlled, and/or G-code controlled.

# Overview

## Supported fans

The rotation speed of most commonly-available fans is usually voltage-controlled, i.e. lowering the voltage reduces fan speed. However, Duet boards use Pulse Width Modulation (PWM) to control the speed; this effectively means the fan is getting the full voltage, but this is being turned on and off very quickly. The speed of most fans can be controlled this way, but some fans only support voltage control not PWM, and will only work at 100%. The fan speed may not always scale smoothly with PWM. Ideally, choose fans that support PWM speed control.

## Duet boards and fan outputs

| Duet board | Number of fan headers |||| Total maximum current limit | Supported voltages |
|:---|:---|
|  | Always-on | 2-pin | 3-pin | 4-pin | | |
| [Duet 3 Mainboard 6HC](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6HC_Hardware_Overview) | 1 | 3 | - | 3 | 800ma @ 12V, 10A @ VIN (fuse) | 12V / VIN / external power, in 2 banks |
| [Duet 3 Mini 5+](/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Hardware_Overview) | - | 2 | - | 2 | 800ma @ 12V, 7.5A @ VIN (fuse) | 12V / VIN / external power, in 2 banks |
| [Duet 3 Expansion 3HC](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_3HC_Hardware_Overview) | 1 | 3 | - | 3 | 800ma @ 12V, 7.5A @ VIN (fuse) | 12V / VIN / external power, in 2 banks |
| [Duet 3 Toolboard 1LC](/Duet3D_hardware/Duet_3_family/Duet_3_Toolboard_1LC) | - | - | 1 | 1 | (v1.0 board) 800ma @ 12V | 12V |
| ^^ | ^^ | ^^ | ^^ | ^^ | (v1.1 board) 800ma @ 12V, 2A @ VIN | 12V / VIN |
| [Duet 3 Expansion 1XD](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1XD) | - | 2 | - | - | 4A @ VIN | VIN |
| [Duet 2 WiFi and Ethernet](/Duet3D_hardware/Duet_2_family/Duet_2_WiFi_Ethernet_Hardware_Overview) | 2 | 3 | - | - | 2A @ 5V (total 5V/3.3V), 1A @ VIN (fuse) | 5V / VIN / external power |
| [DueX2 and DueX5](/Duet3D_hardware/Duet_2_family/DueX2_and_DueX5) | 1 | 6 | - | - | 2A @ 5V (total 5V/3.3V), 1A @ VIN (fuse) | 5V / 12V / VIN / 5V external power |
| [Duet 2 Maestro](/Duet3D_hardware/Duet_2_family/Duet_2_Maestro) | 1 | 3 | - | - | 2A @ 5V (total 5V/3.3V) | 5V / VIN / external power, in 2 banks |

**Note:** fans can also be connected to spare heater outputs. These run on VIN voltage, and usually have enough current capacity for even the biggest fans.

Duet 2 WiFi/Ethernet and Maestro can support 3- and 4-wire fans, see below for details on how to connect them. 

## Board-specific notes on voltage selection

### Tabs {.tabset}

#### Duet 3 Mainboard 6HC

The 3-pin jumpers above each bank of fan connectors (OUT4 to OUT6, and OUT7 to OUT9) select the voltage for each bank of fans, of either VIN (10A fuse) or 12V (800mA, supplied by onboard 12V regulator. You can supply a different voltage to the centre pin of the 3-pin jumper, pin VOUTLCx, to run different voltages.

#### Duet 3 Mini 5+

The 3-pin jumpers above each bank of fan connectors (OUT3 and OUT4, OUT5 and OUT6) select the voltage for each bank of fans, of either VIN (7.5A fuse) or 12V (800mA, supplied by onboard 12V regulator. You can supply a different voltage to the centre pin of the 3-pin jumper, pin VOUTLCx, to run fans at different voltages.

#### Duet 3 Expansion 3HC

The 3-pin jumpers above each bank of fan connectors (OUT3 to OUT5, and OUT6 to OUT8) select the voltage for each bank of fans, of either VIN (10A fuse) or 12V (800mA, supplied by onboard 12V regulator. You can supply a different voltage to the centre pin of the 3-pin jumper, pin VOUTLCx, to run different voltages.

#### Duet 2 WiFi / Ethernet

The 3-pin jumper (V_FAN) allows you to select whether all fans (three controlled fan connectors FAN0, FAN1 and FAN2 plus two always-on fan connectors) are powered from the VIN supply or from the Duet's 5V supply. You can supply a different voltage to the centre pin of the 3-pin jumper, pin V_FAN, to run fans at different voltages.

#### DueX2 and DueX5

DueX boards have 6 further PWM controllable fans (FAN3 - FAN8). V FAN JUMPER SELECT selects voltage for PWM fans, between 5V (internally or externally supplied), 12V internal and VIN. 

*DueX board versions before 0.8 had 5 fan connections see the [DueX hardware page](/Duet3D_hardware/Duet_2_family/DueX2_and_DueX5) for details.*

#### Duet 2 Maestro

There are two 3-pin jumpers (V_FAN_A and V_FAN_B) to select fan pin voltages between VIN or the Duet's 5V supply. V FAN A controls FAN0, FAN1 and the always-on fan pins, V_FAN_B controls FAN2. You can supply a different voltage to the centre pin of the 3-pin jumper, pin V_FAN_A or V_FAN_B, to run fans at different voltages.


## General recommendations

* Our intention is that in a 3D printer with a single print head, you define/use **Fan0 for the print cooling fan** and **Fan1 for the heatsink fan**. This is the easiest configuration to use because it's what most G-code slicing software (and the firmware, in RRF 2.x and earlier) expects by default.
* If you have a multi-tool setup, you can define the print cooling fan of each tool as part of the tool. This gets around the issue of Slicer software not supporting multiple part cooling fans. See "Allocating fans to tools" section below.
* Mixed-voltage setups are not directly supported, but the Duet switches the fans' connections to ground, so you may be able to connect each fan's positive side to the appropriate voltage and its negative side to the appropriate pin on the Duet. 
* Each controlled fan can be configured in firmware as a gcode controlled fan or as a thermostatically-controlled fan.
* See table above for our recommendations on continuous current draw. However, a current 'blip' that goes over this limit for a few seconds while the fan starts up should not harm them.

# Connecting fans

> **CAUTION!** 
Fans are polarised. When connecting a fan to a fan connector, the fan's positive wire (usually red) must be connected to the positive pin of that connector, usually marked V_OUT+ or V_FAN+. The fan's negative wire (normally black) must be connected to the negative pin if it is a PWM controlled fan connector, usually marked out[n]- or FAN[N]-, or the GND pin if it is an always-on fan connector.
**If you connect the fan the wrong way round, you may damage the fan, the Duet, or both.**
{.is-warning}

## Fan wire colours

2-wire fans usually have black and red wires. 3- and 4- wire fans usually come with pre-crimped housings, and the wire order is standard. The colour of the wires may not be, though. Below is the most common wire colours.

| | 2-wire fan | 3-wire fan || 4-wire fan ||
|---|---|
| -negative (GND) | Black | Black | Black | Black | Black |
| +positive (+V) | Red | Red | Yellow | Red | Yellow |
| Tachometer | | Yellow | Green | Yellow | Green |
| PWM | | | | Blue | Blue |

Fans can also come with wires all the same colour, eg all black or grey, unhelpfully. You will need to identify which wires are which to ensure you connect them correctly.

## Connecting fans to Duet fan pins

## Tabs {.tabset}

### Connecting a 2-wire fan

2-pin fan connectors can be either always-on or PWM-controlled. Check the wiring diagram for your board.

| Fan wires | Fan connector pin |||
| 2-wire | always on | 2-pin | 4-pin |
|---|---|---|
| - | - | - | GND |
| +ve (red) | +5V / +12V / +VIN | V_OUT+ / V_FAN+ | V_OUT+ / V_FAN+ |
| - | - | - | tach |
| -ve (black) | GND | out[n]- / FAN[n]- | out[n]- |

The **Duet 3 Toolboard 1LC** has one 3-pin fan connector. It uses the GND pin as the PWM pin.

| Fan wires | Fan connector pin |
| 2-wire | 3-pin |
|---|---|
| -ve (black) | out[n]- |
| +ve (red) | V_OUT+ / V_FAN+ |
| - | tach |


### Connecting a 3-wire fan

You will need to rewire a 3-wire fan to connect it to a 2- or 4-pin fan connector. If you plug a 3-wire fan into a 4-pin connector without rewiring, it will run 'always-on'. For all 3-wire fans, the tacho will only report the correct speed when the fan is running at 100%.

| Fan wires | Fan connector pin ||
| 3-wire | 2-pin | 4-pin |
|---|---|---|
| - | - | GND |
| +ve (red or yellow) | V_OUT+ / V_FAN+ | V_OUT+ / V_FAN+ |
| tach (yellow or green) | (see note) | tach |
| -ve (black) | out[n]- / FAN[n]- | out[n]- |

Note: On **Duet 2** boards running RRF 3.x, connect the tacho wire of a 3-wire fan to the cathode of a small signal diode (1N4148 should be OK) and connect the anode of the diode to any available digital input pin, to provide a reading of the fan RPM. For example, on Duet 2 WiFi/Ethenet connect it to pin PB6, and on Duet 2 Maestro use one of the four expansion pins. In RRF 2.x, only Duet 2 WiFi/Ethernet expansion pin PB6 supports tacho, and is defined by default. Or you can leave it not connected. 

The **Duet 3 Toolboard 1LC** has one 3-pin fan connector. It uses the GND pin as the PWM pin. A 3-wire fan should simply plug in.

| Fan wires | Fan connector pin |
| 2-wire | 3-pin |
|---|---|
| -ve (black) | out[n]- |
| +ve (red or yellow) | V_OUT+ / V_FAN+ |
| tach (yellow or green) | tach |


### Connecting a 4-wire fan

4-wire fans have a separate PWM input wire and an RPM sensor. On **Duet 3**, there are 4-pin fan headers specifically for these kinds of fan, which should plug straight in. Configure the fan with the PWM signal inverted and define the pin the tacho is connected to (see section below for firmware configuration).

| Fan wires | Fan connector pin ||
| 4-wire | 2-pin | 4-pin |
|---|---|---|
| -ve (black) | (see note) | GND |
| +ve (red or yellow) | V_OUT+ / V_FAN+ | V_OUT+ / V_FAN+ |
| tach (yellow or green) | (see note) | tach |
| PWM (blue) | out[n]- / FAN[n]- | out[n]- |

NOTE: on **Duet 2** with only 2-pin fan connectors, the recommended connections are:
* Negative (black) wires: connect to the - pin of an always-on fan connector.
* Positive (red or yellow): connect to the + pin of an always-on fan connector. 
* Tacho wire (yellow or green): optionally, connect the tacho wire of a 4-wire fan to the cathode of a small signal diode (1N4148 should be OK) and connect the anode of the diode to any available digital input pin, to provide a reading of the fan RPM. For example, on Duet 2 WiFi/Ethenet connect it to pin PB6, and on Duet 2 Maestro use one of the four expansion pins. In RRF 2.x, only Duet 2 WiFi/Ethernet expansion pin PB6 supports tacho, and is defined by default. Or you can leave it not connected. 
* PWM control (blue) wire: connect it to the FAN- pin of your chosen controlled fan connector.
* Configure the fan with the PWM signal inverted and define the pin the tacho wire is connected to with pullup resistor enabled (see section below for firmware configuration)

The **Duet 3 Toolboard 1LC** has one 3-pin fan connector. It uses the GND pin as the PWM pin. If you want to connect a 4-wire fan to this, you will need to rewire the fan connector.

| Fan wires | Fan connector pin |
| 4-wire | 3-pin |
|---|---|
| PWM (blue) | out[n]- |
| +ve (red or yellow) | V_OUT+ |
| tach (yellow or green) | tach |
| -ve (black) | Connect to an available GND pin on the board |

#### Notes

* RepRapFirmware expects a connected 4-wire fan to adhere to the [Intel 4-Wire Pulse Width Modulation (PWM) Controlled Fans specification](https://web.archive.org/web/20110726062453/http://www.formfactors.org/developer/specs/4_Wire_PWM_Spec.pdf).
* This means that the expected Pulses Per Revolution (PPR) from the tacho is 2, and the RPM reading calculated assumes that.
* The Intel fan specification also says that the fan must be able to go down to 30% PWM or lower. Below 30% PWM the fan is allowed to continue at the 30% PWM level, or go slower, or turn off. So at zero PWM the fan may still be at 30%. The Noctua fans all turn off at zero PWM.
* If the speed control of a 4-wire fan is not working well, i.e. fan speed does not scale well with PWM setting, you may be able to improve the response by connecting an external pullup resistor between the PWM input and +5V. 10K ohm would be a good starting point.
* For more information, see [this forum thread](https://forum.duet3d.com/topic/25511/).

## Using 12V fans when VIN is 24V

If you need to use 12V fans but your ``VIN`` is 24V and all your fans are 12V, then on the Duet 2 Wifi / Ethernet you have the following options using a buck-down converter:

1. Note that the Duet 3 has an internal 12v regulator so no buck converter is needed.
1. Wire the buck regulator input directly to the ``VIN`` terminals, and the output to the centre pin of ``V_FAN``^1^
1. Wire the buck regulator positive input to the ``VIN`` end of ``V_FAN``, the output to the centre pin of ``V_FAN``, and ground to any power ground connection (possibly the ground side of an always-on fan output)^2^
1. Put a jumper on ``V_FAN`` at the ``VIN`` end. Wire the buck regulator input to an always-on fan output. Wire the positive wires of 12V fans directly to the buck regulator output, and the negative wires to the ``FAN-`` pins of the controlled fan outputs as usual^3^
1. Here is an example wiring diagram for connecting the buck converter.
![blv_mgn_cube_12v_fan_on_24v.jpg](/manual/fans/blv_mgn_cube_12v_fan_on_24v.jpg =600x)
<!--1. [Here is an example implementation of a buck converter being used to power 12v fans and LEDs in a 24v system. **UPDATE LINK**]()-->

### Notes

^1^ all the fans must be 12V and there is no fuse protection

^2^ all the fans must be 12V and the input of the buck regulator is protected by the 1A fuse

^3^ the connection of a 12V fan is more complicated, but you can use 24V fans as well. The buck regulator input is fuse protected

### Using fans with mixed voltages

When using mixed fan voltages, the voltage fed to the centre pin of the V_FAN jumper must be the **highest** fan voltage in use. You can use a lower voltage fan in the same system by connecting the positive fan wire to the lower voltage supply (e.g. +12V from a buck regulator) and the negative fan wire to the FAN- pin of your chosen fan connector (leave the VFAN pin of that connector not connected).

# Fan modes

## Always-on fans

Most Duet boards provide at least one connector for fans that should be on any time the power is on. Many setup guides suggest this is how you should wire your hot end fan (but see thermostatic fans, below) to prevent heat from creeping back and melting the filament, jamming the hot end. You may also wish to attach a fan to move air across the underside of the Duet board, keeping the stepper drivers cool. Simply plug any such fan into one of these sockets, connecting the red wire to V_FAN and the black wire to GND.

If you want to run your other fans on a Duet 2 from 5V but your always-on fans from 12/24V, you can wire them directly across the power supply pins and ignore the Duet's connectors.

## Thermostatically controlled fans

As mentioned above, many hot ends require a fan to keep the heatsink cool, so that the filament remains solid until it passes through the heat break into the melt zone. This fan should be on any time the hot end is hot enough to melt plastic, but can safely be off when the hot end is cool even if the rest of the machine is on. Duet boards support this mode of operation. Plug your fan into one of the PWM fan pins, and configure it as a thermostatic fan for the appropriate heater by putting the appropriate M106 G-code in config.g. For example:

```
M106 P1 T45 H1
```

This sets fan 1 to run any time the temperature of heater 1 is above 45 Celsius. See [M106](/User_manual/Reference/Gcodes/M106) for details. We recommend you use the FAN1 connector for a thermostatically-controlled hot end fan, because on the Duet 2 WiFi / Ethernet in RRF 2.x it defaults to being on at power up , to provide maximum safety if you restart your Duet when the hot end is hot.

A thermostatically controlled hot end fan will be turned on automatically when you auto tune any heater that it monitors.

## G-code controlled fans

Printing PLA (and perhaps other plastics) benefits from additional cooling of printed layers, particularly when layers are printing quickly. That said, excessive cooling can cause problems with first layer adhesion or even interfere with a new layer's bonding to the previous one. Many slicer programs will introduce fan control G-codes to run the fan strongly for layers that print quickly, and only start running the fan after the first few layers. Attach/define such a fan to one of the connectors FAN0, FAN1, or FAN2. If your slicer doesn't support specifying which fan to control, it defaults to FAN0.

The gcode command to set the fan speed is M106 Pnn Svv where nn is the fan number (default 0 if the P parameter is not present) and vv is the required speed. The speed can be expressed either in the range 0 to 1, or in the range 0 to 255. A value of 1 or less will be assumed to be in the range 0 to 1. A value of 0 corresponds to off, and a value of 1 or 255 corresponds to full speed.

**In RRF 2.x, on Duet 2 WiFi/Ethernet, FAN1 is set up as a thermostatically-controlled fan by default because it is typically used to control the hot end heatsink fan.** To use it as a normal controlled fan, you must first cancel thermostatic mode by sending **M106 P1 H-1**.


# Configuring fans

## Firmware settings

## Tabs {.tabset}

### RepRapFirmware 3.x

Fans are first defined and configured by [M950](/User_manual/Reference/Gcodes/M950), then operated by [M106](/User_manual/Reference/Gcodes/M106).

#### Tabs {.tabset}

##### Duet 3

```
; 2-wire fans, normally controlled and thermostatic
M950 F0 C"out3" Q500 ; create fan 0 on pin out3 and set its frequency
M106 P0 S0 H-1       ; set fan 0 value. Thermostatic control is turned off
M950 F1 C"out4" Q500 ; create fan 1 on pin out4 and set its frequency
M106 P1 S1 H1 T45    ; set fan 1 value. Thermostatic control is turned on

: 3-wire fan with tacho
M950 F2 C"out4+out4.tach" ; Fan 2 uses out4, and using out4.tach as a tacho input

: 4-wire PWM fan and tacho
M950 F3 C"!out5+out5.tach" ; Fan 3 uses out5, but we are using a PWM fan so the output needs to be inverted, and using out5.tach as a tacho input
```
When using M950 to create a fan, the port name string may be either a single port, or two ports separated by the '+' sign. The second port is used to read the fan tacho. **Any CAN address at the start of the port name string applies to both port names.**

```
M950 F5 C"!out4+out4.tach" Q450 ; Create Fan 5 on the mainboard on OUT4 with a tacho input
M950 F0 C"!1.out3+out3.tach" Q450 ; Create Fan 0 on expansion board 1, output OUT3  with a tacho input
```

##### Duet 2

```
; 2-wire fans, normally controlled and thermostatic
M950 F0 C"fan0" Q500 ; create fan 0 on pin fan0 and set its frequency
M106 P0 S0 H-1       ; set fan 0 value. Thermostatic control is turned off
M950 F1 C"fan1" Q500 ; create fan 1 on pin fan1 and set its frequency
M106 P1 S1 H1 T45    ; set fan 1 value. Thermostatic control is turned on

: 3-wire fan with tacho
M950 F2 C"fan2+^pb6" ; Fan 2 uses the Fan2 output, and using PB6 as a tacho input with pullup resistor enabled

: 4-wire PWM fan and tacho
M950 F2 C"!fan2+^pb6" ; Fan 2 uses the Fan2 output, but we are using a PWM fan so the output needs to be inverted, also we are using PB6 as a tacho input with pullup resistor enabled
```
### RepRapFirmware 2x and earlier

For details of the configuration options available see the [M106 GCode section](/User_manual/Reference/Gcodes/M106)

```
; 2-wire fans, normally controlled and thermostatic
M106 P0 S0 I0 F500 H-1    ; set fan 0 value, PWM signal inversion and frequency. Thermostatic control is turned off
M106 P1 S1 I0 F500 H1 T45 ; set fan 1 value, PWM signal inversion and frequency. Thermostatic control is turned on

: 3-wire fan with tacho
M106 P2 S0 I0 F25000 H-1 ; set fan 2 value, PWM signal inversion and frequency. Thermostatic control is turned off. PB6 is used by default for tacho signal.

: 4-wire PWM fan and tacho
M106 P2 S0 I1 F25000 H-1 ; set fan 2 value, invert PWM signal, and set PWM frequency to 25kHz. Thermostatic control is turned off. PB6 is used by default for tacho signal.
```

## Allocating fans to tools

Most slicers do not support having multiple separate print cooling fans so they simply send M106 Snnn, rather than allowing the choice of which fan is allocated to which hotend.

To solve this, other fan channels can be mapped to fan 0 when a specific tool is selected for example:

```
M563 P0 D0 H1 F0 ; tool 0 uses extruder 0, heater 1 and fan 0
M563 P1 D1 H2 F1 ; tool 1 uses extruder 1, heater 2 and fan 1
M563 P2 D2 H3 F2 ; tool 2 uses extruder 2, heater 3 and fan 2
```

After this whenever tool 0 is selected, sending M106 Snnn will control fan 0. With tool 1, fan 1 and tool 2 fan 2.

For more examples see the [tool definition section](/User_manual/Machine_configuration/Configuration_cartesian#tool-definition-section) of the config.g file.

# Using fan connectors for other functions

See [Connecting servos and controlling IO pins](/User_manual/Connecting_hardware/Motors_servos).