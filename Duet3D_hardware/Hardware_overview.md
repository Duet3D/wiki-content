---
title: Hardware overview
description: 
published: true
date: 2022-07-29T09:24:24.100Z
tags: 
editor: markdown
dateCreated: 2021-12-06T11:36:08.818Z
---

# The Duet family of motion controllers

## Current production Duet mainboards

| Feature | [Duet 3 Mainboard 6HC](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6HC_Hardware_Overview) | [Duet 3 Mainboard 6XD](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6XD_Hardware_Overview) | [Duet 3 Mini 5+ WiFi](/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Hardware_Overview) | [Duet 3 Mini 5+ Ethernet](/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Hardware_Overview) | [Duet 2 WiFi](/Duet3D_hardware/Duet_2_family/Duet_2_WiFi_Ethernet_Hardware_Overview) | [Duet 2 Ethernet](/Duet3D_hardware/Duet_2_family/Duet_2_WiFi_Ethernet_Hardware_Overview) |
|:---|:---|:---|:---|:---|:---|
| **Board image** | ![duet_3_mb6hc_v0.6_top.jpg](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_v0.6_top.jpg =268x) | ![Duet 3 Mainboard 6XD v1.0](/duet_boards/duet_3_mb6xd/mb6xd_5_small.png =230x) | ![duet_3_mini_5+_wifi_top.jpg](/duet_boards/duet_3_mini_5_plus/duet_3_mini_5+_wifi_top.jpg =200x) || ![duet_2_wifi_v1.04c_top.jpg](/duet_boards/duet_2_wifi_ethernet_maestro/duet_2_wifi_v1.04c_top.jpg =240x){.align-center} ||
| **Processor** | ATSAME70Q20B || ATSAME54P20A || ATSAM4E8E ||
| **Processor features** | 300MHz ARM Cortex M7, 1Mb flash, 384Kb RAM, hardware floating point (double precision), DMA, 32Kb cache || 120MHz ARM Cortex M4F, 1Mb flash, 256Kb RAM, hardware floating point (single precision), DMA, 4Kb cache || 120MHz ARM Cortex M4F, 512Kb flash, 128Kb RAM, hardware floating point (single precision), DMA, 2Kb cache ||
| **Networking** | 100BaseTX Ethernet, or via attached SBC || 2.4GHz WiFi with external antenna, or via attached SBC | 100BaseTX Ethernet, or via attached SBC | 2.4GHz WiFi (internal and external antenna versions available) | 100BaseTX Ethernet |
| **Support for attached Raspberry Pi or other Single Board Computer (SBC)** | Yes || Yes || Yes, from RRF 3.2 (requires hardware modification) ||
| **Stepper drivers on-board** | 6 x TMC2160 or TMC5160 | NA | 5 x TMC2209 || 5 x TMC2660 ||
| **Stepper driver features** | Up to 6.3A peak current, microstep interpolation from any setting to x256, stall detection, stealthChop2 | NA | Up to 2.0A peak current (TBC), microstep interpolation from any setting to x256, stall detection, stealthChop2 || Up to 2.5A peak current, microstep interpolation from x16 to x256, stall detection ||
| **Stepper driver expansion** | Almost unlimited expansion using CAN-connected expansion boards || 2 additional TMC2209 stepper drivers; further expansion via CAN || Up to 7 additional stepper drivers, of which up to 5 can be TMC2660 ||
| **External stepper driver support** | Via CAN bus | 6 ports with step/direction/enable outputs, and fault inputs. Signalling is 5V single ended. Also via CAN bus | Possibility of driving 2 external stepper drivers from stepper driver expansion connector, or via CAN bus || Up to 7 external stepper drivers when not using DueX expansion board. Signal levels are 3.3V. A 4-channel expansion breakout board is available to level shift them to 5V. ||
| **High current outputs** | 1 x 15A, 3 x 6A each | 3 x 6A each | 1 x 15A, 2 x 5A each || 1 x 15A, 2 x 6A each ||
| **Thermistor/PT1000 inputs** | 4, optimised for 100K thermistors and PT1000 sensors || 3, optimised for 100K thermistors and PT1000 sensors || 3, optimised for 100k thermistors, can support PT1000 sensors with reduced accuracy ||
| **PT100 and thermocouple daughter board support** | 2 boards (4 channels) || 1 board (2 channels) || 2 boards (4 channels) ||
| **Heater/thermistor expansion** | Via CAN-FD expansion boards || Limited expansion via CAN || 5 more extruder heater outputs and thermistor inputs, 2 more PT100/thermocouple daughter boards (4 channels) ||
| **Endstop or filament monitor inputs** | 9 on-board I/O connectors for endstop, filament monitor, Z probe, hobby servo or PanelDue connection. Inputs are 30V-tolerant. Almost unlimited expansion via CAN-connected expansion boards. | 9 on-board I/O connectors for endstop, filament monitor, Z probe, hobby servo or PanelDue connection. Inputs are 30V-tolerant. 4 of the 9 pairs of IO also have alternative opto-isolated connectors. | 5 on-board I/O connectors, 2 input-only connectors = 7  in total for endstop, filament monitor, Z probe or PanelDue connection. Inputs are 30V-tolerant. Further expansion via CAN-connected expansion boards. || 5 on-board (STP pins are 8V-tolerant on revision 1.04 and later), 7 expansion ||
| **Endstop status LEDs** | No |||| Yes ||
| **Z probe connector** | See under Endstop or filament monitor inputs |||| 4-pin with IN/GND/MOD/3V3 pins (IN pin is 30V-tolerant in revision 1.04 and later) ||
| **Medium current outputs** | 6 x PWM-controlled outputs, of which 3 support tacho input. Voltage selectable between VIN / 12V / external power, in 2 banks. 1 x VIN-voltage, always-on output. 1 x 12V, always-on output || 4 controlled fans of which 2 support 4-wire fans with PWM and tacho wires, voltage selectable between VIN and 12V in 2 banks || 3 controlled fans, 2 always-on fan connectors, voltage selectable between VIN, 5V or external supply (all fans together); 6 more controlled fans on expansion board ||
| **Servo support** | See under Endstop and filament monitor inputs. Also one output with 5V signal level for servo, laser or VFD converter (shared control signal with one of the 2-wire  fan outputs). || See under Endstop and filament monitor inputs. Also one output with 5V signal level for servo, laser or VFD converter (shared control signal with one of the 2-wire  fan outputs). || Up to 5 via expansion connector (3.3V drive unless expansion board used) ||
| **Input power voltage** | 11V to 32V | 11V to 30V | 11V to 25V || 11V to 25V ||
| **Power monitoring** | VIN voltage and 12V regulator output voltage || VIN voltage || VIN voltage and input to on-board 5V regulator ||
| **LCD support** | Typically provided by HDMI touch screen attached to the SBC. PanelDue also supported. || PanelDue colour touch screen, mini 12864 mono graphics display using ST7567 controller (3.3V signal levels) || PanelDue colour touch screen ||
| **LED strip support** | Neopixel (max. 240 RGBW or 320 RGB in RRF 3.4) or DotStar || Neopixel (max. 80 RGBW or 106 RGB in RRF 3.4, external 5V power required) || Neopixel (max. 60 RGBW or 80 RGB in RRF 3.4, external 5V power required) ||
| **Other expansion** | Almost unlimited expansion via CAN-connected expansion boards || Via I/O ports and CAN bus || 4 GPIO pins on Duex 2/Duex 5 expansion board, 16 GPIO pins on third-party SX1509B-based I/O expansion board ||
| **SD card interface speed** | 25Mbytes/sec || 22.5Mbytes/sec || 20Mbytes/sec ||
| **RepRapFirmware versions available** | 3.0 series |||| 2.0 series, 3.0 series ||
| **Dimensions** | 140x134mm | 140x115mm | 120x100mm || 120x100mm ||
<!---
## Legacy Duets

| Feature | [Duet 2 Maestro](/Duet3D_hardware/Duet_2_family/Duet_2_Maestro) | [Duet 0.8.5](https://reprap.org/wiki/Duet) | [Duet 0.6](https://reprap.org/wiki/Duet) |
|:---|:---|:---|:---|:---|:---|
| **Board image** | ![duet_2_maestro_top.jpg](/duet_boards/duet_2_wifi_ethernet_maestro/duet_2_maestro_top.jpg =200x){.align-center} | | |
| **Notes** | Discontinued June 2020 | No longer supported | No longer supported  | 
| **Processor** | ATSAM4S8C | ATSAM3X8E || 
| **Processor features** | 120MHz ARM Cortex M4, 512Kb flash, 128Kb RAM, DMA | ARM Cortex M3, 512Kb flash, 96Kb RAM, DMA || 
| **Networking** | 10BaseT/100BaseTX Ethernet | Ethernet || 
| **Support for attached SBC** | No ||| 
| **Stepper drivers on-board** | 5 x TMC2224 | 5 x A4982 | 4 x A4982 | 
| **Stepper driver features** | Up to 1.6A peak current, microstep interpolation from any setting to x256, stealthChop2 | Up to 2.0A peak current, x16 microstepping || 
| **Stepper driver expansion** | Up to 2 additional TMC2208 or TMC2224 stepper drivers | Up to 4 additional stepper drivers || 
| **External stepper driver support** | None | Via expansion connector and additional hardware || 
| **Heater outputs** | Bed heater (up to 18A TBC), 2 x extruder heater (up to 6A each) | Bed heater (up to about 13A), 2 x extruder heater (up to 4A), up to 4 more extruder heaters on DueX4 expansion board (last one shared with Fan 1) | Bed heater (up to about 11A), extruder heater (up to 4A), up to 4 more extruder heaters on DueX4 expansion board | 
| **Thermistor/PT1000 inputs** | 4, optimised for 100K thermistors and PT1000 sensors | 3 | 2 | 
| **PT100 and thermocouple daughter board support** | 2 boards (4 channels) - second board available only if I2C expansion pins are not used | Via expansion connector and additional hardware || 
| **Heater/thermistor expansion** | None | 4 more extruder heater outputs and thermistor inputs on DueX4 || 
| **Endstop or filament monitor inputs** | 5 on-board (STP pins are 30V-tolerant) | 5 on-board, 4 expansion | 4 on-board, 4 expansion | 
| **Endstop status LEDs** | No | Yes || 
| **Z probe connector** | 5-pin with IN/GND/MOD/3V3/5V pins (IN pin is 30V-tolerant) | 4-pin with IN/GND/MOD/3V3 pins | No, use expansion connector | 
| **Fan outputs** | 3 controlled fans, 1 always-on fan connector, voltage selectable between VIN, 5V or external supply in 2 banks | 2 controlled fans | 1 controlled fan | 
| **Servo support** | 1 servo output on board with 5V drive (shares the output with the Z probe MOD pin and the diagnostic LED) | Via expansion connector || 
| **Input power voltage** | 11V to 28V | 11V to 28V | 11V to 30V | 
| **Power monitoring** | VIN voltage | No || 
| **LCD support** | PanelDue colour touch screen, 12864 mono graphics display using ST7920 controller (5V signal levels) | PanelDue colour touch screen (via expansion connector) || 
| **LED strip support** | No | Via expansion connector and additional hardware || 
| **Other expansion** | 2 pins usable as analog inputs, GPIO, or UART; 2 pins usable as I2C or GPIO | No || 
| **SD card interface speed** | 15Mbytes/sec | 21Mbytes/sec || 
| **RepRapFirmware versions available** | 2.0 series, 3.0 series | 1.x series (latest is 1.26.1) || 
| **Dimensions** | 120x100mm ||| 
<!---
# Duet vs. other 32-bit controllers

| Property | BigTreeTech SKR 1.3 | SpeedyBoard | Smoothieboard 1.1 | Smoothieboard 2 Mini | Smoothieboard 2 Prime | Smoothieboard 2 Pro | Duet Maestro | Duet WiFi or Ethernet | Duet 3 6HC
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| Open source? | no | no | **yes** | **yes** | **yes** | **yes** | **yes** | **yes** | **yes**
| Processor type | ARM Cortex M3 | **ARM Cortex M7** | ARM Cortex M3 | ARM Cortex M4F + Cortex M0 | ARM Cortex M4F + Cortex M0 | ARM Cortex M4F + Cortex M0 | ARM Cortex M4 | ARM Cortex M4F | **ARM Cortex M7**
| Processor speed | 100MHz | **400MHz** | 120MHz | 204MHz | 204MHz | 204MHz | 120MHz | 120MHz | 300MHz
| RAM | 64Kb | ??? | 64kb | 264kb | 264kb | 264kb | 128Kb | 128Kb | **384Kb**
| Floating point hardware? | No | **Double precision** | No | Single precision | Single precision | Single precision | No | Single precision | **Double precision**
| Stepper driver type | None included | STSPIN820 | up to 5x A5984 | 4x A5984 | 4x TMC2660 | 3x TMC2160, 2x TMC2660 | 5x TMC2224 | 5x TMC2660 | **6x TMC5160**
| Stepper motor peak current | n/a | ?? (est. 1.2A) | 2A claimed | 2A claimed | 2.8A claimed (see note) | 5A (TMC2160) and 2.8A (TMC2660) claimed (see note) | 1.6A | 2.4A | **6.3A**
| Max microstepping | n/a | **x256** | x32 | x16(?) | **x256** | **x256** | **x256** | **x256** | **x256**
| Microstep interpolation? | n/a | no | no | no | **yes** (from x16 only) | **yes** (from x16 only on TMC2660 drivers) | **yes** | **yes** (from x16 only) | **yes**
| StealthChop? | n/a | no | no | no | no | **yes** (TMS2160 drivers), no (TMC2660 drivers) | **yes** | no | **yes**
| Stall detection? | n/a | no | no | no | **yes** | **yes** | no | **yes** | **yes**
| Network interface | No | **WiFi** | **Ethernet** | **WiFi and Ethernet** (via SBC), Ethernet direct) | **WiFi and Ethernet** (via SBC), Ethernet direct) | **WiFi and Ethernet** (via SBC), Ethernet direct) | **Ethernet** | **WiFi or Ethernet** | **WiFi and Ethernet** (via SBC), Ethernet direct)
| High speed SD card | no | unknown (closed source) | no | **yes** | **yes** | **yes** | **yes** | **yes** | **yes**
| Direct SBC interface | no | **yes** | no | **yes** | **yes** | **yes** | no | not yet | **yes**
| I/O connectors | unknown | 18 inputs? ("up to 18 digital inputs, up to 8 analog inputs") | ??? | ??? | ??? | ??? | 5 endstop/filament monitor inputs | 12 endstop/filament monitor inputs, GPIO pins | 9 input/output connectors (some with UART, some with analog input option, some with hardware PWM output option), more on CAN-connected expansion boards
| Heater/fan outputs | unknown | up to 10 | 3 (0-3 high current, 2-3 medium current) | 0 very high current, 1 high current, 1 low current | 1 very high current, 2 high current, 1 low current | 2 very high current, 2 high current, 2 low current | 1 very high current, 2 high current, 3 low current | 1 very high current, 2 high current, 3 low current | 1 very high current, 3 high current, 6 low current
| Stepper driver expansion | none(?) | none(?) | none(?) | none | none yet | none yet | 2x TMC2224 on daughter board | 7 external, or 5 on DueX5 + 2 external, or 2 on DueX2 + 7 external | Almost unlimited via CAN bus (e.g. Duet 3 EXP3HC shipping Jan 2020)
| Other expansion | unknown | ??? | ??? | ??? | ??? | ??? | Daughter board for SPI temperature sensors (PT100 or thermocouple) | DueX5 or DueX2 (5 heaters, 6 fans, 5 endstops/filament monitors, GPIO); 4 daughter boards for SPI temperature sensors (PT100 or thermocouple); 2 additional endstops/filament monitors | Almost unlimited expansion via CAN-FD bus
| Power monitoring | no | **yes** | no | no | **yes** | **yes** | **yes** | **yes** | **yes**
| Firmware | Smoothieware, **RepRapFirmware**, Marlin | unknown (proprietary?) | Smoothieware, **RepRapFirmware** | Smoothieware | Smoothieware | **RepRapFirmware** | **RepRapFirmware** | **RepRapFirmware** | **RepRapFirmware**
| Other | | | | | USB master port | On-board FPGA, USB master port | Direct PT1000 support with superior resolution | Choice of WiFi or Ethernet version | Direct PT1000 support with superior resolution
| Availability | **now** | not specified | **now** | est. Feb 2020 (Kickstarter backers only) | est. Jan 2020 (Kickstarter backers only) | est. Apr 2020 (Kickstarter backers only) | **now** | **now** | **now** |
-->

# Version Numbering

The following version numbering system is used for all Duet3D products. Note there are some legacy products that do not conform to this system; please enquire if it is unclear.

\<Product Name><Major Revision #>.<Minor revision # or ##>\<bug fix letter>

**Examples**

| \<Product Name> | <Major Revision #>. | <Minor revision ## or #>^1^ | \<bug fix letter>^2^ |
|:---|:---|
| Duet 2 Wifi | 1. | 04 | a |
| Smart Effector | 1. | 6 |  |

Notes:
^1^ Minor revision numbers can start from 0 or 00
^2^ Bug fix letter can be blank.