---
title: Connecting a PanelDue
description: 
published: true
date: 2022-01-10T16:13:02.743Z
tags: 
editor: markdown
dateCreated: 2021-11-09T17:02:03.830Z
---

![displays_paneldue_screen.jpg](/manual/displays/displays_paneldue_screen.jpg =500x)

# Introduction

All Duet boards support the PanelDue colour TFT touch screen control panel.

There are two cable options for connecting the PanelDue, both options are included with the PanelDue V3 kit. Option 1 is the included 4-wire cable with Molex KK connector ends. Option 2 is the included 10-wire ribbon cable. For some boards, both cables need to be plugged in to enable both TFT panel and SD card socket.

Information for connecting older versions of the PanelDue is available from [dc42's blog here](https://miscsolutions.wordpress.com/paneldue/).

### PanelDue v3.0, 5i and 7i connection matrix

|  | 4-wire | ribbon | 4-wire and ribbon |
|:---|:---|
| Duet 3 Mainboard 6HC | PanelDue, no SD | see below | see below |
| Duet 3 Mini 5+, Duet 3 MB6XD | PanelDue, no SD | PanelDue, SD | no reason to use both |
| Duet 2 WiFi/Ethernet v1.02 and later | PanelDue, no SD | PanelDue, SD | no reason to use both |
| Duet 2 WiFi/Ethernet v1.0, v1.01 | PanelDue, no SD | SD only | PanelDue, SD |
| Duet 2 Maestro | PanelDue, no SD | PanelDue, SD | no reason to use both |

# Option 1: 4-way cable

* Compatible with all Duets and all PanelDues
* Provides longest cable run
* SD socket on PanelDue not accessible; use ribbon cable or see notes for SD connectivity

|  | Connect to: | Notes |
|:---|:---|
| Duet 3 | IO_0 | Only IO_0 supports PanelDue |
| Duet 2 WiFi/Ethernet | PANELDUE connector |  |
| Duet 2 Maestro | PANELDUE connector |  |

Use a 4-way cable terminated in Molex KK or compatible connectors. See below for connections for specific Duet boards.

The length of the 4-way cable is not critical, however the resistance per conductor should not exceed 0.1 ohm. The SD card socket on the TFT panel will not be functional. The cables supplied by Escher3D and Duet3D are about 800mm long. There have been reports of cables up to 1500mm long being successfully used. Take care to route the cable away from motor and endstop cables. Twisting the cables may help prevent cross talk interference.

## Tabs {.tabset}

### Duet 3 (all main boards)

A PanelDue can be connected to connector IO_0 using a 4-core cable wired like the one shown in the images below. The 4-wire cable supplied with the PanelDue has a 4-way Molex KK connecter on each end, but is supplied with a 5-way Molex KK connector for use with Duet 3. You will need to rewire one end. The 4-wire cable does not allow access to the SD card socket on the PanelDue.

**NOTE:** the Duet 3 pin order in the table below is **not** the physical order. See the diagram for the physical pin order.

| PanelDue pin | Wire colour | Duet 3 IO_0 pin | |
|---|---|
| +5V | Red | 5V_EXT (pin 5) | ![duet_3_mb6hc_input_output.jpg](/duet_boards/duet_3_mb6hc/duet_3_mb6hc_input_output.jpg =200x) |
| GND | Black/yellow | GND (pin 3) | ^^ |
| Dout | Blue | IO_0_IN (pin 2) | ^^ |
| Din | Green | IO_0_OUT (pin 4) | ^^ |

![displays_paneldue_d3_wiring_01.jpg](/manual/displays/displays_paneldue_d3_wiring_01.jpg =400x)

![displays_paneldue_d3_wiring_02.jpg](/manual/displays/displays_paneldue_d3_wiring_02.jpg =400x)


### Duet 2 WiFi, Ethernet and Maestro

For PanelDue 5i and 7i, connect the supplied 4-wire cable as shown.

![displays_paneldue_d2_wiring_01.jpg](/manual/displays/displays_paneldue_d2_wiring_01.jpg =400x)

PanelDue V2

![displays_paneldue_d2_wiring_02.png](/manual/displays/displays_paneldue_d2_wiring_02.png =600x)

### Duet 2 WiFi v1.0 and v1.01

Older versions of the Duet 2 WiFi/Ethernet need both the 4-wire and ribbon cable to be plugged in to use the TFT Panel and the SD card socket, when connecting PanelDue v2.0 or v3.0.

# Option 2: Ribbon cable

* Available for PanelDue V2.0, V3.0, 5i and 7i only
* Compatible with Duet 3 Mini 5+, Duet 3 MB6XD, Duet 2 WiFi / Ethernet / Maestro
* Supports use of PanelDue SD card slot
* Connects to PanelDue_SD on Duet 5 Mini 5+ and Duet 3 MB6XD, CONN_SD on Duet 2
* Cable length limited to 400mm for reliability
* For PanelDue V2.0, both the ribbon cable **AND** 4-wire cable need to be connected to enable use of SD card slot.

|  | Connect to: | Notes |
|:---|:---|
| Duet 3 MB6HC | N/A | See below for special wiring scheme |
| Duet 3 Mini 5+, Duet 3 MB6XD | PanelDue_SD | Additional jumper may be required; see note below |
| Duet 2 WiFi/Ethernet | CONN_SD |  |
| Duet 2 Maestro | CONN_SD |  |

In order to use the SD card slot on the PanelDue, you must use the ribbon cable option. If you do not wish to use the SD card slot, it's recommended to use the 4-wire cable option described in Option 1.

Connect a 10-way ribbon cable between socket X5 on the PanelDue and socket CONN_SD (Duet 2) or PanelDue_SD (Duet 3). The connector is a standard 10 pin 2 row 2.54mm pitch box connector that accepts IDC connectors for 1.27mm ribbon cable.

![displays_paneldue_ribbon_01.jpg](/manual/displays/displays_paneldue_ribbon_01.jpg =700x)

*(Photo from [www.flickr.com](https://www.flickr.com/photos/adafruit/12226184743))*

In tests using standard 28awg 1.27mm spaced ribbon cable, 400mm worked reliably but 800mm did not. So 400mm is the maximum recommended cable length. You can also get 26awg 1.27mm ribbon cable, and by using such cable you may be able to achieve reliable operation with cables longer than 400mm.

**Caution:** if you are using a thermocouple and/or PT100 daughter board, the use of long ribbon cables between the Duet and PanelDue may affect communication between the Duet and the daughter boards, because the ribbon cable connection to the SD card on PanelDue uses the same SPI bus as the daughter boards.

### Duet 3 Mini 5+ and Duet 3 MB6XD

On Duet 3 Mini 5+ and Duet 3 MB6XD, a PanelDue can be connected using a ribbon cable plugged into the socket labelled 'PanelDue_SD'. This is the only way to use the SD card socket on the PanelDue. Note:

* Non-integrated versions of PanelDue and older versions of PanelDue 5i and 7i do not provide a card detect signal, so for these displays it is necessary to ground the card detect signal. On the Duet 3 Mini 5+ you can do this by bridging pins 2 and 4 of the EXP2 connector as shown here.
![displays_paneldue_ribbon_02.png](/manual/displays/displays_paneldue_ribbon_02.png =400x)
* The PanelDue connector on the Duet 3 Mini 5+ shares the io0.out and io0.in pins, so IO_0 cannot be used at the same time. 

### Duet 3 MB6HC using ribbon cable

Although the Duet 3 MB6HC does not have a connector for the PanelDue ribbon cable, if access to the SD card on PanelDue is required then this is possible using a special wiring arrangement. You must use RepRapFirmware 3.4 or later, and you must enable the external SD card using this command:

`M950 D1 C"cs_pin+cd_pin"`

where ''cs_pin'' is the pin that the SD card CS line is connected to and ''cd_pin'' is the pin that the SD card detect pin (if available) is connected to. For these pins, if you are not using a temperature daughter board then we suggest that you use CS0 and CS2 respectively on the SPI daughter board connector to simplify the wiring; otherwise use the output pin and the input pin on one of the IO connectors. So the M950 line would look like this:

`M950 D1 C"spi.cs0+spi.cs2"`

or (for example)

`M950 D1 C"io3.out+io3.in"`

Note: if you are using an older version of either PanelDue 7i or PanelDue 5i, or a non-integrated version of PanelDue, then those do not support the CD signal. In that case you should omit the second port, for example:

`M950 D1 C"spi.cs0"`

The required wiring is as follows

| PanelDue ribbon cable pin | Signal name | Duet 3 MB6HC pin |
|---|---|
| 1 | +5V | IO0 +5V |
| 2 | GND | Temperature daughterboard pin 2 |
| 3 | CS | Temperature daughterboard pin 3 (spi.cs0) or IOx OUT |
| 4 | SCLK | Temperature daughterboard pin 4 |
| 5 | MOSI | Temperature daughterboard pin 5 |
| 6 | MISO | Temperature daughterboard pin 6 |
| 7 | CD | Temperature daughterboard pin 7 (spi.cs2) or IOx IN |
| 8 | +3V3 | Temperature daughterboard pin 8 |
| 9 | Dout | IO0 IN |
| 10 | Din | IO1 OUT |

If you have no temperature daughter boards installed, then one way to cable this is to use both the ribbon cable and the 4-way cable, and remove conductors 1, 9 and 10 of the ribbon cable as illustrated here. **Caution! Using a ribbon cable with all conductors present will feed +5V into the microcontroller!**

![displays_mb6hc_ribbon_cable.jpg](/manual/displays/displays_mb6hc_ribbon_cable.jpg =300x)

# Firmware configuration

**RepRapFirmware 3.x and later**: Using a PanelDue with Duet 3 and Duet 2 and RRF3 will require `M575 P1 S1 B57600` being present in config.g.

**RepRapFirmware 2.x and earlier**: In RRF2 the command above was assumed as the default, so no additional configuration is required.

## PanelDue firmware and RRF compatibility

Generally it is best to run the latest version of the PanelDue firmware that is supported by the RepRapFirmware version on your Duet mainboard. See: [Installing and updating PanelDue firmware](/User_manual/RepRapFirmware/Updating_PanelDue)

From RRF v3.2, PanelDue firmware releases are co-ordinated with the RRF release, and share the same version number. Use the PanelDue firmware version that matches your Duet mainboard's firmware version.

| RepRapFirmware version | PanelDue firmware version | Notes |
|---|---|
| 3.2 and later | Use matching firmware number | 3.2 and later uses RRF 3 Object Model, not backward compatible |
| 3.11 and earlier (2.x, 1.x) | 1.24 | See notes below |

## Notes on PanelDue firmware v1.24

The PanelDue firmware v1.24 assumes a fixed relationship between bed, tools and heaters. Please note the following:

* PanelDue will display the bed heater H0 first (even if it is disabled), then iterate the defined tools. It then iterates the defined heaters below this. It expects a 1:1 relationship between tools and heaters. This means:
  * if you have a machine that uses one heater for more than one tool (eg a 2-into-1, filament-swapping hot end), it will display more tools than heaters. Tools may not line up with their respective heaters.
  * if you have more heaters defined than tools (eg extra bed heater/chamber heater, or a tool that uses multiple heaters), you'll have more heaters than tools.
* The PanelDue also iterates the heaters from the first defined heater to the last, including all heaters in between, whether defined or not. This means if you have a heater defined on H0 (bed) and one on H5 (Duex output), it will show all the ones in between, eg H0, H1, H2, H3, H4 and H5. For an example, see [this forum post](https://forum.duet3d.com/post/136207). Ideally, configure heaters on consecutive heater connections.
* Due to constraints on display resolution, PanelDue can only display 7 heaters in total on 5" and 7" panels, and 5 on 4.3" panels. If there are more heaters and/or tools than this, some columns will overlap. 
* PanelDue does not recognise Chamber heaters, or extra heated beds. It will show them as heaters, though.

These restrictions are largely removed in later versions of the PanelDue firmware. However, they will require you to update RepRapFirmware on your Duet mainboard.

# Configuring the display

Display options, eg show multiple heaters/tools. To do.

# Using the external SD card socket on the LCD panel

> Caution! Do not use an SD extender cable from the SD socket on the Panel Due. Some types of SD card extender cable have been found to damage the SD card socket. **Damage to the SD card socket from using an extender cable is not covered by the warranty.**
{.is-warning}

**This information also applies to a stand alone SD or micro SD card socket**

You can use the external SD card socket on the LCD panel if you have used a ribbon cable as described above. Please note, the SPI interface provided by this SD card socket is much slower than the on-board SD card socket built into the Duet. Therefore we recommend that you do not upload files to this card over the network. Use the external SD card socket only if you want to write files to the SD card on a PC and then move the SD card to your printer.

To access the second SD card socket, you will need these firmware versions:

* DuetWiFiFirmware/RepRapFirmware 1.16 or later
* DuetWebControl 1.13 or later
* PanelDue firmware 1.15c or later

## Using the SD card socket with other PanelDue version and Duet version combinations

You will need to make a custom 5-way cable using this table of connections. For the PanelDue 1.1, the X5 connector pins are numbered from the bottom end of the connector (the end close to the X5 legend).

| SD signal name | PanelDue 1.1 X5 pin # | PanelDue 2.0 X5 pin # | Duet 2 signal name | Duet 2 CONN_SD pin # |
|:---|:---|
| Ground | 1 | 2 | Ground | 2 |
| DO | 2 | 6 | SPI0_MISO | 6 |
| CLK | 3 | 4 | SPI0_SCK | 4 |
| DIN | 4 | 5 | SPI0_MOSI | 5 |
| CS | 5 | 3 | SPI0_CS0 | 3 |