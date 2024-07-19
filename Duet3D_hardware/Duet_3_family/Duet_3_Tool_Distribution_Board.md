---
title: Duet 3 Tool Distribution Board
description: A board to make wiring multiple Duet 3 Toolboards easy.
published: true
date: 2024-07-19T14:14:09.057Z
tags: 
editor: markdown
dateCreated: 2021-07-14T14:17:03.874Z
---


![duet_3_tool_distribution_board_v0.5_top.jpg](/duet_boards/duet_3_can_expansion/duet_3_tool_distribution_board_v0.5_top.jpg =400x)

# Introduction

The Duet 3 Tool Distribution Board is designed to simplify the connections of multiple [Duet 3 Toolboard 1LC](/Duet3D_hardware/Duet_3_family/Duet_3_Toolboard_1LC)s to the Duet 3 CAN-FD bus by providing breakout for CAN and power, bus pass-through and bus termination.

See [CAN connection basics](/User_manual/Machine_configuration/CAN_connection).

# Features

* 2-way barrier strip for power in.
* Four 4-pin JST ZH connectors to connect CAN bus to Tool Boards.
* Four 2-way JST VH connectors for power out to tool boards, individually fused (5A fuses supplied).
* Two RJ11 connectors for CAN bus in/out to connect to Duet 3 main board and expansion boards.
* CAN bus termination jumper pins.

The Duet 3 Tool Distribution Board is supplied with:
* Four 4-pin JST ZH connectors with 1m cables to connect CAN bus to four Tool Boards. The cables supplied are 28AWG.
* Four pairs of 2-pin jumpers to bypass unused tool board CAN connectors.
* CAN bus termination jumper, for use if the CAN_OUT RJ11 port is not used.

# Physical properties

## Dimensions

## Tabs {.tabset}

### Revision v0.5

[![duet_3_tool_distribution_board_v0.5_dimensions.png](/duet_boards/duet_3_can_expansion/duet_3_tool_distribution_board_v0.5_dimensions.png =500x)](/duet_boards/duet_3_can_expansion/duet_3_tool_distribution_board_v0.5_dimensions.png){target=_blank}

### Revision v0.4

[![duet_3_tool_distribution_board_v0.4_dimensions.png](/duet_boards/duet_3_can_expansion/duet_3_tool_distribution_board_v0.4_dimensions.png =500x)](/duet_boards/duet_3_can_expansion/duet_3_tool_distribution_board_v0.4_dimensions.png){target=_blank}

# Physical connections

## Wiring diagram

[![duet_3_tool_distribution_board_v0.5_wiring.png](/duet_boards/duet_3_can_expansion/duet_3_tool_distribution_board_v0.5_wiring.png =500x)](/duet_boards/duet_3_can_expansion/duet_3_tool_distribution_board_v0.5_wiring.png){target=_blank}

## Power distribution

Supply between 12V and 32V to the 2-way barrier strip power connector on the board, observing the correct polarity.

Use the four 2-way JST VH connectors for power out to tool boards. Each output is individually fused, with a supplied 5A fuse.

> If you use a relay to control VIN power to the board, ie the power supply is already switched on, and a relay is used to turn on power to the board, you should use an inrush current limiter wired in series with VIN. See the [section on Inrush current here](https://docs.duet3d.com/en/User_manual/Connecting_hardware/Power_choosing#inrush-current){target=_blank}.
>
> OUT ports on the mainboard should NOT be used to switch power to expansion or tool boards directly. See the note at the end of the 'inrush current' section at the link above.  
{.is-info}

# CAN wiring

Also see [CAN connection basics](/User_manual/Machine_configuration/CAN_connection){target=_blank}

The Tool Distribution Board provides flexibility in extending the CAN bus, offering a number of different ways to connect a range of two- and four-wire CAN bus expansion and tool boards.

The Tool Distribution Board does this by extending the CAN bus internally. 

[![duet_3_tool_distribution_board_can_diagram.png](/duet_boards/duet_3_can_expansion/duet_3_tool_distribution_board_can_diagram.png =500x)](duet_boards/duet_3_can_expansion/duet_3_tool_distribution_board_can_diagram.png){target=_blank}

## Connecting expansion and tool boards

Tool Distribution Board supplies convenient places to connect either a loop in the CAN bus (4-wire connection) or a stub (2-wire connection). Connect expansion and tool boards by:

### 4-wire connection

[![duet_3_tdb_can_wiring_01.jpg](/duet_boards/duet_3_can_expansion/duet_3_tdb_can_wiring_01.jpg =500x){.align-right}](/duet_boards/duet_3_can_expansion/duet_3_tdb_can_wiring_01.jpg){target=_blank}Originally, the Tool Distribution Board was developed to provide an easy way to connect [Duet 3 Toolboard 1LC](/Duet3D_hardware/Duet_3_family/Duet_3_Toolboard_1LC) boards. The supplied cables connect directly to that board, but can be adapted to connect other 4-wire boards. To connect:
* Remove the CAN bypass jumpers from the selected Tool output
* Connect the cable between the Tool Distribution Board and tool board
* Of the four wires, one pair carries the CAN bus to the tool board, and the other returns from the board, to loop on to the next tool output.
* Make sure CAN_H wires connect to CAN_H pins only, and CAN_L wires connect to CAN_L pins only.

<p style="clear:both"></p>

[![duet_3_tdb_can_wiring_02.jpg](/duet_boards/duet_3_can_expansion/duet_3_tdb_can_wiring_02.jpg =500x){.align-right}](/duet_boards/duet_3_can_expansion/duet_3_tdb_can_wiring_02.jpg){target=_blank}As the Tool Distribution Board is just providing a convenient place to connect a loop in the CAN bus, multiple boards can be connected in each loop from the board:
* Remove the CAN bypass jumpers from the selected Tool output
* Connect the cable between the Tool Distribution Board and multiple boards
* The CAN loop extends from the CAN_OUT pair of pins of the Tool Distribution Board, through each  expansion board, and back to the CAN_IN pair of pins.
* Make sure CAN_H wires connect to CAN_H pins only, and CAN_L wires connect to CAN_L pins only.

### 2-wire connection

To do

[![duet_3_tdb_can_wiring_03.jpg](/duet_boards/duet_3_can_expansion/duet_3_tdb_can_wiring_03.jpg =500x){.align-right}](/duet_boards/duet_3_can_expansion/duet_3_tdb_can_wiring_03.jpg){target=_blank}Expansion boards as stubs

<p style="clear:both"></p>

[![duet_3_tdb_can_wiring_04.jpg](/duet_boards/duet_3_can_expansion/duet_3_tdb_can_wiring_04.jpg =500x){.align-right}](/duet_boards/duet_3_can_expansion/duet_3_tdb_can_wiring_04.jpg){target=_blank}Expansion boards as stubs


### Unused connection

If no expansion or tool board is to be connected to a tool connector, leave the CAN bypass jumpers in place. This allows the CAN bus to continue on to the next tool connector, or the CAN OUT RJ11 connector.

## Termination

CAN bus termination is required at each end of the CAN bus. The mainboard, usually at one end, supplies the termination there. So long as the continuity of the bus is maintained through the Tool Distribution Board (ie with bypass jumpers or wiring tool/expansion board with a 4-wire connection),  the CAN bus can be terminated on the Tool Distribution Board.

Alternatively, you can put the termination on the last toolboard on the Tool Distribution Board.

If there are further CAN expansion boards connected via the RJ11 CAN OUT connector, remove the termination jumper from the Tool Distribution Board. 
