---
title: Duet 3 6XD
description: 
published: true
date: 2022-01-24T19:30:57.121Z
tags: 
editor: markdown
dateCreated: 2022-01-24T19:30:04.220Z
---

![Render of the Duet 3 Mainboard 6XD v0.1](/duet_boards/duet_3_mb6xd/6xd_v0.1_proto_render1.png =400x)

## This page is still in draft

# Introduction

The Duet 3 Mainboard 6XD is designed to allow interfacing with the most common external stepper and servo motor dives using step and direction signals, with support for 6 external drivers. In addition it provides Optoisolated Input and Output connections for some of the GPIO functions to allow easier interfacing with PLC control systems.

Like the other Duet 3 mainboards it can be accesed directly over the network in stand alone mode (vi the Ethernet post) or it has the option of using an SBC (Single Board Computer, e.g. Raspberry Pi) for control. Using the Duet 3 CAN-FD bus it is expandable with the range of Duet 3 expansion and tool boards.

# Features

## Hardware Specification

The main hardware features of the Duet 3 Mini 5+ are listed below.


| HARDWARE SPECIFICATION ||
|---|---|
| **Processor** | [ATSAMV71Q20B](https://www.microchip.com/en-us/product/ATSAMV71Q20) alternatively the ATSAME70Q20B will be used. They are functionally equivalent in this design |
| **Processor features** | 300MHz ARM Cortex M7, 1Mb flash, 384Kb RAM, hardware floating point (double precision), DMA, 32Kb cache |
| **Networking/Comms** | 10BaseT/100BaseTX Ethernet, or via attached SBC; USB port; serial port |
| **External Driver Support** | 6 ports with step/direction/enable outputs, and fault inputs. signalling is 5V single ended. |
| **High current outputs** | 3 x High current outputs (up to 6A each) |
| **medium current outputs** | 6 x medium current outputs for PWM fans and similar devices, of which 3 support 4-wire fans with PWM and tacho wires. Arrnaged in two banks, each bank is individually fused with voltage selectable between VIN / 12V.|
| **Thermistor/PT1000 inputs** | 4, optimised for 100K thermistors and PT1000 sensors |
| **Inputs/Outputs** | 9 on-board I/O connectors for endstop, filament monitor, Z probe, hobby servo, or PanelDue connection. Inputs are 30V-tolerant. 4 of the 9 pairs of IO are Optoisolated.
| **Power monitoring** | VIN voltage monitoring allows for state save on power failure.12V regulator output voltage also monitored |
| **SD card interface** | On-board high speed (25Mbytes/sec) SD card socket. |


| **EXPANSION** ||
|---|---|
| **Support for attached Raspberry Pi or other Single Board Computer (SBC)** | Yes |
| **External stepper driver support** | 6 on board, further via CAN-FD bus |
| **Stepper driver expansion** | Via CAN-FD expansion boards |
| **IO/Output/thermistor expansion** | Via CAN-FD expansion boards |
| **PT100 and thermocouple daughter board support** | 2 boards (4 channels) |
| **Servo support** | One output with 5V signal level for hobby servo, laser or VFD converter |
| **LCD support** | PanelDue or via HDMI touch screen attached to the SBC. |
| **LED strip support** | Neopixel (max. 60 LEDs) or DotStar |


## Operating limits

|:---|:---|
| **Input power voltage** | 11V to 30V (14V-30V to use onboard 12V regulator)|
| **Input connector rated current** | 25A maximum, or fused limit (whichever is lower) |
| **high current outputs** | 3 x high current outputs up to 6A each, total high current heater limit fused at 15A |
| **medium current outputs** | 3 x medium current outputs up to 1A each, each bank of medoum current outputs fused at 2A (note when set to 12V there is a total 800mA limit) |
| **External Driver Signal voltage** | 5V |
| **External Driver Signal current** | TBCmA |
| **Non optoisolated Inputs/Outputs** | Inputs are 30V-tolerant |
| **Optoisolated Inputs/Outputs** | Limits TBC |
| **Fuses** | 15A for V_FUSED, 2A for each bank of medium current outputs/ |
| **5V current limit** | 3A total on 5V and 3.3v, including the internal current consumption (around 200-300mA), any PanelDue or other display, and any endstops/Z probes that draw significant power, and any connected SBC powered by the Duet. |
| **12V current limit** | 800mA (only used for fan outputs OUT_3 thru OUT_8, when selected) |

## Firmware notes

* Compatible RepRapFirmware versions: RRF 3.4 and later

For firmware update instructions, see [Installing and updating firmware](/User_manual/RepRapFirmware/Updating_firmware).

If running from an SBC, see [SBC setup for Duet 3](/User_manual/Machine_configuration/SBC_setup).
<!--- #restore once the feature comparison tale is updated
## Feature Comparison

See the [Hardware overview](/Duet3D_hardware/Hardware_overview) page for a feature comparison table between different versions of the Duet.
-->

# Physical properties

## Dimensions

![Diagram showing the critical dimensions of the Duet3 6XD v0.1 prototype](/duet_boards/duet_3_mb6xd/duet3_mb_6xd_v0.1_dimensions_d1.0.png =500x)

## Mounting

The mounting holes are isolated and not plated through, they have a keep out area around the M4 clearance hole of an additional 2.2mm radius. Any metallic mounting solution should keep inside this keep-out area.

The board should be mounted on standoffs, ensuring the back of the board cannot come into contact with a conductive surface.

## Cooling

In many applications passive cooling will be sufficient, especially if the board is mounted vertically in a well ventilated position. If active cooling is needed then a fan blowing across the back of the board will cool the high current mosfet heatsinking and onboard pwoer supply heatsinking.

The MOSFETs' heatsinks are connected to the PCB and the majority of the heat is dissipated via the PCB so heatsinks on the FETs or other ICs are largely ineffective.

### MCU Temp

TBC

## 3D Model

STEP file to follow.

# Physical connections

## Wiring diagram
<!---
## Tabs{.tabset}
-->

### Revision 0.1


![duet3_mb_6xd_v0.1_d1.0_wiring.png](/duet_boards/duet_3_mb6xd/duet3_mb_6xd_v0.1_d1.0_wiring.png =500x)

#### Errata

* The Driver Active enable select options: "ACTIVE ENABLE" and "ACTIVE DISABLE" legends are the wrong way round on the board silk screen

<!---
## Description of Connections

| Duet 3 Mini 5+ Connectors |||
|:---|:---|---|
| **1 x 2-way barrier strip** | POWER IN, GND, VIN  | Two pins for main VIN and GND protected by a 15A fuse. |
| **3 x 2-pin JST VH connectors** | OUT_0, OUT_1, OUT_2 | Intended for extruder heaters or similar high current resistive loads. Flyback diodes are built-in to these outputs. Maximum recommended current 6A each. |
| **3 x 4-pin KK connectors** | OUT_3, OUT_4, OUT_5 | Intended for PWM-controllable fans or other medium/low current loads. Flyback diodes are built-in to these outputs. The connector fits a standard PC-type 4-pin PWM fan. Alternatively, a 2-pin fan may be connected between the V_OULC1+ pin (+ve) and the OUT_n_NEG pin (-ve).
| ^^ | ^^ | **Note:** OUT_3 and OUT_4 are protected by a flyback diode to V_FUSED. This does not provide protection if driving these outputs from a higher voltage than V_FUSED |
| **1 x 3-pin Jumper** | OUT_3&4 Select V | The positive supply to the above connectors is the centre pin of the 3-pin jumper block. A jumper in the "left" position will power them from the fused VIN supply (max 2A each ). A jumper in the "right" position will power them from the onboard 12V regulator (subject to overall 12V supply current *see note 1 below*). |
| **2 x 2-pin KK connectors** | OUT_5, OUT_6 | these are intended for PWM-controllable fans or other medium/low current loads. Flyback diodes are built-in to these outputs. **Note** out_6 PWM pin is shared with LASER/VFD |
| ^^ | ^^ | **Note:** OUT_5 and OUT_6 are protected by a flyback diode to V_FUSED. This does not provide protection if driving these outputs from a higher voltage than V_FUSED |
| **1 x 3-pin Jumper** | OUT_5&6 Select V | The positive supply to the above connectors is the centre pin of the 3-pin jumper block. A jumper in the "left" position will power them from the fused VIN supply (max 2A each ). A jumper in the "right" position will power them from the onboard 12V regulator (subject to overall 12V supply current *see note 1 below*). |
| **5 x 4-pin KK connectors** | DRIVER_0, DRIVER_1, DRIVER_2, DRIVER_3, DRIVER_4 | Stepper motor connections, See "Connecting Stepper Motors" section below. |
| **1 x 16-pin socket strip** | EXTERNAL DRIVERS | Step, Direction, Enable, Diag and UART connections for DRIVER_5 and DRIVER_6, along with fused VIN, ground, 3.3V and 5V. Designed for a Mini 2+ expansion board to plug directly in. Step, direction and enable can also be used with other external drivers (3.3V signal level). |
| **1 x 2x5 IDC connector** | PanelDue_SD | Connects the [PanelDue](/Duet3D_hardware/Accessories/PanelDue) UART and shared SPI bus for external SD card. Powered from 5V supply (*see note 2*). **Note** shared with io0.in and io0.out pins on the IO_0 header. |
| **1 x 3-pin KK connectors** | LASER/VFD | 5V buffered output shared with out6, along with 5V and ground supply (*see note 2 below*). Provides a 5V PWM signal to drive hobby servos, and PWM->analog controls for VFDs or Lasers. **Note** out6 PWM pin is shared with OUT_6 |
| **1 x 2-pin KK connectors** | 12V | Always on 12V supply (*see note 1 below*) |
| **Network** | **Ethernet** | 1 x RJ45 100BaseT Port. *non MDIX* connect to an Ethernet switch, hub or MDIX enabled laptop port. If connecting to a non MDIX enabled port use a crossover cable. Orange LED on Ethernet port indicates Ethernet enabled, green LED indicates network activity |
| ^^ | **Wifi** | U.FL/IPEX push on connector for external antenna. *Note this connector is delicate, take care when plugging and unplugging*. "LED ESP": green LED indicates Wifi connection status |
| **Reset** |  | Single push to reset the board. Double push to put the board into UF2 bootloader upload mode. See [Duet 3 Mini 5 + Firmware, Updating firmware via USB](https://docs.duet3d.com/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Firmware#updating-firmware-via-usb) |
| **1 x JST ZH 6-pin connectors** | SWD | Connection for an SWD programming device such as an Atmel-ICE |
| **1 x 2-pin KK connectors** | CAN | CAN-FD Bus connection for Duet 3 CAN-FD expansion boards. |
| **5 x 5-pin KK connectors** | IO_0, IO_1, IO_2, IO_3, IO_4 | These are for endstop switches, Z probes, filament monitors and other low-voltage I/O functions. Each connector provides both 3.3V and 5V power. The inputs will tolerate up to 30V with 10K series resistors (but see below for bypass option). The outputs are 3.3V signals levels with 470R series resistors. IO_1,2,3 are PWM capable. |
| **2 x 2-pin Jumpers 10K->470R bypass** | IO2.in, IO3.in | v1.01 and later only. Jumpers to allow the 10K resistors on IO2.in and IO3.in to be bypassed with 470R resistors. This is required to use IO2 or IO3 for I2C. |
| **2 x 3-pin KK connectors** | IO_5, IO_6 | Input only IO connections that will  tolerate up to 30V with 10K series resistors |
| **3 x 2-pin KK connectors** | TEMP_0, TEMP_1, TEMP_2 | Connections for thermistor or PT1000 sensors. |
| **1 x 2x13 IDC connector** | SBC | Connections to a Single Board Computer (SBC) such as a Raspberry Pi. |
| **1 x 2x5 IDC connector** | TEMPDB | For connecting a [PT100](/Duet3D_hardware/Accessories/PT100_Temperature_Daughterboard) or [thermocouple](/Duet3D_hardware/Accessories/Thermocouple_Daughterboard) interface board. **Note** boards cannot be stacked so only 1 board at a time is supported. |
| **1 x 3-pin Jumper** | 5V_SELECT | Source of optional External 5V input, see note 2 below |
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

For more information on pin names, see [Pin Names](https://docs.duet3d.com/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names).

RepRapFirmware 3 uses pin names for user-accessible pins, rather than pin numbers, to communicate with individual pins on the PCB. In RRF 3 no user-accessible pins are defined at startup by default. Pins can be defined for use by a number of gcode commands, eg M574, M558, M950.

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
| TEMP_1 | temp1 | |
| TEMP_2 | temp2 | |
| **Inputs/Output** |||
| IO_0 | io0.in | Endstops, Z probes, filament monitors etc |
| ^^ | io0.out | ^^ |
| IO_1 | io1.in | ^^ |
| ^^ | io1.out | ^^ |
| IO_2 | io2.in | ^^ |
| ^^ | io2.out | ^^ |
| IO_3 | io3.in | ^^ |
| ^^ | io3.out | ^^ |
| IO_4 | io4.in | ^^ |
| ^^ | io4.out, pson | ^^ |
| IO_5 | io5.in | Input only |
| IO_6 | io6.in | ^^ |
| **SPI CS** |||
| TEMPDB | spi.cs0 | Thermocouple or PT100 daughterboard |
| ^^ | spi.cs1 | ^^ |
| ^^ | spi.cs2 | ^^ |
| ^^ | spi.cs3 | ^^ |
| **Miscellaneous** |||
| LASER/VFD | laser, vfd, out6 | Pin shared with OUT 6 and LASER/VFD connectors |
| EXT 5V | pson, io4.out | For controlling an external PSU or SSR, shared with io4.out |

## IO port pin capabilities

OUT_0 to OUT_6 are all PWM-capable. OUT_6 is shared with LASER/VFD.

Capabilities of IO_0 to IO_6 are shown below.

| IO # | UART? | Analog in? | PWM out? | Notes |
|:---|:---|
| 0 | yes | no | no | AUX0 port, can be used to connect a PanelDue. Configure using M575 P1. |
| 1 | yes | no | yes | AUX1 port. Configure using M575 P2. |
| 2 | yes | no | yes | The  standard firmware does not support this UART |
| 3 | no | yes | yes | Shared with  backlight control on 12864 displays having Neopixel backlights |
| 4 | no | no | no | IO4_OUT is shared with PSON output |
| 5 | no | no | n/a | 3-pin connector, input only |
| 6 | no | no | n/a | 3-pin connector, input only |

## Power distribution

### VIN (Input voltage from PSU)

Supply 12V-24V input power (11-25V absolute minimum/maximum) between the GND and VIN terminals.

VIN is split via two fuses:

V_FUSED: 10A :  All on board power demands, other than OUT0 (Normally used for heated bed

OUT0_FUSE: 15A: Directly to the V_OUT0 terminal

VFUSED is distributed across the board as follows:

* 12V Regulator
* 5V regulator
* Stepper drivers (including external driver header)
* OUT 1 and OUT 2 headers
* V_OUTLC1 and V_OUTLC2 selection jumpers

### 12V

**12V_EXT**: 800mA limit, Supplied to:
  * V_OUTLC1/2 for OUT_3 thru OUT_6.
  * 12V header (useful for supplying 12V to a VFD controlled from the adjacent 5V PWM header)

### 5V

**5V**: Split up to 5V_EXT, 5V_INT. Those points can also be supplied by VBUS (i.e. USB) and from the 5V_SELECT jumper that selects between 5V_EXT_INPUT and 5V_SBC. (see 5V power options below for more details).
* **5V_EXT feeds**:
  * IO headers
  * 12864 display and PanelDue
  * LASER/VFD header
* **5V_INT feeds**:
  * Internal and External 3.3V regulators
  * External Driver header
  * Internal 5V logic

5V input can come from one of these sources:

* **Onboard 5V regulator:** Once 3.3V and other onboard demands are met approximately 700mA remains for use on the 5V_EXT rail.
* **USB:** Can supply both 5V_INT and 5V_EXT. limits based on USB specification. 
* **5V_EXT_INPUT**: With the 5V_SELECT jumper set to this position, 5V is passed through to both 5V_EXT and 5V_INT from the EXT_5V header. When using EXT_5V, add jumper to Int_5V_Disable, to disable the onboard 5V regulator. The EXT_5V header also has a pin for controlling an external power supply (note signal shared with io4.out). This allows for the board to be powered from 5V, with an external supply for VIN turned on and off as required. 
* **5V_SBC:** In some, limited, cases it may be desirable to power the board from the 5V output of a SBC connected to the SBC header. Note that the total power of the Duet+ peripherals must be factored into the SBC power budget. Also note that powering the SBC from the duet is not supported.

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

### Connecting a PanelDue

A [PanelDue](/Duet3D_hardware/Accessories/PanelDue) can be connected to IO_0 using a 4-wire cable. See [Connecting a PanelDue](https://docs.duet3d.com/User_manual/Connecting_hardware/Display_PanelDue#option-1-4-way-cable)

Alternatively, it can be connected using a ribbon cable plugged into the socket labelled PanelDue_SD, which enables access to the PanelDue SD card socket. However, there are some caveats; see note on Duet 3 Mini 5+ on [Connecting a PanelDue](https://docs.duet3d.com/User_manual/Connecting_hardware/Display_PanelDue#duet-3-mini-5-wifiethernet).

## Connecting a Raspberry Pi

The Duet 3 Mini+ cannot power the Pi. You must power the Pi separately. The Raspberry Pi is sensitive to the input voltage, and many smartphone chargers or other USB power supplies cannot supply sufficient voltage. Therefore, we strongly recommend that you use the official Raspberry Pi PSU, or another PSU specifically designed to power a Raspberry Pi. If the red LED on the Pi is not continuously illuminated, the power supply is insufficient.

In other respects, SBC connection to the Duet and configuration is the same for the Duet 3 Mini  as for the Duet 3 Mainboard 6HC. See [SBC Setup for Duet 3](/User_manual/Machine_configuration/SBC_setup).

When using an attached Raspberry Pi or other SBC, the WiFi or Ethernet interface on the Duet 3 Mini+ is disabled.

## Connecting Stepper Motors

See [Connecting stepper motors](/User_manual/Connecting_hardware/Motors_connecting).

## Connecting Fans

See [Connecting and configuring fans](/User_manual/Connecting_hardware/Fans_connecting).

# Revision History

# Tabs{.tabset}

## Revision 1.01

* Added Jumpers to bypass 10K input protection resistors with 470R resistors for IO2.in and IO3.in. This is to allow these inputs to be used with I2C.
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
| 7 | no | no | n/a | 3-pin connector, input only