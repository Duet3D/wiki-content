---
title: Connecting external stepper and servo motor drivers
description: 
published: true
date: 2022-05-04T15:34:00.297Z
tags: 
editor: markdown
dateCreated: 2021-10-05T15:57:39.091Z
---

# Introduction

If your motors are rated above the current limit of your Duet, or if they need higher voltage than the Duet can provide, then you need external stepper motor drivers. These generally have optically isolated step/dir/enable inputs. For example, stepper motor drivers rated at up to 5A using the TB6600 stepper driver chip are widely available on eBay. External stepper drivers are also available to drive large servo motors, and these can usually be controlled with step/direction pulses from Duet board.

Duet3D sell expansion boards to connect external drivers to Duet mainboards; see Connection options below. This page mainly deals with connecting an external stepper driver to the external driver pins (Duet 3 Mini 5+, Duet 2 Maestro) or the expansion connector or CONN_LCD pins (Duet 2 WiFi/Ethernet).

See the linked pages for connecting external stepper/servo drivers to [Duet 3 Mainboard 6XD](https://docs.duet3d.com/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6XD_Hardware_Overview), [Duet 3 Expansion 1XD](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1XD) and for connecting stepper motors with an encoder for position feedback with the [Duet 3 Expansion 1HCL](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL).

## Duet on-board stepper driver limits

Ideally, motors should be run at between 60% and 85% of their rated current. The table below shows the maximum current and voltage each Duet board can supply to its on-board drivers.

| | Max motor current | Max motor voltage | 
|---|---|
| Duet 3 Mainboard 6HC | 6.3A | 32V | 
| Duet 3 Mainboard 6XD | NA | NA | 
| Duet 3 Mini 5+ | 2.0A | 25V | 
| Duet 2 WiFi/Ethernet | 2.4A | 25V | 
| Duet 2 Maestro | 1.6A | 25V | 

# Connection options

There are a number of options available to connect external stepper/servo drivers.

| | Pins for direct connection | Provision for external drivers |
|---|---|
| [Duet 3 Mainboard 6HC](https://docs.duet3d.com/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6HC_Hardware_Overview) | None | CAN bus |
| [Duet 3 Mainboard 6XD](https://docs.duet3d.com/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6XD_Hardware_Overview) | Headers for six external drivers with 5V signalling (no on-board drivers) | CAN bus |
| [Duet 3 Mini 5+](https://docs.duet3d.com/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Hardware_Overview) | External driver pins (2 drives, 3V signalling) | CAN bus |
| [Duet 2 WiFi/Ethernet](https://docs.duet3d.com/Duet3D_hardware/Duet_2_family/Duet_2_WiFi_Ethernet_Hardware_Overview) | Expansion connector (5 drives, 3V signalling), CONN_LCD (2 drives, 3V signalling) | [DueX2/5](https://docs.duet3d.com/Duet3D_hardware/Duet_2_family/DueX2_and_DueX5) (3 drives, 3V signalling), [Duet Expansion Breakout Board](https://docs.duet3d.com/Duet3D_hardware/Duet_2_family/Duet_Expansion_Breakout_Board) (5 drives, differential signalling -3.6V to +3.6V, or 5V) |
| [Duet 2 Maestro](/Duet3D_hardware/Duet_2_family/Duet_2_Maestro) | External driver pins (2 drives, 3V signalling) | None |

There are two options for Duet 3 CAN-connected expansion boards:

| | Provision for external drivers |
|---|---|
| [Duet 3 Expansion 1XD](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1XD) | Single-ended or differential 5V signalling for one external driver. Multiple 1XD boards can be daisy-chained to support as many external drivers as you need. |
| [Duet 3 Expansion 1HCL](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL) | Stepper driver (6.3A peak, 4.45A RMS) driving a single stepper motor (up to 48V) with encoder. |

# Tabs {.tabset}

## Duet 3 Mainboard 6HC

The **Duet 3 Mainboard 6HC** has no on-board external driver pins. Use a CAN bus connected expansion board to provide signalling for the external driver, such as the [Duet 3 Expansion 1XD](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1XD) or [Duet 3 Expansion 1HCL](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL).

## Duet 3 Mini 5+

If the drivers require no more than about 2mA @ 3V on the step and dir inputs (there is no enable signal), then you can drive them directly from the **Duet 3 Mini 5+** external driver pins. See the [Duet 3 Mini 5+ wiring diagram](/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Hardware_Overview#wiring-diagram) for pin locations.

Otherwise, use a CAN bus connected expansion board to provide signalling for the external driver, such as the [Duet 3 Expansion 1XD](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1XD) or [Duet 3 Expansion 1HCL](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL).

## Duet 3 6XD

The Duet 3 6XD supports up to 6 external drivers, see the [Duet 3 6XD Documentation](Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6XD_Hardware_Overview) for more details. Additional CAN bus connected expansion boards can provide signalling for further external drivers; see [Duet 3 Expansion 1XD](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1XD) or [Duet 3 Expansion 1HCL](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL).

## Duet 2 WiFi/Ethernet

If the drivers require no more than about 2mA @ 3V on the step, dir and enable inputs, then you can drive them directly from the **Duet 2 WiFi/Ethernet** Expansion header or CONN_LCD header. The Expansion header has support for 5 external drivers. The CONN_LCD header supports two external drivers.

You can also connect external drivers to the [Duet Expansion Breakout Board (EBoB)](/Duet3D_hardware/Duet_2_family/Duet_Expansion_Breakout_Board) or [DueX2 or DueX5](/Duet3D_hardware/Duet_2_family/DueX2_and_DueX5). You can also connect both of the above boards at the same time, using a ribbon cable with a connector in the middle; most of the signals are not connected on the EBoB.

Refer to the [Duet 2 WiFi and Ethernet wiring diagram](/Duet3D_hardware/Duet_2_family/Duet_2_WiFi_Ethernet_Hardware_Overview#wiring-diagram) for pinout diagram.

![motor_external_01.png](/manual/motors/motor_external_01.png =336x)
![motor_external_02.png](/manual/motors/motor_external_02.png =381x)

| Driver | Location | Axis name | Endstop pin | Step pin | Direction pin | Enable pin |
|---|---|
| 5 | Expansion header | E2 | 4) E2_STOP | 5) E2_STEP | 6) E2_DIR | 7) E2_EN |
| 6 | Expansion header | E3 | 9) E3_STOP | 10) E3_STEP | 11) E3_DIR | 12) E3_EN |
| 7 | Expansion header | E4 | 14) E4_STOP | 15) E4_STEP | 16) E4_DIR | 17) E4_EN |
| 8 | Expansion header | E5 | 19) E5_STOP | 20) E5_STEP | 21) E5_DIR | 22) E5_EN |
| 9 | Expansion header | E6 | 24) E6_STOP | 25) E6_STEP | 26) E6_DIR | 27) E6_EN |
| 10 | CONN_LCD | 10 | 3) Stop 10 (ENC_B) | 9) Stp 10 (LCD_DB5) | 7) Dir 10 (LCD_DB6) | 5) En 10 (LCD_DB7) |
| 11 | CONN_LCD | 11 | 4) Stop 11 (ENC_A) | 10) Stp 11 (LCD_DB4) | 8) Dir 11 (LCD_E) | 6) En 11 (LCD_RS) |

You can pick up +3.3V and ground from the Expansion connector and/or CONN_LCD.

**Important! External drivers must always be connected to the expansion connector of the Duet or to the CONN_LCD port.** The 3-pin pads on the Duet near to the stepper drivers are test pads and are not suitable for connecting external drivers.

## Duet 2 Maestro

If the drivers require no more than about 2mA @ 3V on the step, dir and enable inputs, then you can drive them directly from the **Duet 2 Maestro** external driver pins. See the [Duet 2 Maestro wiring diagram](/Duet3D_hardware/Duet_2_family/Duet_2_Maestro#wiring-diagram) for pin locations.

One forum user created an external driver board for Maestro, see [this forum thread](https://forum.duet3d.com/topic/10314/).

# Connecting stepper drivers

## Connecting external stepper drivers with differential inputs

These stepper driver modules generally have optically isolated inputs labelled STEP+, STEP-, DIR+, DIR-, ENA+ and ENA-.

If they require no more than about 2mA @ 3V on the step, dir and enable inputs, then you can drive them directly from the expansion connector of the Duet. See the expansion connector pinouts above. Connect STEP+ and DIR+ to your chosen Step and Dir pins on the expansion connector or CONN_LCD, and connect STEP- and DIR- to ground. Connect ENA+ to +3.3V and connect ENA- to your chosen Enable pin.

If you need 5V signalling, you can use 3.3V to 5V level shifting ICs such as 74HCT04 to boost the signal level to 5V and drive them. This is what the [Duet Expansion Breakout Board (EBoB)](/Duet3D_hardware/Duet_2_family/Duet_Expansion_Breakout_Board) does.

## Connecting Pololu-style drivers

![motor_external_09.jpg](/manual/motors/motor_external_09.jpg =300x) 

These come in with a variety of different stepper driver chips, but generally have a standard pinout. These take single-ended STEP, DIR and EN (step, direction and enable) signals. For example the popular http://reprap.org/wiki/StepStick has the following pin out:

![motor_external_03.jpg](/manual/motors/motor_external_03.jpg =300x)

Even though the stepsticks are nominally 5V boards, nearly all stepper driver chips are specified for 3.3V operation too.

# Examples

# Tabs {.tabset}

## Stepper driver breakout boards

A simple way of connecting an external is to use a stepper driver breakout board such as [this one](http://hobbycomponents.com/motor-drivers/892-breakout-board-for-a4988-stepper-motor-driver){target=_blank}. 

Wire as follows: 
* 3.3V and GND (Note: The pin labelled 5V on the breakout board must be connected to 3.3V on the Duet, not 5V!)
* 12V/24V (vin) and GND
* ESDG wires (Enable, Step, Direction, Ground)

**Note:** It’s strongly recommend clearly marking your wires, so as not to short out something or otherwise plug them in improperly!

[![motor_external_10.jpg](/manual/motors/motor_external_10.jpg =500x)](/manual/motors/motor_external_10.jpg){target=_blank}

Next, install the Drivers to the breakout board

* The orientation of the drivers is really important. You are going to fry the drivers if they are inserted incorrectly. Remember to always have all power turned off, that includes any USB, whenever you handle the drivers - aside from adjusting the vref.
* The A4988 is inserted with trimpot facing away from dipswitches.
* The DRV8825 is inserted with trimpot facing towards the dipswitches.
* SilentStepStick TMC2100 is inserted with trimpot facing towards the dipswitches.

[![motor_external_11.jpg](/manual/motors/motor_external_11.jpg =500x)](/manual/motors/motor_external_11.jpg){target=_blank}

Measure vRef - good practice

* This is not about what value to set it at, but about a good practice to avoid accidents. Use 2 clamps on your Multimeter.
* Connect the black one to a GND on your printer. Easy to use is directly from PSU or from a GND from endstop pins.
* Attach the red one to a small screw-driver fitting for your trimpot.
* This way you have your hands free to actually adjust the trimpot and not having to hold multiple wires and worry about something slipping and making a short circuit somewhere:

[![motor_external_12.jpg](/manual/motors/motor_external_12.jpg =500x)](/manual/motors/motor_external_12.jpg){target=_blank}

## External driver using RAMPS board

This example shows how to add one external stepper driver to drive a third extruder on a Duet 2 Wifi.

At the Duet 2 Wifi end, connections are made to the GND, +3.3V, E2_STEP, E2_DIR and E2_EN pins on the expansion connector (see table and pinout diagram earlier). A custom cable is made up that connects these pins to the Stepstick.

[![motor_external_04.png](/manual/motors/motor_external_04.png =300x)](/manual/motors/motor_external_04.png){target=_blank}

In this example the Stepstick is mounted on on a RAMPS 1.3 pcb, just adding the minimal components needed to support 1 more driver. It was much quicker to use a RAMPS pcb that was spare than to use stripboard to make a custom stepper driver carrier for the stepstick driver, however that is all that the pcb is doing so directly connecting to the stepstick or using a stripboard will also work. It is important to connect a bulk capacitor of ~100uF between power and ground close to the stepstick.

At the RAMPS pcb end:

[![motor_external_05.png](/manual/motors/motor_external_05.png =300x)](/manual/motors/motor_external_05.png){target=_blank}

And the RAMPS PCB with the other components fitted:

[![motor_external_06.png](/manual/motors/motor_external_06.png =300x)](/manual/motors/motor_external_06.png){target=_blank}


## TMC2130 Example

[![motor_external_07.png](/manual/motors/motor_external_07.png =300x)](/manual/motors/motor_external_07.png){target=_blank}

It is possible to use Trinamic stepper drivers in the so called "standalone mode". Since as for now the logic allowing to control the SPI features are not implemented one needs to configure the stepper driver with the CFG pins, those are the pins that would otherwise be used for the SPI interface and are configured with different states: pull up (to VIN), pull down (to GND) or open.

Refer to [this wiring diagram](https://www.trinamic.com/fileadmin/assets/Products/Eval_Drawings/SilentStepStick-TMC2130_v10.pdf){target=_blank} to identify the CFG pins and follow the tables "CFG0", "CFG1 and CFG2" and "CFG3  to set the desired mode [pages 85-86 of TMC2130 data sheet](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC2130_datasheet_Rev1.11.pdf){target=_blank}.

**Notice that the order from the top is CFG0, CFG3, CFG2, CFG1**

**CFG0** - controls the *TOFF setting* which can help with noise reduction, to achieve the recommended by the datasheet setting this pin needs to be pulled down. On a step stick breakout board like the one [from the picture](https://www.aliexpress.com/item/32838869245.html){target=_blank}  that pin in open, so has to be pulled down to ground, a pin can be soldered up on the step stick.

**CFG1 and CFG2** - those two allows to choose the mode of operation - between the StealthChop and SpreadCycle, microsteps and step interpolation. **Note that the CFG1 pin is most likely pulled down to ground on your breakout board** (with a 10k ohm resistor).

**CFG3** - allows to specify the mode of current setting. An open pin allows to set the reference voltage (Vref) with the potentiometer.

There's also the the SPI, CFG4 and CFG5 pins that are on the bottom of the stick. Recommended settings for the CFG4 is pull down to ground and pull up for CFG5. The SPI pads need to be soldered together to disable the SPI mode in favor of the configuration by the CFG pins. The following picture shows how those can be soldered (if there is the need for that - some will already be bridged correctly with 0 ohm resistors).

![motor_external_08.png](/manual/motors/motor_external_08.png =300x)

### Noise reduction

If noise is reduced then there are different settings to consider for the CFG pins. 24V power supply (when compared to 12V) reduces noise considerably on it's own. Page 82 of the linked datasheet provides more information about that.


# Firmware configuration

## Remapping drivers

To use external drivers to drive an axis instead of an internal driver, you need to remap the axis to the external driver using the [M584](/User_manual/Reference/Gcodes/M584) command. The drive numbers used in G-code correspond to the following driver labels on the board(s):

| Drive number | Duet 3 board label | Duet 2 board label ||
| | Mini 5+ | WiFi/Ethernet | Maestro |
|:---|:---|
| 0 | DRIVER_0 | X ||
| 1 | DRIVER_1 | Y ||
| 2 | DRIVER_2 | ZA ZB (Two headers wired in series) ||
| 3 | DRIVER_3 | E0 ||
| 4 | DRIVER_4 | E1 ||
| 5 | DRIVER_5 (pins for external driver)  | E2 (On Expansion header, DueX2/5 or EBoB) | E2 (pins for external driver) |
| 6 | DRIVER_6 (pins for external driver) | E3 (On Expansion header, DueX2/5 or EBoB) | E3 (pins for external driver) |
| 7 | | E4 (On Expansion header, DueX5 or BoB) |  |
| 8 | | E5 (On Expansion header, DueX5 or BoB) |  |
| 9 | | E6 (On Expansion header, DueX5 or BoB) |  |
| 10 | | On LCD_CONN header |  |
| 11 | | On LCD_CONN header |  |

## Configuring the Enable polarity

Only relevant for Duet 3 MB6XD, Duet 3 Expansion 1XD and Duet 2 WiFi/Ethernet/Maestro. All other boards do not have enable signals.

**Duet 3 Mainboard 6XD:** See [Duet 3 Mainboard 6XD](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6XD_Hardware_Overview) documentation.

**Duet 3 Expansion 1XD:** See [Duet 3 Expansion 1XD](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1XD) documentation.

**Duet 2:** The Enable signals are active low by default but you can change this using the R parameter in the [M569](/User_manual/Reference/Gcodes/M569) command. 
```
M569 P5 R1 ; driver 5 requires an active high enable
```

## Configuring the step timing

Refer to the documentation for your stepper driver to determine settings for step timing. The default step timing used for the onboard drivers may be too fast for your external driver. You can set a minimum step pulse width and other timings in the [M569](/User_manual/Reference/Gcodes/M569) command using the T parameter, and configure the direction with the S parameter.

**Taa:bb:cc:dd** Minimum driver step pulse width, step pulse interval, direction setup time and direction hold time, in microseconds

```
M569 P5 R1 T5:5:10:10 ; driver 5 requires an active high enable, 5us minimum step pulse, 5us minimum step interval, 10us DIR setup time and no hold time
```

> Note that microstepping mode (M350) and driver currents (M906) is not controlled by firmware configuration; it is set by the external stepper driver. Steps per mm (M92), speeds (M566, M203) and acceleration (M201) are controlled by the firmware. 
See [Configuring stepper motors](/User_manual/Connecting_hardware/Motors_configuring) for examples.
{.is-info}
