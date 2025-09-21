---
title: Duet 3 Mini 5+
description: Overview of Duet 3 Mini 5+ hardware features.
published: true
date: 2025-09-21T13:15:43.957Z
tags: 
editor: markdown
dateCreated: 2021-07-13T14:26:10.583Z
---

[![duet_3_mini_5+_wifi_top.jpg](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_wifi_top.jpg =400x)](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_wifi_top.jpg){target=_blank}

# Introduction

The Duet 3 Mini 5+ balances value and performance, focused on small and medium size machines that do not need the high power output of the Duet 3 Mainboard 6HC. It provides 5 on board drivers and the normal complement of heaters, fans, IO, LCD etc. There are Wifi and Ethernet variants, with the option of using an SBC (Single Board Computer, e.g. Raspberry Pi) for control in the same manner as other Duet 3 mainboards. It has a header for a two driver expansion board and a CAN-FD port for connecting Duet 3 expansion and tool boards.

Except where otherwise stated, the following applies to both the WiFi and Ethernet versions.

# Features

## Hardware Specification

The main hardware features of the Duet 3 Mini 5+ are listed below.

| HARDWARE SPECIFICATION |||
|:---|:---|
|  | **Duet 3 Mini 5+ WiFi** | **Duet 3 Mini 5+ Ethernet** |
| **Processor** | [ATSAME54P20A](https://www.microchip.com/wwwproducts/en/ATSAME54P20A){target=_blank} ||
| **Processor features** | 32-bit, 120MHz ARM Cortex M4F, 1Mb flash, 256Kb RAM, hardware floating point (single precision), DMA, 4Kb cache ||
| **Networking/Comms** | 2.4GHz WiFi; USB port; CAN-FD bus | 10BaseT/100BaseTX Ethernet; USB port; serial port; CAN-FD bus |
| **On-board stepper drivers** | 5 x [TMC2209](https://www.trinamic.com/products/integrated-circuits/details/tmc2209-la/){target=_blank} ||
| **Stepper driver features** | Up to 2.0A peak current, microstep interpolation from any setting to x256, stall detection, stealthChop2 ||
| **High current outputs** | 1 x 15A, 2 x 5A each ||
| **Medium current outputs** | 4 x PWM-controlled outputs, of which 2 support tacho input. Voltage selectable between VIN and 12V in 2 banks ||
| **Thermistor/PT1000 inputs** | 3 x inputs, optimised for 100K thermistors and PT1000 sensors ||
| **Inputs/Outputs** | 5 x on-board I/O connectors plus 2 x input-only connectors for endstop, filament monitor, Z probe, hobby servo or PanelDue connection. Inputs are 30V-tolerant. Also one output with 5V signal level for hobby servo, laser control or VFD. Two I/O connectors have serial UART support. ||
| **Power monitoring** | VIN voltage monitoring allows for the state to be saved on power failure. ||
| **SD card interface** | On-board high speed SD card socket. ||


| EXPANSION ||
|:---|:---|
| **Support for attached Raspberry Pi or other Single Board Computer (SBC)** | Yes ||
| **External stepper driver support** | 2 x external stepper drivers from stepper driver expansion connector. Multiple expansion via the CAN-FD bus. ||
| **Stepper driver expansion** | 2 x additional TMC2209 stepper drivers using a Duet 3 Expansion Mini 2+. Further expansion via CAN-FD bus ||
| **PT100 and thermocouple daughterboard support** | Supports 1 x daughterboard (2 channels) on board. More via CAN-FD-connected expansion boards. ||
| **LCD support** | PanelDue colour touch screen, mini 12864 mono graphics display using ST7567 controller (3.3V signal levels) ||
| **LED strip support** | RGB Neopixel (max. 60 LEDs, external 5V power required) ||
| **Other expansion** | Via I/O ports and CAN-FD bus ||

## Operating limits

|:---|:---|
| **Stepper drivers** | Up to 2.0A peak current |
| **High current outputs** | OUT0 up to 15A. OUT1/2 up to 5A each |
| **Medium current outputs** | Up to 2A per output when powered from VIN. Up to 800mA total when powered from 12V |
| **Input power voltage** | 11V to 25V |
| **Input connector rated current** | 25A maximum, or fused limit (whichever is lower) |
| **Inputs/Outputs** | Inputs are 30V-tolerant |
| **Fuses** | 10A for V_FUSED, 15A for OUT0 (e.g. for a heated bed). |
| **5V current limit** | 1A total on 5V and 3.3v, including the internal current consumption (around 200-300mA), any PanelDue or other display, and any endstops/Z probes that draw significant power. |
| **12V current limit** | 800mA total (only used for outputs OUT_3 thru OUT_6, when selected) |
| **Maximum ambient temperature** | 70°C |

## Firmware notes

* Duet 3 Mini 5+ is compatible with RepRapFirmware version 3.2 and later. The Duet3 Mini 5+ Ethernet v1.03 requires v3.5 or later firmware.
* For **firmware update instructions**:
  * Standalone mode - [Installing and updating firmware](/User_manual/RepRapFirmware/Updating_firmware){target=_blank}. 
  * SBC mode - [SBC setup for Duet 3](/User_manual/Machine_configuration/SBC_setup){target=_blank}.
* There are some **limits on firmware and hardware configuration**. See:
  * [Firmware configuration limits](/User_manual/RepRapFirmware/RepRapFirmware_overview#firmware-configuration-limits){target=_blank}
  * [CAN expansion configuration limitations](/User_manual/RepRapFirmware/CAN_limitations){target=_blank}

## Open Source

* The Duet3D Mini5+ is Open Hardware, see [our license here](https://github.com/Duet3D/Duet3-Mini5plus/blob/main/LICENSE){target=_blank}
* All hardware [source files](https://github.com/Duet3D/Duet3-Mini5plus){target=_blank} are available on Github.
* Both the [Duet Web Control](https://github.com/Duet3D/DuetWebControl){target=_blank} web interface and [RepRapFirmware](https://github.com/Duet3D/RepRapFirmware){target=_blank} are [Open Source Software](http://www.gnu.org/licenses/gpl-3.0.en.html){target=_blank} with source files available and actively maintained, see [Contributing to development](/User_manual/Reference/Developers){target=_blank} for more information.
* The Duet hardware and RepRapFirmware are built with Open tools: designed in [KiCad](http://kicad.org/){target=_blank} and [Eclipse](https://eclipse.org/){target=_blank} using open tools means the barrier to getting involved is as low as possible.

## Feature Comparison

See the [Hardware overview](/Duet3D_hardware/Hardware_overview){target=_blank} page for a feature comparison table between different versions of the Duet.

# Physical properties

## Dimensions
## Tabs{.tabset}

### v1.03
Ethernet Only
[![duet3_mini5+_v1.03_d1.0_dimensions.png](/duet_boards/duet_3_mini_5_plus/duet3_mini5+_v1.03_d1.0_dimensions.png =500x)](/duet_boards/duet_3_mini_5_plus/duet3_mini5+_v1.03_d1.0_dimensions.png){target=_blank}

### v0.5-v1.02
[![duet_3_mini_5+_wifi_dimensions.png](/duet_boards/duet_3_mini_5_plus/duet3_mini5+_v0.5-v1.02_d1.1_dimensions.png =500x)](/duet_boards/duet_3_mini_5_plus/duet3_mini5+_v0.5-v1.02_d1.1_dimensions.png){target=_blank}

## Mounting

The form factor of the Duet 3 Mini 5+ is the same as the Duet 2 WiFi/Ethernet and can be mounted in much the same way, though the driver connectors are flipped sides compared to the Duet 2 WiFi/Ethernet.

## Cooling

In many applications passive cooling will be sufficient, especially if the board is mounted vertically in a well ventilated position. If active cooling is needed then a fan blowing across the back of the board along the line of the stepper drivers is recommended.

The stepper drivers' heatsinks are connected to the PCB and the majority of the heat is dissipated via the PCB so heatsinks on the stepper driver chips are largely ineffective.

### MCU Temp

Unfortunately the SAME54P20A chip used in the Duet 3 Mini 5+ does not have a functioning temperature sensor. In theory it does have an on-chip temperature sensor, but the errata document for the chip says it doesn't work. However, it has been enabled in RRF 3.3 as an experiment, and does appear to give useful readings on the samples tested.

### Stepper Driver Temp

Due to this you would be unable to monitor the MCU temp in order to control fans. The driver sensors do work for fan control, but only return flags of warning and overtemp,

## 3D Model

The STEP files for both boards are available [on Github here](https://github.com/Duet3D/Duet3-Mini5plus/tree/main/v1.01){target=_blank}.

# Physical connections

## Wiring diagram

## Tabs{.tabset}

### Revision v1.03
Ethernet only
[![An image showing the connections for the Duet 3 Mini 5+ v1.03 Ethernet](/duet_boards/duet_3_mini_5_plus/duet3_mini5+_v1.03_d1.0_wiring.png =x500)](/duet_boards/duet_3_mini_5_plus/duet3_mini5+_v1.03_d1.0_wiring.png){target=_blank}

### Revision v0.5 to v1.02

[![An image showing the connections for the Duet 3 Mini 5+ v0.5-v1.02 wifi and ethernet](/duet_boards/duet_3_mini_5_plus/duet3_mini5+_v0.5-v1.02_d1.5_wiring.png =x500)](/duet_boards/duet_3_mini_5_plus/duet3_mini5+_v0.5-v1.02_d1.5_wiring.png){target=_blank}

### Revision 0.4
<!--removed the picture, just have the link for this very old board to speed up page loading -->
[Duet 3 Mini 5+ v0.4 wiring diagram](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_wiring_v0.4.jpg){target=_blank}

#### Errata

* There is insufficient room to fit the PanelDue 2x5 pin box connector unless a small part of the connector housing is removed, because the processor intrudes into its footprint. All version 0.4 boards have been fitted either with a box connector with some of the plastic removed, or with a 2x5 pin header instead. If a pin header is fitted, the PanelDue ribbon cable must be mated with it such that the spigot on the ribbon cable connector faces away from the stepper drivers. Pin 1 of the ribbon cable (usually indicated by a red stripe) must face towards the power input connector as shown above.
* A pullup resistor was missing. All version 0.4 boards have been modified to include this resistor on the underside of the board.

<!--
Removed revision 0.2 section because they are no longer supported and had very limited distribution.

### Revision 0.2

Wiring diagram is similar to the v0.4

![10K pullup between RESET and 3.3V](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_wiring_v0.2.jpg =x400)

This prototype version of the Duet3 Mini 5+ had limited distribution

#### Errata

* The SCLK and MOSI signals on the PanelDue ribbon cable connector have 5V signal levels, however the PanelDue displays connect these signals directly to the SD card socket, therefore they should be 3.3V. **Do not use a SD card in the SD card socket of PanelDue.** Doing so may damage the card.
* The Reset pin on the Mini 12864 display connector is not connected. This means that unless the display has a pullup resistor on this pin, the display may remain in the reset state. To fix this you can connect a 10K resistor between the pin and +3.3V as shown above.
* The wiring between the WiFi module and main processor was incorrect. All prototype boards have been modified to fix this.
* The LED to indicate WiFi status is missing.
* Pin 17 on the SBC header is labelled SBC_5V in error it should read SBC_3.3V
 -->

## Description of Connections

| Duet 3 Mini 5+ Connectors |||
|:---|:---|---|
| **1 x 4-way barrier strip** | POWER IN, GND, VIN  | Two pins for main VIN and GND
| ^^ | OUT_0, V_OUT_0_OUT, OUT_0_NEG | Two pins for positive and negative OUT_0 terminals. OUT_0 is intended to drive a bed heater or other high current resistive load. If you connect an inductive load to this output, you must use a suitably rated external flyback diode. The ground side of OUT_0 is switched by the mosfet and the positive side is protected by a 15A fuse. |
| **2 x 2-pin JST VH connectors** | OUT_1, OUT_2 | Intended for extruder heaters or similar medium/high current resistive loads. The out1 and out2 pins are PWM switched GND, the V_FUSED pins are fused input voltage. Flyback diodes are built-in to these outputs. Maximum recommended current 6A each. |
| **2 x 4-pin KK connectors** | OUT_3, OUT_4 | Intended for PWM-controllable fans or other medium/low current loads. Flyback diodes are built-in to these outputs. The connector fits a standard PC-type 4-pin PWM fan. Alternatively, a 2-pin fan may be connected between the V_OULC1+ pin (+ve) and the OUT_n_NEG pin (-ve).
| ^^ | ^^ | **Note:** OUT_3 and OUT_4 are protected by a flyback diode to V_FUSED. This does not provide protection if driving these outputs from a higher voltage than V_FUSED |
| **1 x 3-pin Jumper** | OUT_3&4 Select V | The positive supply to the above connectors is the centre pin of the 3-pin jumper block. A jumper in the "left" position will power them from the fused VIN supply (max 2A each ). A jumper in the "right" position will power them from the onboard 12V regulator (subject to overall 12V supply current *see note 1 below*). |
| **2 x 2-pin KK connectors** | OUT_5, OUT_6 | These are intended for PWM-controllable fans or other medium/low current loads. Flyback diodes are built-in to these outputs. **Note** out_6 PWM pin is shared with LASER/VFD |
| ^^ | ^^ | **Note:** OUT_5 and OUT_6 are protected by a flyback diode to V_FUSED. This does not provide protection if driving these outputs from a higher voltage than V_FUSED |
| **1 x 3-pin Jumper** | OUT_5&6 Select V | The positive supply to the above connectors is the centre pin of the 3-pin jumper block. A jumper in the "left" position will power them from the fused VIN supply (max 2A each ). A jumper in the "right" position will power them from the onboard 12V regulator (subject to overall 12V supply current *see note 1 below*). |
| **5 x 4-pin KK connectors** | DRIVER_0, DRIVER_1, DRIVER_2, DRIVER_3, DRIVER_4 | Stepper motor connections, See "Connecting Stepper Motors" section below. |
| **1 x 16-pin socket strip** | EXTERNAL DRIVERS | Step, Direction, Enable, Diag and UART connections for DRIVER_5 and DRIVER_6, along with fused VIN, ground, 3.3V and 5V. Designed for a Mini 2+ expansion board to plug directly in. Step, direction and enable can also be used with other external drivers (3.3V signal level). |
| **1 x 2x5 IDC connector** | PanelDue_SD | Connects the [PanelDue](/Duet3D_hardware/Accessories/PanelDue) UART and shared SPI bus for external SD card. Powered from 5V supply (*see note 2*). **Note** shared with io0.in and io0.out pins on the IO_0 header. |
| **1 x 3-pin KK connectors** | LASER/VFD | 5V buffered output shared with out6, along with 5V and ground supply (*see note 2 below*). Provides a 5V PWM signal to drive hobby servos, and PWM->analog controls for VFDs or Lasers. **Note** out6 PWM pin is shared with OUT_6 |
| **1 x 2-pin KK connectors** | 12V | Always on 12V supply (*see note 1 below*) |
| **Network** | **Ethernet** | 1 x RJ45 100BaseT Port. *non MDIX* connect to an Ethernet switch, hub or MDIX enabled laptop port. If connecting to a non MDIX enabled port use a crossover cable. Orange LED on Ethernet port indicates Ethernet enabled, green LED indicates network activity |
| ^^ | **Wifi** | U.FL/IPEX push on connector for external antenna. *Note this connector is delicate, take care when plugging and unplugging*. "LED ESP": green LED indicates Wifi connection status |
| **Reset** |  | Single push to reset the board. Double push to put the board into UF2 bootloader upload mode. See [User manual: Updating firmware - Duet 3 Mini 5+ via USB](/User_manual/RepRapFirmware/Updating_firmware#duet-3-mini-5-wifiethernet){target=_blank} |
| **1 x JST ZH 6-pin connectors** | SWD | Connection for an SWD programming device such as an Atmel-ICE |
| **1 x 2-pin KK connectors** | CAN | CAN-FD Bus connection for Duet 3 CAN-FD expansion boards. |
| **5 x 5-pin KK connectors** | IO_0, IO_1, IO_2, IO_3, IO_4 | These are for endstop switches, Z probes, filament monitors and other low-voltage I/O functions. Each connector provides both 3.3V and 5V power. The inputs will tolerate up to 30V with 10K series resistors (but see below for bypass option). The outputs are 3.3V signal levels with 470R series resistors. IO_1,2,3 are PWM capable. |
| **2 x 2-pin Jumpers 10K->470R bypass** | IO2.in, IO3.in | v1.01 and later only. Jumpers to allow the 10K resistors on IO2.in and IO3.in to be bypassed with 470R resistors. This is required to use IO2 or IO3 for I2C. **Note:** RepRapFirmware does not currently support I2C on Duet 3 boards. |
| **2 x 3-pin KK connectors** | IO_5, IO_6 | Input only IO connections that will  tolerate up to 30V with 10K series resistors. Perfect for simple endstop switches.|
| **3 x 2-pin KK connectors** | TEMP_0, TEMP_1, TEMP_2 | Connections for thermistor or PT1000 sensors. |
| **1 x 2x13 IDC connector** | SBC | Connections to a Single Board Computer (SBC) such as a Raspberry Pi. |
| **1 x 2x5 IDC connector** | SPI DB | *(Connector named TEMPDB before v1.03)* <br> For connecting a [PT100](/Duet3D_hardware/Accessories/PT100_Temperature_Daughterboard){target=_blank}, [thermocouple](/Duet3D_hardware/Accessories/Thermocouple_Daughterboard){target=_blank} or [Duet3D Accelerometer](/Duet3D_hardware/Accessories/Duet3D_Accelerometer){target=_blank} interface board. **Note** on v1.02 and earlier boards cannot be stacked so only 1 board at a time is supported. |
| **2 x 2-pin Jumper2** | SPI3_SELECT<br>SPI4_SELECT| *Version 1.03 and later*: only. Route the spi.cs3 and spi.cs4 signals to the SPI DB connector instead of the 12864 display EXT1 connector |
| **1 x 3-pin Jumper** | 5V_SELECT | *v0.5-v1.02* Source of optional External 5V input, see note 2 below |
| **2 x 3-pin Jumpers** | 5V_SELECT |*Version 1.03 and later*: Source of optional External 5V input, see note 2 below |
| **1 x 2-pin Jumper** | Int_5V_Disable | Connect a jumper across this to disable the internal 5V regulator, see note 2 below |
| **2 x 2x5 IDCs** | 12864_EXP1, 12864_EXP2 | Headers for connecting a 12864 display using  a ST7567 controller, see "Connecting a 12864 display" below. |
| **1 x 3-pin KK connector** | NP_LED | This is to connect and power NeoPixel LED strips (DotStar LED strips are not supported) . Connect the  DO pin to Neopixel DI. External 5V must be supplied to the "EXT 5V" header to power the NeoPixel array, they cannot be powered from the onboard regulator. |
| **1 x 3-pin KK connector** | EXT 5V | Input for External 5V supply, see Note 2 below. There is a buffered 5V "pson" pin which can be used to switch an external supply, note it is shared with io4.out |

**Notes**

1. Total 12V load should not exceed 800mA
1. 5V can be powered from multiple inputs (USB, External 5V input, SBC) as well as the internal 5V regulator. Total 5V load should not exceed 800mA when powered from the internal 5V regulator. see the "Power distribution, 5V" section below for more information

## LED indications

LEDs are provided to indicate the following:

| Label | Colour | Function |
|--|--|--|
| **ACT** | Green | Indicates activity on the CAN-FD bus |
| **STATUS** | Red | See description below |
| **V_FUSED** | Blue | Indicates fused VIN supply present |
| **12V+** | Amber | Indicates indicates on-board 12V regulator operating |
| **5V+** | Red | Indicates indicates 5V supply present |
| **3.3V+** | Green | Indicates on-board 3.3V regulator operating |
| **ESP** | Green | WiFi version only. Indicates WiFi activity; flashing for searching/connecting, on for connected. |
| **OUT_0** | Red | Next to the OUT 0 connector, indicates when on |
| **OUT_1** | Red | Next to the OUT 1 connector, indicates when on |
| **OUT_2** | Red | Next to the OUT 2 connector, indicates when on |

The red LED next to the Reset button is labelled "STATUS".  *On version 0.2 boards it is labelled DIAG*. It indicates the state of the board, as follows.

| LED | Meaning |
|:---|:---|
| Flashing steadily, about half a second off and half a second on | Normal operation, RepRapFirmware is running |
| Flashing three times, then off for a while | Firmware CRC check failed |
| Fading from bright to dim and then back again | USB bootloader activated |


## Pin names

For more information on pin names, see [Pin Names](https://docs.duet3d.com/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank}.

RepRapFirmware 3 uses pin names for user-accessible pins, rather than pin numbers, to communicate with individual pins on the PCB. In RRF 3 no user-accessible pins are defined at startup by default. Pins can be defined for use by a number of gcode commands, e.g. M574, M558, M950.

The Duet 3 series uses the pin name format "expansion-board-address.pin-name" to identify pins on expansion board, where *expansion-board-address* is the numeric CAN address of the board. A pin name that does not start with a sequence of decimal digits followed by a period, or that starts with "0." refers to a pin on the Duet 3 Mini 5+.

| Pin location | RRF3 Pin name | Notes |
|---|---|---|
| **Heater outputs** |||
| OUT_0 | out0 | High current output, bed heater |
| OUT_1 | out1 | Medium current outputs, hot ends |
| OUT_2 | out2 | |
| **Outputs (4-pin)** |||
| OUT_3 | out3 | 4-wire fans with tacho|
| ^^ | out3.tach | ^^ |
| OUT_4 | out4 | ^^ |
| ^^ | out4.tach | ^^ |
| **Outputs (2-pin)** |||
| OUT_5 | out5 | |
| OUT_6 | out6, laser, vfd | Pin shared with OUT 6 and LASER/VFD connectors |
| **Temperature inputs** |||
| TEMP_0 | temp0 | |

| IO_1 | io1.in | endstops, Z probes, filament monitors etc |
| ^^ | io1.out | ^^ |
| IO_2 | io2.in | ^^ |
| ^^ | io2.out | ^^ |
| IO_3 | io3.in | ^^ |
| ^^ | io3.out, 12864 neopixel pin | ^^ |
| IO_4 | io4.in | ^^ |
| ^^ | io4.out, pson | ^^ |
| IO_5 | io5.in | Input only |
| IO_6 | io6.in | ^^ |
| **SPI CS** |||
| SPI DB | spi.cs1 | *(Connector named TEMPDB before v1.03)* Thermocouple or PT100 daughterboard |
| ^^ | spi.cs2 | ^^ |
| ^^ | spi.cs3 | v1.03 and later only, shared with lcd.cs3 Jumper selectable |
| ^^ | spi.cs4 | v1.03 and later only, shared with lcd.a0 Jumper selectable|
| **Miscellaneous** |||
| LASER/VFD | laser, vfd, out6 | Pin shared with OUT 6 and LASER/VFD connectors |
| EXT 5V | pson, io4.out | For controlling an external PSU or SSR, shared with io4.out |
| NEOPIXEL | led | For controlling Neopixel LEDs (firmware 3.5 and later only)
| 12864_EXP1 | lcd.a0,exp1.7 | (RRF 3.5 and later only) Pin 7 of the EXP1 connector. Available for general output if no 12864 display is connected.<br> Shared with spi.cs4 on SPI DB header on v1.03 and later.
| ^^ | enc.sw,exp1.9 | (RRF 3.5.0-rc.1 and later) Pin 9 of the EXP1 connector. Available for general output if no 12864 display is connected.
| ^^ | lcd.buzz,exp1.10 | (RRF 3.5.0-rc.1 and later only) Pin 10 of the EXP1 connector. Available for general output including PWM if no 12864 display is connected.
| 12864_EXP2 | enc.b,exp2.6 | (RRF 3.5 and later) Pin 6 of the EXP2 connector. Available for general input (max 3.3V) and output if no 12864 display is connected. There is no protection on this pin.
| ^^ | enc.a,exp2.8 | (RRF 3.5 and later) Pin 8 of the EXP2 connector. Available for general input (max 3.3V) and output if no 12864 display is connected. There is no protection on this pin.

## Input/Output

### OUT headers

OUT_0 to OUT_6 are all PWM-capable. OUT_6 is shared with LASER/VFD. See tables above for notes on voltage selection and current limits.

### IO headers

There are 7 IO headers on board. IO_0 to IO_4 have pins for input, output, 3.3V, 5V and Gnd supplied. IO_5 and IO_6 have an input, 3.3V and Gnd supplied. This enables support for a wide range of endstops, probes, filament monitors and future low bandwidth devices. RepRapFirmware 3 can be configured to map these ports to the appropriate functions as required.

[![duet_3_mb6hc_input_output.jpg](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_input_output.jpg =282x)](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_input_output.jpg){target=_blank}

Except as noted in the table below, an IO_x_IN pin can always be used to provide a digital input (e.g. for endstop inputs or filament monitors), and an IO_x_OUT pin can always be used to provide a digital output.

Additionally:
* IO output pins can be used as inputs, but are only 3.3V tolerant. When using a io_out pin as an input, you need to either enable the pullup resistor using the ^ character at the start of the port name, or use an external pullup resistor to +3.3V. On the Mini5+, output pins can't generate interrupts, so will work as inputs for some uses only (e.g. endstops).
* IO input pins can be used as outputs, but have 10K protection resistors in series with them, so you would need to bypass these to use them as outputs. Warning: it can be easy to damage the board irreparably doing this, and modifying your board will invalidate the warranty.
* On board revision v1.01 and later only, jumpers allow the 10K resistors on IO2.in and IO3.in to be bypassed with 470R resistors. This is required to use IO2 or IO3 for I2C.
**Note:** RepRapFirmware does not currently support I2C on Duet 3 boards.

The individual IO_x connectors have the following capabilities:

| IO # | UART? | Analog in? | PWM out? | Notes |
|:---|:---|
| 0 | yes | no | no | AUX0 port, can be used to connect a PanelDue. Configure using M575 P1. Shares io0.out and io0.in pins with PanelDue_SD connector. |
| 1 | yes | no | yes | AUX1 port. Configure using M575 P2. |
| 2 | yes | no | yes | The  standard firmware does not support this UART |
| 3 | no | yes | yes | Shared with  backlight control on 12864 displays having Neopixel backlights |
| 4 | no | no | no | IO4_OUT is shared with PSON output |
| 5 | no | no | n/a | 3-pin connector, input only |
| 6 | no | yes | n/a | 3-pin connector, input only |

## Power distribution

### VIN (Input voltage from PSU)

Supply 12V-24V input power (11-25V absolute minimum/maximum) between the GND and VIN terminals.

VIN is split via two fuses:
* V_FUSED: 10A :  All on board power demands, other than OUT0 (Normally used for heated bed
* OUT0_FUSE: 15A: Directly to the V_OUT0 terminal

V_FUSED is distributed across the board as follows:

* 12V Regulator
* 5V regulator
* Stepper drivers (including external driver header)
* OUT 1 and OUT 2 headers
* V_OUTLC1 and V_OUTLC2 selection jumpers

> If you use a relay to control VIN power to the board, i.e. the power supply is already switched on, and a relay is used to turn on power to the board, you should use an inrush current limiter wired in series with VIN. See the [section on Inrush current here](https://docs.duet3d.com/en/User_manual/Connecting_hardware/Power_choosing#inrush-current){target=_blank}.
>
> OUT ports on the board should NOT be used to switch power to other boards directly. See the note at the end of the 'inrush current' section at the link above.  
{.is-info}

### 12V

**12V_EXT**: 800mA limit, Supplied to:
  * V_OUTLC1/2 for OUT_3 thru OUT_6.
  * 12V header (useful for supplying 12V to a VFD controlled from the adjacent 5V PWM header)

### 5V

### Tabs{.tabset}

#### Revision v1.03
Ethernet only

**5V**: Split up to 5V_EXT, 5V_INT. Those points can also be supplied by USB (i.e. VBUS) and from the 5V SELECT jumpers that allow selection from 5V_EXT_INPUT and 5V_SBC.
* **5V_EXT feeds**:
  * IO headers
  * 12864 display and PanelDue
  * LASER/VFD header
* **5V_INT feeds**:
  * Internal and External 3.3V regulators
  * External Driver header
  * Internal 5V logic

5V input can come from one of these sources:

* **Onboard 5V regulator:** Once 3.3V and other onboard demands are met approximately 700mA remains for use on the 5V_EXT rail. No jumpers required on 5V_EXT_INPUT, 5V_SBC or Int_5V_Disable.
* **USB:** Can supply both 5V_INT and 5V_EXT. Limits based on USB specification. No jumpers required on 5V_EXT_INPUT and 5V_SBC or Int_5V_Disable.
* **5V_EXT_IN**: Put a jumper on 5V_EXT_INPUT, add jumper to Int_5V_Disable, to disable the onboard 5V regulator. With the 5V_EXT_INPUT jumper set to this position, 5V is passed through to both 5V_EXT and 5V_INT from the EXT_5V header. The EXT_5V header also has a pin for controlling an external power supply (note signal shared with io4.out). This allows for the board to be powered from 5V, with an external supply for VIN turned on and off as required. 
* **5V_SBC:** In some, limited, cases it may be desirable to power the board from the 5V output of a SBC connected to the SBC header. Put a jumper 5V_SBC, and put another jumper on 'Internal 5V disable'. Note that the total power of the Duet+ peripherals must be factored into the SBC power budget. Also note that powering the SBC from the Duet is not supported.
* **5V_EXT_IN** and **5V_SBC:**: With both jumpers fitted and suitable supply connected to 5V_EXT the external 5V supply can also supply the SBC. Note that the total 5V current draw on 5V_EXT should be no more than 2A. This includes any neopixels, the Duet5V power consumption and anything supplied to the SBC. Ensure you put a jumper on 'Internal 5V disable' as well.

#### Revision v0.5-v1.02
**5V**: Split up to 5V_EXT, 5V_INT. Those points can also be supplied by USB (i.e. VBUS) and from the 5V_SELECT jumper that selects between 5V_EXT_INPUT and 5V_SBC. 
* **5V_EXT feeds**:
  * IO headers
  * 12864 display and PanelDue
  * LASER/VFD header
* **5V_INT feeds**:
  * Internal and External 3.3V regulators
  * External Driver header
  * Internal 5V logic

5V input can come from one of these sources:

* **Onboard 5V regulator:** Once 3.3V and other onboard demands are met approximately 700mA remains for use on the 5V_EXT rail. No jumpers required on 5V_SELECT or Int_5V_Disable.
* **USB:** Can supply both 5V_INT and 5V_EXT. Limits based on USB specification. No jumpers required on 5V_SELECT or Int_5V_Disable.
* **5V_EXT_IN**: Put a jumper on 5V_SELECT pins between 5V_COM and 5V_EXT_IN. When using EXT_5V, add jumper to Int_5V_Disable, to disable the onboard 5V regulator. With the 5V_SELECT jumper set to this position, 5V is passed through to both 5V_EXT and 5V_INT from the EXT_5V header. The EXT_5V header also has a pin for controlling an external power supply (note signal shared with io4.out). This allows for the board to be powered from 5V, with an external supply for VIN turned on and off as required. 
* **5V_SBC:** In some, limited, cases it may be desirable to power the board from the 5V output of a SBC connected to the SBC header. Put a jumper on the 5V SELECT pins between '5V_SBC' and '5V_COM', and put another jumper on 'Internal 5V disable'. Note that the total power of the Duet+ peripherals must be factored into the SBC power budget. Also note that powering the SBC from the Duet is not supported.

### 3.3V

**3.3V**: Internal 3.3V supply for onboard logic, Temperature Daughterboards and external drivers
* **3.3V_EXT**:
  * IO Headers
  * External SD cards


## Connecting Displays

### Connecting a 12864 display

The signal levels are mostly 3.3V. This means that LCD modules using a ST7920 display powered from 5V (e.g. the RepRapDiscount display) will **not** work reliably. Use a display with a ST7567 controller instead. The display we test with is the Fysetc Mini 12864 display version 2.1. You can connect this directly to the EXP1 and EXP2 connectors using the straight-through ribbon cables normally supplied with this display. Enable the display using these commands in config.g:

```
M918 P2                     ; enable ST7567-based 12864 display
M150 X2 R255 U255 B255 S3   ; set all 3 LEDs to white
```

You can adjust the M150 command to change the backlight colours. Please note, interrupts are disabled for a short time while the backlight colours are changed, and printing will be paused during this time; therefore you should not change the backlight colours during a print.

Note: (v1.03 only) the 12864 display cannot be used in conjunction with a second SPI DB as pins are shared.

### Connecting a PanelDue

A [PanelDue](/Duet3D_hardware/Accessories/PanelDue){target=_blank} can be connected to IO_0 using a 4-wire cable. See [Connecting a PanelDue](https://docs.duet3d.com/User_manual/Connecting_hardware/Display_PanelDue#option-1-4-way-cable){target=_blank}

Alternatively, it can be connected using a ribbon cable plugged into the socket labelled PanelDue_SD, which enables access to the PanelDue SD card socket. However, there are some caveats:
* The PanelDue connector shares the io0.out and io0.in pins, so IO_0 cannot be used at the same time.
* Duet 3 boards support the card detect signal. Newer versions of the PanelDue 5i and 7i (v1.01 and later of the 5i and v2.01 and later of the 7i) provide this signal, but older versions will need to be modified. See [Connecting a PanelDue](/User_manual/Connecting_hardware/Display_PanelDue#card-detect-signal){target=_blank}

## Connecting a Raspberry Pi

The Duet 3 Mini+ cannot power the Pi. You must power the Pi separately. The Raspberry Pi is sensitive to the input voltage, and many smartphone chargers or other USB power supplies cannot supply sufficient voltage. Therefore, we strongly recommend that you use the official Raspberry Pi PSU, or another PSU specifically designed to power a Raspberry Pi. If the red LED on the Pi is not continuously illuminated, the power supply is insufficient.

In other respects, SBC connection to the Duet and configuration is the same for the Duet 3 Mini  as for the Duet 3 Mainboard 6HC. See [SBC Setup for Duet 3](/User_manual/Machine_configuration/SBC_setup){target=_blank}.

When using an attached Raspberry Pi or other SBC, the WiFi or Ethernet interface on the Duet 3 Mini+ is disabled.

## Connecting Stepper Motors

See [Connecting stepper motors](/User_manual/Connecting_hardware/Motors_connecting){target=_blank}.

## Connecting Fans

The maximum current that fans can draw depends on the fan voltage, and how it is being powered.
* VIN-powered fans are only limited by the connectors, which are rated at 2A.
* Internally supplied 12V-powered fans are limited by a total current of 800mA, as that is what the 12V regulator can supply.
* You can connect 5V fans to the internal 5V regulator by either bridging from a 5V pin to the centre pin of OUT_3&4 Select V or OUT_5&6 Select V (which will supply both connected fan headers with 5V), or supplying 5V directly to the fan and using the fan#- pin to control it). Internally supplied 5V-powered fans are limited by a total current of 1A (which is also used to supply power to other components, see Operating limits), as that is what the 5V regulator can supply.
* You can use an external voltage regulator, and either supply the fans directly or using the centre pin of OUT_3&4 Select V or OUT_5&6 Select V. Current is limited by the connectors, which are rated at 2A.

See also [Connecting and configuring fans](/User_manual/Connecting_hardware/Fans_connecting){target=_blank}.

## CAN-FD Bus expansion

See [CAN connection basics](/User_manual/Machine_configuration/CAN_connection){target=_blank}.

The CAN-FD bus provides connectivity to compatible devices. Duet3D manufactures a range of expansion devices. 

The CAN-FD bus is connected via a 2-pin KK connector. 2-core twisted pair wiring is recommended.

### Bus termination

There are two 60R bus termination resistors fitted to the CAN-FD bus on Mini 5+ boards, so normally this board must be at the end of the CAN bus.

### Removing the CAN bus termination

If the board needs to be on the CAN bus between other boards, the CAN bus termination resistors should be disconnected or removed. This is NOT required in normal operation. It is only required if a Mini 5+ board is to be used as an expansion board and not placed at the end of the bus but somewhere in between.

#### Board revision 1.03 and later

From board revision 1.03 and later, there are two drill-to-disconnect jumpers that allow the termination resistor to be disconnected.

To disconnect the termination resistors, use a small drill bit ~2mm by hand to carefully remove the connection between the two pads that is made with the ring of the copper between the pads. Do not drill all the way through the board, the copper layer is approx 70um thick (i.e. very thin!) once its removed test that the pads are actually disconnected by checking that there is no continuity between them with a voltmeter.

Both jumpers must be disconnected or connected. Do not do only one.

If in the future you want to add the termination resistor back into the circuit the jumper can be bridged with solder.

#### Earlier boards

[![duet_3_mini_5+_can_termination_01.png](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_can_termination_01.png =300x){.align-right}](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_can_termination_01.png){target=_blank} Board revisions earlier than v1.03 will require you to desolder the two termination resistors. You need to remove both resistors, marked on the PCB silkscreen as R96 and R97, located adjacent to the CAN pins. 
*See image on right, resistors highlighted red. Click image for larger version.*

Both resistors must be removed to disable the CAN termination. Do not do only one.

To remove a 0402 component such as one of those resistors, we recommend using a soldering iron with a 45 degree bit just large enough to cover both pads of it. Put fresh solder on the bit and then apply it to the component for several seconds. Once the solder under the resistor has melted, the resistor will stick to the bit and can be lifted off.

**NOTE:** Removing these termination resistors will void the warranty!

<p style="clear:both"></p>

# Programming the bootloader

The bootloader is in protected memory and should not need to be reprogrammed. If you do need to reprogram it, you will need a Windows PC running Atmel Studio, and a SWD programming tool. Suitable tools, in order of increasing price, include:

* SEGGER J-Link EDU Mini (for non-commercial use only)
* Atmel ICE Basic
* SEGGER J-Link BASE

If using the Segger tool, see [this Adafruit page](https://learn.adafruit.com/how-to-program-samd-bootloaders/overview){target=_blank} for an overview of the process.

To connect the programmer to the Duet, you will need a SWD cable breakout board such as [this one](https://www.adafruit.com/product/2743){target=_blank} and a 6-pin JST ZH cable.

You will need to remove the bootloader protection by doing Erase Chip first and then setting USER_WORD_0.NVMCTRL_BOOTPROT to zero. After that you can program the bootloader binary into memory.

# Revision History

# Tabs{.tabset}

## Revision 1.03a

Ethernet Only

* Replaced the SD card filer/protection component that was no longer available with a functionally equivalent part. This will not change SD card perfomance or other functionality.

## Revision 1.03

Ethernet Only

* Changed USB connector to USB-C
* Split the 5V_EXT_INPUT and 5V_SBC selection jumpers to allow the 5V_SBC to be powered from 5V_EXT.
* Added drillable jumpers to allow the CAN termination resistors to be disconnected to make it easier to use the mini5+ as an expansion board.
* Added option to connect two 12864 LCD pins to the SPI DB header to allow for a second SPI DB to be used if the 12864 display was not used.

## Revision 1.02(a)

WiFi revision is version 1.02, Ethernet revision is 1.02a

* (WiFi only) Changed to the BGA package for the processor. This is functionally identical to the TQFP package and will be reverted once supplies of the TQFP package become available.
* Added input protection diodes to the Tacho inputs
* Minor silkscreen tidying up

## Revision 1.01

* Added Jumpers to bypass 10K input protection resistors with 470R resistors for IO2.in and IO3.in. This is to allow these inputs to be used with I2C. **Note:** RepRapFirmware does not currently support I2C on Duet 3 boards.
* Add a 10K pulldown resistor between signal line io4_out and ground. This is to prevent PS_ON turning on momentarily when the board is powered up.

## Revision 1.0

* Component footprint and solder stencil changes to improve manufacturability
* Ground plane changes to further improve EMC performance
* Minor component changes to increase commonality with other Duet 3 products

## Revision 0.5

* Add 5V TVS diode footprint option so either 1610 or SMC can be used on 5V.
* Move OUT3 and OUT 4 connector up slightly to clear the stepper diver expansion header.
* Added pullup between Driver enable and 3.3.V
* Add 3.3V TVS diode
* Updated the 12V and 5V BUCK input circuits to improve EMC performance.
* Updated the passive components around the Ethernet Phy to improve EMC performance.
* Further minor routing and component updates.

## Revision 0.4

* Added CAN support
* Removed IO_4 (pins used for CAN) and renamed IO_5, 6,7 to 4,5,6.
* Remove 1 stepper driver and TMC diag mux. Used freed pins to route each stepper driver diag line directly to the MCU. Significantly improves stall detection performance.
* Swap 4 wire fan pin headers for normal molex KK
* 3 way jumper to choose between External 5V and 5V_SBC
* Swap the pinout for IO6 and IO7 to be the same as Duet 2
* Rename DIAG LED to STATUS
* Further minor routing and component updates.

## Revision 0.2

First prototype, this revision will not be supported in future firmware releases.

### Revision 0.2 IO port pin capabilities

| IO # | UART? | Analog in? | PWM out? | Notes |
|:---|:---|
| 0 | yes | no | no | Can be used to connect a PanelDue |
| 1 | yes | no | yes |  |
| 2 | yes | no | no | The  standard firmware does not support this UART |
| 3 | no | yes | yes |  |
| 4 | no | no | yes |  |
| 5 | no | no | no | IO5_OUT is shared with PSON output |
| 6 | no | no | n/a | 3-pin connector, input only |
| 7 | no | no | n/a | 3-pin connector, input only |

**Firmware note:**

Version 0.2 boards run RepRapFirmware 3.2beta1 or beta2. The WiFi and Ethernet variants use a common firmware binary, named Duet3Mini5plus.uf2 in the 3.2beta 1 release, and Duet3Mini5plus_v02.uf2 in the 3.2beta 2 release. These binaries also support an attached Single Board Computer.

Note that support for version 0.2 prototypes will not be maintained in future firmware, starting from RRF 3.2 stable onwards.
