---
title: Duet 3 Mainboard 6HC
description: Overview of Duet 3 Mainboard 6HC hardware features.
published: true
date: 2025-05-07T16:13:59.284Z
tags: 
editor: markdown
dateCreated: 2021-07-09T14:00:13.273Z
---

![Image of the Duet 3 Mainboard 6HC taken from an angle](/duet_boards/duet_3_mb6hc/duet3_mb_6hc-v1.02.png =500x)

# Introduction

The Duet 3 Mainboard 6HC is a next-generation controller board that builds on Duet3D's experience in developing the most flexible 3d printer control solutions. It is the core of the Duet 3 family which provides control for a wide range of machines including 3d printers, CNCs, lasercutters and more. The overall aim with the Duet 3 series is to allow for maximum flexibility of machine design through highly capable mainboards, expansion boards, smart tool boards and custom expansion modules. The flexibility of configuration and advanced features are enabled by our innovative [RepRapFirmware](/User_manual/RepRapFirmware/RepRapFirmware_overview){target=_blank} running on the Mainboard and DuetSoftwareFramework running on the single board computer.

# Features

![Render of the Duet3 Mainboard 6HC v1.02 overlaid with the key features of the board grouped by location](/duet_boards/duet_3_mb6hc/duet3_mainboard_6hc_v1.2_features_d1.1.png =500x)


## Hardware Specification

The main hardware features of the Duet 3 Mainboard 6HC are listed below.

| HARDWARE SPECIFICATION ||
|---|---|
| **Processor** | [ATSAME70Q20B](https://www.microchip.com/en-us/product/ATSAME70Q20){target=_blank} or compatible|
| **Processor features** | 32-bit, 300MHz ARM Cortex M7, 1Mb flash, 384Kb RAM, hardware floating point (double precision), DMA, 32Kb cache |
| **Networking/Comms** | 10BaseT/100BaseTX Ethernet, WiFi via optional Duet 3 WiFi Module (6HC v1.02 or later only, see 'Network, user interface and plugins' below) or via attached SBC; USB port; serial port; 2x CAN-FD bus |
| **On-board stepper drivers** | 6 x  [TMC2160a](https://www.trinamic.com/products/integrated-circuits/details/tmc2160-ta/){target=_blank} |
| **Stepper driver features** | Up to 6.3A peak current, microstep interpolation from any setting to x256, stall detection, stealthChop2 |
| **High current outputs** | 1 x 15A^1^, 3 x 6A each |
| **Medium current outputs** | 6 x PWM-controlled outputs, of which 3 support tacho input. Voltage selectable between VIN / 12V / external power, in 2 banks. 1 x VIN-voltage, always-on output. 1 x 12V, always-on output. |
| **Thermistor/PT1000 inputs** | 4 x inputs, optimised for 100K thermistors and PT1000 sensors |
| **Inputs/Outputs** | 9 x on-board I/O connectors for endstop, filament monitor, Z-probe, hobby servo or PanelDue connection. Inputs are 30V-tolerant. Also one output with 5V signal level for hobby servo, laser control or VFD. |
| **Power monitoring** | VIN voltage monitoring allows for state save on power failure.12V regulator output voltage also monitored |
| **SD card interface** | On-board high speed SD card socket. External SD card socket (e.g. on attached PanelDue) also supported from v1.02|


| **EXPANSION** ||
|---|---|
| **Support for attached Raspberry Pi or other Single Board Computer (SBC)** | Yes |
| **External stepper driver support** | Via CAN-FD bus |
| **Stepper driver expansion** | 20+ via CAN-FD-connected expansion boards |
| **High current output and/or thermistor expansion** | 20+ via CAN-FD-connected expansion boards |
| **PT100 and thermocouple daughterboard support** | Supports 2 x daughterboards (4 channels) on board. 50+ via CAN-FD-connected expansion boards. |
| **LCD support** | PanelDue, or via HDMI touch screen attached to the SBC. |
| **LED strip support** | RGB/RGBW Neopixel or DotStar. Note: LEDs can draw a lot of current so external 5V supply is required to drive more than ~8, see the operating limits below. |
| **Other expansion** | A huge variety supported via CAN-FD-connected expansion boards. WiFi via optional Duet 3 WiFi Module (6HC v1.02 or later only, see 'Network, user interface and plugins' below). |


| **NOTES** |
|---|---|
| ^1^ Note that the board is rated to 18A on the '15A high current output'; however it is supplied with a 15A fuse fitted (18A are difficult to source). If you need 18A on this output then you need to fit a 20A fuse and take further precautions against over-current. |

## Operating limits

## Tabs {.tabset}

### Revision v1.02

|:--|:--|
| **Stepper drivers** | Up to 6.3A peak current |
| **High current outputs** | OUT0 up to 18A (see note above), OUT1/2/3 up to 6A each |
| **Input power voltage** | 11V to 48V for VIN up to 30A, 0V to 48V for OUT0 |
| **Inputs/Outputs** | Inputs are 30V-tolerant |
| **Input connector rated current** | 25A maximum, or fused limit (whichever is lower) |
| **Fuses** | 15A for V_Fused, 15A for OUT0. |
| **12V current limit** | 800ma (total for out3-8 ports, when 12V selected) |
| **5V and 3.3V current limit** | 800mA total on 5V and 3.3V, of which no more than 300mA from 3.3V. |
| **Maximum ambient temperature** | 70°C |

### Revision v1.01 and earlier

|:--|:--|
| **Stepper drivers** | Up to 6.3A peak current |
| **High current outputs** | OUT0 up to 18A (see note above), OUT1/2/3 up to 6A each |
| **Input power voltage** | 11V to 32V for VIN, 0V to 32V for OUT0 |
| **Inputs/Outputs** | Inputs are 30V-tolerant |
| **Input connector rated current** | 25A maximum, or fused limit (whichever is lower) |
| **Fuses** | 15A for V_Fused, 15A for OUT0. |
| **5V current limit** | 3A total on 5V and 3.3v, including the internal current consumption (around 200-300mA), any PanelDue or other display, and any endstops/Z probes that draw significant power, and any connected SBC powered by the Duet. **Note: future versions of Duet 3 MB6HC are likely to have the 5V current limit reduced to about 1.0A, which will make about 0.7A available for use by external devices.** |
| **12V current limit** | 800ma (out4-out9 pins only, when 12V selected) |
| **Maximum ambient temperature** | 70°C |



## Firmware notes

* **Version 1.02 and later ** require RRF 3.4.5 or later (latest stable version is recommended)
* **Version 1.01 and earlier ** are compatible RepRapFirmware version 3.0 and later (latest stable version is recommended)
* For **firmware update instructions**:
  * Standalone mode - [Installing and updating firmware](/User_manual/RepRapFirmware/Updating_firmware){target=_blank}. 
  * SBC mode - [SBC setup for Duet 3](/User_manual/Machine_configuration/SBC_setup){target=_blank}.
* There are some **limits on firmware and hardware configuration**. See:
  * [Firmware configuration limits](/User_manual/RepRapFirmware/RepRapFirmware_overview#firmware-configuration-limits){target=_blank}
  * [CAN expansion configuration limitations](/User_manual/RepRapFirmware/CAN_limitations){target=_blank}

## Open Source

Importantly Duets are Open:

* The Duets are Open Hardware, see [our license here](https://github.com/Duet3D/Duet3-Mainboard-6HC/blob/master/LICENSE){target=_blank}.
* All hardware [source files](https://github.com/Duet3D/Duet3-Mainboard-6HC){target=_blank} are available on Github.
* Both the [Duet Web Control](https://github.com/Duet3D/DuetWebControl){target=_blank} web interface and [RepRapFirmware](https://github.com/Duet3D/RepRapFirmware){target=_blank} are [Open Source Software]( http://www.gnu.org/licenses/gpl-3.0.en.html){target=_blank} with source files available and actively maintained, see [Contributing to development](/User_manual/Reference/Developers){target=_blank} for more information.
* The Duet hardware and RepRapFirmware are built with Open tools: designed in [KiCad](http://kicad.org/){target=_blank} and [Eclipse](https://eclipse.org/){target=_blank} using open tools means the barrier to getting involved is as low as possible.

## Feature Comparison

See the [Hardware overview](/Duet3D_hardware/Hardware_overview){target=_blank} page for a feature comparison table between different versions of the Duet.

# Physical properties


## Dimensions

## Tabs {.tabset}
### v1.02
[![Dimensions and mounting holes for the Duet 3 MB 6HC v1.02](/duet_boards/duet_3_mb6hc/duet3_mb_6hc_v1.02_dimensions_d1.0.png =500x)](/duet_boards/duet_3_mb6hc/duet3_mb_6hc_v1.02_dimensions_d1.0.png){target=_blank}

### v0.6-v1.01a
[![Dimensions and mounting holes for the Duet 3 MB 6HC v0.6 -v1.01](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_dimensions.png =500x)](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_dimensions.png){target=_blank}

## Mounting

On Duet 3 Mainboard 6HC the mounting holes are isolated and not plated through, they have a keep out area around the M4 clearance hole of an additional 2.2mm radius. Any metallic mounting solution should keep inside this keep-out area.

![Detail of the mounting hole size and clearance for the Duet 3 MB 6HC v0.6 and later](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_mounting.png =230x)

Please note:

* If you mount the board on a metal surface, use standoffs to ensure that the back of the board cannot short against the plate. A minimum standoff length of 5mm is advised, however larger is preferred for better cooling.
* **Do not use metal washers under the mounting screws**, or screws with large heads (e.g. button head). Nylon washers are recommended and supplied with the board.  With a metal washer or large screw head there is a risk of creating a short.

## Cooling

In many applications passive cooling will be sufficient, especially if the board is mounted vertically in a well ventilated position. If active cooling is needed then a fan blowing across the back of the board along the line of the stepper drivers is recommended.

The stepper drivers' heatsinks are connected to the PCB and the majority of the heat is dissipated via the PCB so heatsinks on the stepper driver chips are largely ineffective.

## 3D model

The STEP file for the Duet 3 Mainboard 6HC is shared on the [Duet3D github here](https://github.com/Duet3D/Duet3-Mainboard-6HC){target=_blank}.

# Physical connections

## Wiring diagram

## Tabs {.tabset}

### Revision v1.02

[![Duet 3 MB 6HC v1.02 wiring diagram shwoing all the pin connections, click on the image for a larger version](/duet_boards/duet_3_mb6hc/duet3_mb_6hc_v1.02_d1.1_wiring.png =745x)](/duet_boards/duet_3_mb6hc/duet3_mb_6hc_v1.02_d1.1_wiring.png){target=_blank}

### Revision v1.0 and v1.01

[![duet_3_mb6hc_wiring_diagram_v1.0_v1.01.png](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_wiring_diagram_v1.0_v1.01.png =745x)](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_wiring_diagram_v1.0_v1.01.png){target=_blank}

### Revision v0.6

[![duet_3_mb6hc_wiring_diagram_v0.6.png](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_wiring_diagram_v0.6.png =500x)](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_wiring_diagram_v0.6.png){target=_blank}

### Revision v0.5
<!--removed the picture, just have the link for this very old board to speed up page loading -->
[duet_3_mb6hc_wiring_diagram_v0.5.png](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_wiring_diagram_v0.5.png){target=_blank}

**Caution!** On v0.5 boards the GND and V_FUSED legends on the underside of the board are the wrong way round! The ones in the wiring diagram above are correct. This is the same for the jumper block OUT7 - OUT9 that provides power to these connectors respectively (see below).

**Caution!** On v0.5 boards, do not connect anything to the OUT pin of the IO_5 connector, because on the prototype boards this pin is used to signal to the Raspberry Pi. The IO_5_OUT pin will be available on later version boards.


## Wiring Notes

|---|---|
| **CAUTION!** | The pinout of the 5-pin connectors is not the same as on the 5-pin Z-probe connector on the Duet Maestro! It has been changed to reduce the risk of shorting +5V to +3.3V. |
| **CAUTION!** | On v0.5 boards the GND and V_FUSED legends on the underside of the board are the wrong way round! The ones in the wiring diagram above are correct. This is the same for the jumper block OUT7 - OUT9 that provides power to these connectors respectively (see below).
| **CAUTION!** | On v0.5 prototype boards, do not connect anything to the OUT pin of the IO_5 connector, because on these boards this pin is used to signal to the Raspberry Pi. The IO_5_OUT pin is available on v0.6 and later boards. |

* The 4 wire motor and OUT1, OUT2 and OUT3 are [JST VH series connectors](http://www.jst-mfg.com/product/pdf/eng/eVH.pdf). They require a minimum of 22AWG wire (20AWG or 0.5mm^2^ recommended. Most NEMA17 size stepper motor wire will will not be thick enough to use in the normal way; but you can double the stripped part of the wire back on itself to bulk it up, and put a small length of heatshrink sleeving over the insulation to bulk up the insulation. You will need a suitable crimping tool for the crimp pins, for example Engineer PA21 (use the 2.2mm jaw opening to crimp the bare wire and the 2.5mm on to crimp the insulation). Alternatively you can solder the wire to the crimp pin
* The default 5V power configuration is Internal-5V-EN jumpered, 5V->SBC jumpered (the Duet is powering the SBC) , SBC->5V not jumpered. If you want the SBC to provide 5V to the Duet then remove the jumper from Internal-5V-EN and place a jumper on SBC-5V (leaving the 5V->SBC jumper in place).  **NOTE** this bypasses the 5V protection and a fault on the SBC may damage the Duet. See [Power distribution, 5V](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6HC_Hardware_Overview#h-5v) section below.
* The two banks of Low Current outputs (OUT4-6, OUT7-9) can be separately selected to be powered by either VIN or internal 12V. Total 12V fan current draw must not exceed 800mA.
* The separate OUT0 Power in allows for a different voltage to be supplied for the OUT0 high current output (e.g for a large bed heater). If this is not required VIN power must be applied to both the POWER IN and the OUT0 POWER IN terminals for OUT 0 to be powered.
* The SBC_3.3V is purely to ensure logic levels are the same between the Duet and the SBC. Do not attempt to use this pin to apply, or draw, 3.3V.

## Description of Connections

Duet 3 Mainboard 6HC provides the following connectors:

| Header | PCB label | Function |
|---|---|---|
| **1 x 6-way barrier strip** | POWER IN, GND, VIN | Two pins for main VIN and GND. VIN min/max: 11V to 48V (11V to 32V before v1.02) up to 30A|
| ^^ | OUT 0 POWER IN, GND, V_OUT0 | Two pins for the VIN and GND supply for the OUT_0 terminals. OUT0 voltage min/max: 0V to 48V (0V to 32V before v1.02) |
| ^^ | OUT 0, V_OUT0, OUT0- | Positive and negative OUT_0 terminals. OUT_0 is intended to drive a bed heater. The ground side of OUT_0 is switched by the mosfet and the positive side is protected by a 15A fuse. If using the OUT0 terminal to drive a SSR, take note that their polarity is opposite to the polarity of the VIN terminals. There is no flyback diode on this output, so if you connect a high-current inductive load, you must use an external flyback diode. |
| **1 x 3-pin KK connector** | EXT 5V | Open drain mosfet output for controlling an ATX-style power supply or a SSR. The +5V pin can also be used to provide a small amount of 5V power (through an internal 220 ohm resistor) so that the control terminals of an SSR can be connected directly between the +5V and PS_ON pins. |
| **1 x 3-pin KK header** | 5V SELECT | *v1.02 and later boards.* 5V supply select, between 5V internal regulator (no jumper), 5V_EXT, and 5V_SBC. See Power distribution > 5V documentation below for details. |
| **3 x 2-pin KK headers** | 5V Options | *v0.6 to v1.0a boards only, not populated on v1.0a*. 5V supply select. See Power distribution > 5V documentation below for details. |
| **6 x 4-pin JST VH connector** | DRIVER_0, DRIVER_1, DRIVER_2, DRIVER_3, DRIVER_4, DRIVER_5 | Stepper motor connections. (see note on JST VH connectors) |
| **3 x 2-pin JST VH connector** | OUT 1, OUT 2, OUT 3 | These are intended for extruder heaters or fans. The ground side of OUT_N is switched by a mosfet.  Maximum recommended current 6A each. These outputs are protected by flyback diodes. |
| **3 x 4-pin KK connectors with offset spigot** | OUT 4, OUT 5, OUT 6 | These medium current outputs are intended for PWM-controllable fans. The connector fits a standard PC-type 4-pin PWM fan. Alternatively, a 2-pin fan may be connected between the V_OUT_LC_1 pin (+ve) and the OUT_n_NEG pin (-ve). Maximum recommended current 2A each when supplied by VIN. |
| ^^ | ^^ | **Note:** These outputs are protected by a flyback diode connected to V_OUT_LC_1. Do not mix loads connected to V_OUT_LC_1 with the jumper set to 12V and loads connected to V_FUSED on the same bank. |
| **1 x 3-pin KK header** | OUT4-OUT6_SelectV | The positive supply to the OUT 4, OUT 5 and OUT 6 connectors is the centre pin of the 3-pin jumper block labeled OUT4-OUT6_SelectV. A jumper in the top position will power them from the fused VIN supply. Alternatively you can connect a 3-terminal buck regulator to the 3-pin jumper block to supply the required voltage to the centre pin. |
| ^^ | ^^ | *see note 1 below*. |
| **3 x 2-pin KK connectors** | OUT 7, OUT 8, OUT 9 | These are intended for fans. Maximum recommended current 2A each when supplied by VIN. |
| ^^ | ^^ | **Note:** These outputs are protected by a flyback diode connected to V_OUT_LC_2. Do not mix loads connected to V_OUT_LC_2 with the jumper set to 12V and loads connected to V_FUSED on the same bank. |
| **1 x 3-pin KK header** | OUT7-OUT9_SelectV | The positive supply to the OUT 7, OUT 8 and OUT 9 connectors is the centre pin of the 3-pin jumper block labeled OUT7-OUT9_SelectV. A jumper in the top position will power them from the fused VIN supply. Alternatively you can connect a 3-terminal buck regulator to the 3-pin jumper block to supply the required voltage to the centre pin.|
| ^^ | ^^ | *see note 1 below*. |
| **1 x 2-pin KK connector** | VFUSED | Always on, not switched. This is for powering an always-on fan or similar. Current limit 1.5A|
| **1 x 2-pin KK connector** | RESET_EXT | For an external normally-open reset switch. |
| **1 x 2-pin KK header** | ERASE | Add a jumper to erase the firmware. |
| **1 x 2-pin KK connector** | 12V | Provides 12V power for a PWM to 0-10V converter.|
| ^^ | ^^ | *see note 1 below*. |
| **1 x 3-pin KK connector** | LASER/VFD | *v1.0 and later boards.* This provides 5V power and a 5V level signal for a TTL-compatible input to a laser controller, a PWM-to-0 to 10V converter (for variable-frequency drives), or a servo. The control signal for this output is shared with OUT9, so don't use OUT9 if you use this connector. |
| **1 x 2x5 IDC connector** | PanelDue_SD | *v1.02 and later boards.* Connects the [PanelDue](/Duet3D_hardware/Accessories/PanelDue) UART and shared SPI bus for external SD card. Powered from 5V supply |
| ^^ | ^^ | **Note** The PanelDue UART is shared with io0.in and io0.out pins on the IO_0 header. |
| ^^ | ^^ | *see note 2 below*. |
| **1 x 2-pin jumper** | PD_CD_OVERRIDE | *v1.02 and later boards.* Add a jumper to connect Card Detect on the PanelDue to ground, to handle PanelDue versions that don't support Card Detect. |
| **4 x 2-pin KK connectors** | TEMP_0, TEMP_1, TEMP_2, TEMP_3 | Connections for thermistor or PT1000 sensors. |
| **1 x RJ45 network connector** | Ethernet | 100BaseT Port. non MDIX connect to an Ethernet switch, hub or MDIX enabled laptop port. If connecting to a non MDIX enabled port use a crossover cable. Orange LED on Ethernet port indicates Ethernet enabled, green LED indicates network activity |
| **9 x 5-pin KK connectors** | IO_0, IO_1, IO_2, IO_3, IO_4, IO_5, IO_6, IO_7, IO_8 | These are for endstop switches, Z probes, filament monitors, servos, and other low-voltage I/O functions. Each connector provides both 3.3V and 5V power. The inputs will tolerate up to 30V. The outputs are 3.3V signals levels with 470R series resistors. |
| **1 x 4-pin KK connector** | DS_LED | This is to connect and power DotStar LED strips. |
| ^^ | ^^ | *see note 2 below*.
| **1 x 6-pin JST ZH (ZHR-6) connector** | SWD | This is for firmware debugging|
| **1 x 2-pin jumper** | IO2 I2C BYPASS | *v1.02 and later boards.* Add a jumper to bypass the 10k resistor on IO2.in, so it can be used for I2C. **Note:** RepRapFirmware does not currently support I2C on Duet 3 boards. |
| **1 x 2x13 header** | SBC | This is for connecting a single board computer (SBC) such as a Raspberry Pi. |
| **1 x 2x6 header** | ESP | *v1.02 and later boards*: Header to connect an ESP WiFi board. |
| **1 x 2x5 header** | TEMPDB | This is for connecting PT100 and thermocouple interface boards. |
| **1 x RJ11 CAN connector** | CAN1_OUT | *v1.02 and later boards*: RJ11 connector for CAN1 (main CAN-FD bus). **USE CAN1** for connecting Duet expansion boards, see CAN-FD bus expansion section below. Termination resistor fitted so normally this board must be at the end of the bus. There are drill-to-disconnect jumpers that allow the termination resistor to be removed, however this is not required in normal operation. |
| ^^ | CAN_OUT |*v1.01a and earlier boards.* RJ11 CAN connector for CAN0 and CAN1. **USE CAN1** for connecting Duet expansion boards, CAN0 is for future expansion; see CAN-FD bus expansion section below. A permanent termination resistor is fitted, so this board must be at one end of the CAN bus |
| **1 x 2-pin KK connector** | CAN0_OUT | *v1.02 and later boards.* Secondary CAN-FD bus for future expansion. **Use CAN1** for connecting expansion boards, see CAN-FD bus expansion section below. Termination resistor fitted so normally this board must be at the end of the bus. There are drill-to-disconnect jumpers that allow the termination resistor to be removed, however this is not required in normal operation. |

**Notes**

1. Total 12V load should not exceed 800mA
1. 5V power can be sourced from multiple inputs (USB, External 5V input, SBC) as well as the internal 5V regulator. Total 3.3V and 5V load combined should not exceed 800mA when powered from the internal 5V regulator. see the "[Power Distribution, 5V](https://docs.duet3d.com/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6HC_Hardware_Overview#h-5v)" subsection below for more information

## LED indications

LEDs are provided to indicate the following:

| Label | Colour | Function |
|--|--|--|
| **V_FUSED** | Blue | Indicates presence of fused VIN power |
| **12V+** | Amber | Indicates presence of 12V power from on-board regulator |
| **5V+** | Red | Indicates presence of 5V power from on-board regulator |
| **3.3V+** | Green | Indicates presence of 3.3V power from on-board regulator |
| **USB** | Red | Indicates presence of 5V power from USB |
| **OUT_0** | Red | Next to the OUT 0 connector, indicates when on |
| **OUT_1** | Red | Next to the OUT 1 connector, indicates when on |
| **OUT_2** | Red | Next to the OUT 2 connector, indicates when on |
| **OUT_3** | Red | Next to the OUT 3 connector, indicates when on |
| **STATUS** | Red | Diagnostic LED. See description below |
| **ACT** | Green | Diagnostic LED. See description below |

The red LED labelled "STATUS" (labelled DIAG before v1.02) indicates the state of the board, as follows.

| LED | Meaning |
|:---|:---|
| Flashing steadily, about half a second off and half a second on | Normal operation, RepRapFirmware is running.  |
| ^^ | Any connected expansion board also has a diagnostic LED. When the expansion board starts up this LED will blank rapidly. If the expansion board is connected to a Mainboard running compatible firmware, the LED on the expansion board will switch to blinking synchronously with the Mainboard LED once time sync has been established across the CAN bus. |
| Glowing dimly, or off | Firmware has been erased |
| Flashing three times, then off for a while | Firmware CRC check failed |

The green LED labelled "ACT" (on v1.02 and later) indicates CAN-FD bus activity

## Pin names

For more information on pin names, see [Pin names](https://docs.duet3d.com/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names).

RepRapFirmware 3 uses pin names for user-accessible pins, rather than pin numbers, to communicate with individual pins on the PCB. In RRF 3 no user-accessible pins are defined at startup by default. Pins can be defined for use by a number of gcode commands, eg M574, M558, M950.

The Duet 3 series uses the pin name format "expansion-board-address.pin-name" to identify pins on expansion board, where *expansion-board-address* is the numeric CAN address of the board. A pin name that does not start with a sequence of decimal digits followed by a period, or that starts with "0." refers to a pin on the Duet 3 Mainboard 6HC.

| Function | Pin location | RRF3 Pin name | Notes |
|---|---|---|
| Outputs | OUT 0 | out0, bedheat | High current output, bed heater |
| ^^ | OUT 1 | out1 | Medium current outputs, hot ends |
| ^^ | OUT 2 | out2 | ^^ |
| ^^ | OUT 3 | out3 | ^^ |
| Outputs (4-pin) | OUT 4 | out4 | Fans, pumps. 2A limit per pin on VIN, 800mA limit total on internal 12V |
| ^^ | ^^ | out4.tach | ^^ |
| ^^ | OUT 5 | out5 | ^^ |
| ^^ | ^^ | out5.tach | ^^ |
| ^^ | OUT 6 | out6 | ^^ |
| ^^ | ^^ | out6.tach | ^^ |
| Outputs (2-pin) | OUT 7 | out7 | ^^ |
| ^^ | OUT 8 | out8 | ^^ |
| ^^ | OUT 9 | out9, laser, vfd | Fans, pumps. 2A limit per pin on VIN, 800mA limit total on internal 12V. <br>Pin shared with VFD/Laser/Servo drive header |
| ^^ | OUT 10 / SERVO | servo, out10 | v0.5 boards only. |
| Temperature inputs | TEMP 0 | temp0 | |
| ^^ | TEMP 1 | temp1 | |
| ^^ | TEMP 2 | temp2 | |
| ^^ | TEMP 3 | temp3 | |
| Input/Outputs | IO_0 | io0.in | Endstops, Z probes, filament monitors etc (note io0 pins common with PanelDue header) |
| ^^ | ^^ | io0.out | ^^ |
| ^^ | IO_1 | io1.in | ^^ |
| ^^ | ^^ | io1.out | ^^ |
| ^^ | IO_2 | io2.in | ^^ |
| ^^ | ^^ | io2.out | ^^ |
| ^^ | IO_3 | io3.in | ^^ |
| ^^ | ^^ | io3.out | ^^ |
| ^^ | IO_4 | io4.in | ^^ |
| ^^ | ^^ | io4.out | ^^ |
| ^^ | IO_5 | io5.in | ^^ |
| ^^ | ^^ | io5.out | ^^ |
| ^^ | IO_6 | io6.in | ^^ |
| ^^ | ^^ | io6.out | ^^ |
| ^^ | IO_7 | io7.in | ^^ |
| ^^ | ^^ | io7.out | ^^ |
| ^^ | IO_8 | io8.in | ^^ |
| ^^ | ^^ | io8.out | ^^ |
| SPI CS | SPI_DB | spi.cs0 | Temperature daughterboard connector, for Thermocouple and PT100 boards, Accelerometer etc |
| ^^ | ^^ | spi.cs1 | ^^ |
| ^^ | ^^ | spi.cs2 | ^^ |
| ^^ | ^^ | spi.cs3 | ^^ |
| PanelDue | PanelDue | spi.cs4 | External SD card CS, note Io0 pins shared with io0 header.|
| ^^ | ^^ | io0.in | ^^ |
| ^^ | ^^ | io0.out | ^^ |
| Miscellaneous | EXT 5V | pson | For controlling an external PSU or SSR |
| ^^ | DOTSTAR | led | For controlling DotStar or NeoPixel LEDs (firmware 3.5.0-beta.4 and later only)

## Input/Output

### OUT headers

OUT_0 to OUT_9 are all PWM-capable. OUT_9 is shared with LASER/VFD. See tables above for notes on voltage selection and current limits.

### IO headers

There are 9 IO headers on board. Each has pins for input, output, 3.3V, 5V and Gnd. This enables support for a wide range of endstops, probes, filament monitors and future low bandwidth devices. RepRapFirmware 3 can be configured to map these ports to the appropriate functions as required.

![duet_3_mb6hc_input_output.jpg](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_input_output.jpg =282x)

Except as noted in the table below, an IO_x_IN pin can always be used to provide a digital input (e.g. for endstop inputs or filament monitors), and an IO_x_OUT pin can always be used to provide a digital output.

Additionally:
* IO output pins can be used as inputs, but are only 3.3V tolerant. When using a io_out pin as an input, you need to either enable the pullup resistor using the ^ character at the start of the port name, or use an external pullup resistor to +3.3V.
* IO input pins can be used as outputs, but have 10K protection resistors in series with them, so you would need to bypass these to use them as outputs. Warning: it can be easy to damage the board irreparably doing this, and modifying your board will invalidate the warranty.

## Tabs {.tabset}

### Duet 3 Mainboard 6HC v1.02

On the version 1.02 boards the individual IO_x connectors have the following additional capabilities:

| IO # | UART? | Analog in? | PWM out? | Notes |
|:---|:---|:---|:---|:---|
| 0 | yes | no | no |  |
| 1 | yes | no | no |  |
| 2 | no | no | no | A jumper is provided to bypass the 10K input protection resistor to allow I2C to be used on this port.</br>**Note:** RepRapFirmware does not currently support I2C on Duet 3 boards. |
| 3 | no | yes | no |  |
| 4 | no | yes | yes |  |
| 5 | no | yes | yes |  |
| 6 | no | yes | no |  |
| 7 | no | yes | yes |  |
| 8 | no | no | no |  |

### Duet 3 Mainboard 6HC v0.6 and later

On the version 0.6, 1.0 and 1.01 boards the individual IO_x connectors have the following additional capabilities:

| IO # | UART? | Analog in? | PWM out? | Notes |
|:---|:---|:---|:---|:---|
| 0 | yes | no | no |  |
| 1 | yes | no | no |  |
| 2 | no | no | no | Could in principle be used for I2C if a resistor is changed.</br>**Note:** RepRapFirmware does not currently support I2C on Duet 3 boards. |
| 3 | no | yes | no |  |
| 4 | no | yes | yes |  |
| 5 | no | yes | yes |  |
| 6 | no | yes | no |  |
| 7 | no | yes | yes |  |
| 8 | no | no | no |  |

### Duet 3 Mainboard 6HC v0.5 and earlier

On the Duet 3 Mainboard 6HC prototype v0.5 boards the capabilities are different:

| IO # | UART? | Analog in? | PWM out? | Notes |
|:---|:---|:---|:---|:---|
| 0 | no | yes | no |  |
| 1 | no | yes | no |  |
| 2 | no | yes | no |  |
| 3 | no | yes | no |  |
| 4 | no | yes | no |  |
| 5 | no | yes | no |  |
| 6 | no | yes | no |  |
| 7 | yes | no | no | In current firmware builds, dedicated to supporting a PanelDue, may not be used for any other purpose |
| 8 | no | no | yes |  |

## Power distribution

### VIN (Input voltage from PSU)

VIN in the range 12V-48V must be provided to the Duet (12V-32V before v1.02). In addition the same, or a different voltage can be provided specifically for the very high current OUT0 circuit. This allows for a heated bed or similar to be run from an alternative power supply. If that is not required the same VIN can be supplied to the OUT0 input.

> If you use a relay to control VIN power to the board, ie the power supply is already switched on, and a relay is used to turn on power to the board, you should use an inrush current limiter wired in series with VIN. See the [section on Inrush current here](https://docs.duet3d.com/en/User_manual/Connecting_hardware/Power_choosing#inrush-current){target=_blank}.
>
> OUT ports on the board should NOT be used to switch power to other boards directly. See the note at the end of the 'inrush current' section at the link above.  
{.is-info}

### 12V

The Duet 3 Mainboard 6HC produces 12V onboard from VIN. 12V will not be produced if only 12V is provided as VIN. 12V is provided to the low current (800ma) fan voltage selection pins, and can be assigned to two banks of pins, OUT 4-6 and/or OUT 7-9.

### 5V

The Duet 3 Mainboard 6HC also produces onboard 5V and 3.3V, from VIN. These voltages are divided for internal and external use, with external 3.3V and 5V going to IO and other headers.


### Tabs {.tabset}

#### v1.02

5V can be provided to the board through one of four ways:
- the 5V internal regulator, derived from 12V, (which itself is derived from VIN)
- supplied via the EX_5V header
- supplied via the USB connection
- supplied through the 5V_SBC pins on the SBC header

In normal operation the USB connection is not expected to be used for 5V supply, and the 5V internal regulator is the default source of 5V. Optionally 5V from either the 5V_EXT header or 5V_SBC pins can be used. This is selected using the 5V Select jumper:

![the 5V selection jumper of the Duet 3 Mainboard 6HC, showing the 5V_EXT pin and the 5V_SBC pin](/duet_boards/duet_3_mb6hc/duet3_mb_6hc_v1.02_5v_select.png =200x)

##### 5V via the external 5V header.
Put a 2 pin jumper in the position shown and connect 5V to the external 5V header (three pin molex immediately above the 5V select jumper)

![duet3_mb_6hc_v1.02_5v_select_5v_ext.png](/duet_boards/duet_3_mb6hc/duet3_mb_6hc_v1.02_5v_select_5v_ext.png =200x)

##### 5V via the 5V_SBCpins on the SBC header.
Put a 2 pin jumper in the position shown. 5V wall be drawn from to the 5V_SBC pins on the 26 pin SBC header. Note these are protected by diodes so 5V cannot be supplied to the SBC in this way.

![duet3_mb_6hc_v1.02_5v_select_5v_sbc.png](/duet_boards/duet_3_mb6hc/duet3_mb_6hc_v1.02_5v_select_5v_sbc.png =200x)

Ensure the SBC's 5V power budget is sufficient for the Duet and do not have high current 5V devices (such as lots of LEDs) supplied this way.

#### v1.01a

Previous versions of the Duet 3 Mainboard 6HC allowed for the Duet to power the SBC, or the SBC to power the Duet. More modern SBCs (e.g. RPi 4) needs too much 5V power, especially with a screen, to make it sensible to supply from the Duet. Similarly the spare 5V power budget on the SBC may not be sufficient for the Duet. In addition some SBCs require >5V on the 5V rail to not give an under voltage warning.

Due to this, the jumpers for 5V power between the Duet and SBC ('5V -> SBC' and 'SBC -> 5V*') are not populated on this board revision.

#### v1.01, v1.0, v0.6

External 5V power can be provided to the board by removing the jumper on 'Int 5V EN' and supplying 5V power to the 'EXT 5V' connector.

5V produced by the internal regulator can also provide power to a connected SBC (Single Board Computer, eg Raspberry Pi), via protection that normally prevents the SBC from backfeeding the Duet. There is an array of jumpers to customise the 5V power setup as required:

![duet_3_mb6hc_5v_options_01.jpg](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_5v_options_01.jpg =200x)

![duet_3_mb6hc_5v_options_02.jpg](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_5v_options_02.png)
The default configuration has jumpers on the "Int 5V EN" (Internal 5V enable) and "5V -> SBC" pins only. This means the internal 5V is enabled and the SBC is powered by the Duet's  5V. The Duet can supply up to 3.0A to the SBC.

![duet_3_mb6hc_5v_options_02.jpg](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_5v_options_03.png)
Alternatively, the SBC can provide 5V for the Duet using the "SBC -> 5V" jumper. Note that the Duet's in-built protection is bypassed. In this case the "5V->SBC" and the "SBC->5V*" jumpers should both be fitted, but remove the jumper from "Int 5V EN".

![duet_3_mb6hc_5v_options_02.jpg](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_5v_options_04.png)

If you wish to power the Duet and SBC separately, fit just one jumper, to "Int 5V EN". The Duet will be powered by it's internal 5V regulator and the SBC from it's own 5V power supply.

If you wish to power the Duet and SBC separately, both from external 5V power supplies, remove all the jumpers. Supply 5V power to the Duet via the 'EXT 5V' connector.

**Note:** No other jumper configuration is recommended or supported.

**Note:** Newer SBCs (e.g. RPi 4) need too much 5V power, especially with a screen, to make it sensible to supply from the Duet. Similarily the spare 5V power budget on the SBC may not be sufficient for the Duet. In addition some SBCs require >5V on the 5V rail to not give an under voltage warning.

## Electronics power consumption

### Tabs {.tabset}

#### v1.02

Power the SBC from its own 5V supply that is specifically designed for the purpose.The official Raspberry Pi Power Supply is a good example of this. Additionally, the USB cable used to power the Pi must be of good quality.

It is possible to power the Duet from the SBC is the Duet does not have higher current 5V devices connected (PanelDue, Neopixel/Dotstar LEDs) and the 5V power budget of the SBC power supply is not exceeded. See the 5V documentation above for how to set the selection jumpers.

The Pi may display a lightning bolt icon on the display (if connected) which is a good indication there is a power supply problem.


#### v1.01 and earlier Duet 6HCs:
Powering a SBC like the Pi3 from the Duet is feasible provided any USB peripherals attached to the Pi are very low power draw or have their own separate power supply. Higher power draw SBC like the Pi4 may draw more power than the internal 5v on the Duet can supply, especially with added peripherals. In general, for best results, it is recommended to power the Duet and the SBC separately to avoid power overdraw issues.

Furthermore, it is recommended to use a sufficient power supply for the Pi4 that is capable of providing 5a. The official Raspberry Pi Power Supply is a good example of this. Additionally, the USB cable used to power the Pi must be of good quality.

Power overdraw conditions may result in poor communication between the Pi and Duet. The Pi may display a lightning bolt icon on the display (if connected) which is a good indication there is a power supply problem.

## Network, user interface and plugins

The Duet 3 Mainboard 6HC has a dedicated high speed SPI bus to a single board computer (SBC). The reference implementation is a Raspberry Pi 3B or newer. The SBC provides the user interface (via a browser based control application called Duet Web Control, or a third party application). It also provides a network interface via the local network or VPN depending on how it is configured. It runs a set of applications called DuetSoftwareFramework(DSF) that use the SPI bus to communicate with the Duet, as well as providing a webserver for DuetWebControl, and API for third party application, and a plugin interface specifically for gcode processing plugins.

The Duet 3 Mainboard 6HC can also be run in standalone mode (without the SBC) by using the on-board SD card socket and Ethernet interface, but then the advanced facilities of DSF are not available.

### Ethernet

The Ethernet port provides the ability to directly network to the board when not using an SBC. Along with the built-in SD card this allows the Mainboard to run in Standalone mode.

### WIFI - v1.02

The Duet 3 Wifi module is only supported on 6HC v1.02 mainboards, running RRF 3.5b1 or later.

The Duet 3 6HC v1.02 has a header labelled "ESP" which accepts a Duet 3 Wifi Module
![Duet 3 6HC mainboard v1.02 with a wifi module fitted to the ESP header - view from above](/hardware/wifi_module/duet3_6hc_wifi_module_v0.1_fitted1.png =400x)

This module overlaps the SBC header and uses some of the same pins so it cannot be used at the same time as an SBC connection. 
![Duet 3 6HC mainboard v1.02 with a wifi module fitted to the ESP header - view from an angle](/hardware/wifi_module/duet3_6hc_wifi_module_v0.1_fitted2.png =400x)

#### Wifi Module Installation and Setup

Note that the wifi module is the second network interface on the 6HC (the ethernet interface is the first one) so all [M552](/User_manual/Reference/Gcodes/M552) commands need to contain "I1" to indicate they are for the wifi module interface. 

* With the 6HC powered off plug the WiFi module into the ESP header, as oriented in the image above and ensuring all the pins line up. 
* The WiFi module will need the firmware installed before configuration. The WiFi firmware is available from this [Duet3D Github repository](https://github.com/Duet3D/WiFiSocketServerRTOS/releases). Use the latest available release, at least v2.1beta3. Use the file named "DuetWiFiServer_32S3.bin".
  * If you have no network connection via Ethernet, copy the WiFi firmware binary file to the /firmware directory of the SD card on a PC. Replace the SD card in the Duet, turn on, then send `M997 S1` to install it on the WiFi module.
  * If you can connect to DWC via Ethernet, upload the firmware binary file by clicking on "Upload System Files" on the Files > System page. After upload, you should be asked if you want to install the WiFi firmware; select yes.
* Power up the 6HC and connect to it either via USB or Ethernet (see the [getting started guide](/User_manual/Overview/Getting_started_Duet_3_MB6HC) for how to get to that point. 

* Send `M552 I1 S0` to take the WiFi module out of its disabled state and into idle.

* Use [M587](/User_manual/Reference/Gcodes/M587) to add the SSID and password of your wifi network. Note, it does not need to be the same network that the ethernet is connected to. The WiFi interface will get its own IP address via DHCP, or you can specify what IP address to use in the M587 command.

* Send `M552 I1 S1` to enable the WiFi interface in client mode and connect to an access point that you specified using M587. Send `M552 I1` or `M122` to see the configured IP address.

* You can add this to config.g if you want WiFi to be enabled on boot.

## Motion

The Duet 3 Mainboard 6HC has 6 TMC 2160 or 5160 stepper drivers on board. Until further testing including detailed thermal analysis is conducted these are limited to 4.4A RMS (6.3A Peak). The [initial testing of the TMC5160s](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_3HC#thermal-tests) in this configuration, conducted on the Expansion board looks very promising. 4A per phase will allow driving stepper motors rated to up to 5A (it is recommended to drive stepper motors at around 80% of rated maximum current).

RepRapFirmware configures these drivers over the SPI bus: dynamic setting of microstepping, current and many other features can be achieved through firmware. The drivers can be combined together to use multiple drivers on one axis (for example a 3 motor Z levelling system).

Endstops of a variety of types can be connected to any of the 9 IO headers and mapped to a specific axis. One or more Z probes can be connected (for example a different Z probe per tool). In addition filament monitors for extruder drives are supported. 9 headers allows for an endstop on 3 motion axis, 3 z probes and 3 filament monitors on 3 extruder axis - or any other combination.

## Heating & Fans

The Duet 3 Mainboard 6HC has 10 PWM controlled outputs. These can be configured as heaters, fans or other devices that need a PWM signal (such as spindles or pumps). These are capable of different current outputs:

| Output | Rated current | Notes |
|:---|:---|:---|
| OUT0 | 15A | Designed for a heated bed or similar fused to 15A but rated to 18A |
| OUT1-3 | 6A | Designed for extruder heaters or similar |
| OUT4-6 | 2A | Select between VIN and 12V for this bank of 3 fans. Headers compatible with 4 wire fans, including tacho feedback |
| OUT7-9 | 2A | Select between VIN and 12V for this bank of 3 fans. Standard 2 wire fans |

Note: maximum current drawn by external devices at 12V must not exceed 800mA.

## CAN-FD Bus expansion

The CAN-FD bus provides connectivity to compatible devices. Duet3D manufacture a range of expansion devices. The maximum number of expansion devices on the bus depends on the firmware used; see [Firmware configuration limits](https://docs.duet3d.com/User_manual/RepRapFirmware/RepRapFirmware_overview#firmware-configuration-limits).

The CAN BUS is connected via RJ11 and at least 2-core twisted pair wiring. 6-core RJ11 wiring is more common, and can be used, though only one pair of wires are used.

The Duet 3 Mainboard 6HC has support for two CAN busses; CAN0 and CAN1. Currently (RRF v3.5 and earlier) only **CAN1** is used for connecting CAN-FD Duet 3 Expansion and Tool boards. CAN0 can be used for talking to non-RRF hardware that uses different protocols from that used by Duet 3 boards, including devices that talk plain CAN. Currently it is only used to configure motors for special kinematics.

For further information on CAN connectivity, see [CAN connection](/User_manual/Machine_configuration/CAN_connection)

### Bus termination

There is a 120R bus termination fitted to the CAN-FD bus on the 6HC boards, so normally this board must be at the end of the bus. From **v1.02 and later boards**, there are drill-to-disconnect jumpers that allow the termination resistor to be removed, however this is not required in normal operation. It is only required if a 6HC board is to be used as an expansion board and not placed at the end of the bus but somewhere in between.

### Removing the bus termination

On the back side of the board are 2 drill to disconnect jumpers per bus:
![6hc_can_termination_drillable.png](/duet_boards/duet_3_mb6hc/6hc_can_termination_drillable.png)

To disconnect the termination resistors, use a small drill bit ~2mm **by hand** to carefully remove the connection between the two pads that is made with the ring of the copper between the pads. **Do not drill all the way through the board**, the copper layer is approx 70um thick (i.e. very thin!) once its removed test that the pads are actually disconnected by checking that there is no continuity between them with a voltmeter.

Both jumpers must be disconnected or connected. do not do only one.

If in the future you want to add the termination resistor back into the circuit the jumper can be bridged with solder.



# Revision History

# Tabs{.tabset}

## Revision v1.02b
- Minor changes to USB host support circuit.
- Slik screen changes to aid clarity.

## Revision v1.02a
- Added a pull down resistor to keep the WiFi module (if present) disabled during board powerup
- improved the USB  impedance matching and trace routing. 
- Added USB-C test pads
- improved the CAN-FD termination resistor bypass solder jumper design to make it easier to use the 6HC as an expansion board.


## Revision v1.02
- Update the power and stepper driver circuitry to allow VIN of up to 48V.
- Due to the change in the onboard power supply circuitry the 5V supply available for external devices is now 800mA.
- Updated the external 5V input options to select between the 5V_EXT header and 5V_SBC
- Change to a USB C connector.
- Add a 2x6 header for an ESP wifi module - note this requires the module and firmware support to be completed.
- added a 2 pin Molex KK header for the second CAN-FD Bus (CAN0) to make it easier to access in the future.
- Added an ACT indication LED as on other Duet 3 boards. This signals CAN-FD bus activity.
- Added a 2x5 IDC header for a ribbon cable connection to a PanelDue with external SD card.
- Added a 2-pin jumper to connect Card Detect on the PanelDue to ground, to handle PanelDue versions that don't support Card Detect.
- Added a 2-pin jumper to bypass the 10k resistor on IO2.in, so it can be used for I2C.
- Improved ESD protection for temperature, tacho and other inputs.
- Various component changes to work around supply chain shortages (except those listed above this does not result in functional changes to the design).
- Move the location of the erase jumper.
- Added the termination resistors back to CAN0, provided drill to cut pads to disconnect the termination resistors for CAN0 and CAN1

## Revision v1.01a
- DNP the jumpers for 5V power between the Duet and the SBC. A more modern SBC (e.g. RPi 4) needs too much 5V power, especially with a screen, to make it sensible to supply from the Duet. Similarily the spare 5V power budget on the SBC may not be sufficient for the Duet. In addition some SBCs require >5V on the 5V rail to not give a under voltage warning.
- Minor component changes that do not impact functionality.
## Revision v1.01
- Improved ADC calibration
- Provide additional protection on the 5V_INT rail and 5V supply protection.
- Added buffers between the SBC and the Duet. 3.3V on the SBC is sensed on pin 17 of the GPIO header.
- Changed the labelling of the motor outputs from DRIVER_N_{A2 A1 B2 B1} to DRIVER_N_{A+ A- B+ B-}
- Minor changes to improve EMI
## Revision v1.0
- Added a 3 pin Molex KK header for Laser/VFD with a 5V buffered signal (out9) shared with the out9 output.
- Removed termination resistor from second CAN bus so the board does not have to be at the end of the second CAN Bus.
- minor changes to improve EMC performance
- minor changes to molex KK footprint outlines
## Revision v0.6
- Removed dedicated hobby servo connector as multiple IO ports can drive a hobby servo.
- Added a second CAN Bus tranceiver and connected it to PINs 2,5 of the RJ11 port
- Swap polarity of CANL and CANH signals to match expansion board. (Pin 3 for CANH pin 4 for CANL)
- Removed the second JST VH terminal from Driver 2 because the jumper method will not work as there are not jumper available for that pitch of connector.
- Added jumpers to allow 5V from or to the SBC.
- Added pull down resistors on STEP lines to prevent ghost stepping on startup.
- Connected Yellow LED on Ethernet connector

## Revision v0.5

Prototype board, no longer supported

