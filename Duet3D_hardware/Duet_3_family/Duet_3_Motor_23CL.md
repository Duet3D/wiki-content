---
title: Duet 3 Expansion 1HCL
description: A range of CAN-FD connected closed loop NEMA 23 motors for Duet 3 ecosystem.
published: false
date: 2023-01-12T16:34:36.473Z
tags: 
editor: markdown
dateCreated: 2023-01-09T19:18:18.412Z
---

![Photograph of a Duet 3 Motor 23CL shown at an agle with the M8 connectors for power and CAN-FD coming out the top back of the motor](/duet_boards/duet_3_can_expansion/motor23cl/23cl_proto_s.png =500x)

# Introduction

The Duet 3 Motor 23CL (M23CL) is a family of Closed loop, FAN-FD conncted Nema23 motors, fully integrated in to the Duet 3 ecosytem. They optionally integrate a brake to hold the motor in postion when power is off. Connection to the Duet 3 CAN-FD bus and power use industial M8 connectors. Multiple M23CLs can be connected to the bus, either via an M8 Y splitter or a distibution box.

Note the M23CL series are in active development and this documentation will be expanded as we confirm speicifics of each motor variant, along with bus distribution options.


# Features

## Hardware specification

| | M23CL-56-2800B | M23CL-76-2800B
|---|---|---|
| **Motor Option** | Microchip ATSAME51G19A |
| **Processor** | Microchip ATSAME51G19A |
| **Processor features** | 32-bit, 120MHz ARM Cortex-M4F, 512Kb flash, 192Kb RAM, hardware single precision floating point unit |
| **Networking/Comms** | CAN-FD BUS for connection to the Duet 3 Mainboard. Optional on-board CAN bus termination. |
| **On-board stepper driver** | 1 x [TMC2160A](https://www.trinamic.com/products/integrated-circuits/details/tmc2160-ta/){target=_blank} |
| **Stepper driver features** | SPI controlled, can be run in open loop or closed loop mode. Maximum motor current 6.3A peak per phase (4.45A RMS). |
| **Encoder Inputs** | Quadrature or SPI bus |
| **Thermistor/PT1000 inputs** | 2 x thermistor/PT1000 inputs. This is intended to allow for motor temperature monitoring, potentially coupled with a cooling system controlled by one of the outputs. |
| **Medium current outputs** | 2 x medium-current (2.5A max recommended) outputs at V_FUSED or V_BRAKE voltage, with PWM capability and built-in flyback diodes. Optionally provide V_BRAKE at a different voltage from V_FUSED, to allow (for example) running the stepper motor at 48V and the brake at 24V. |
| **Inputs/Outputs** | 2 x 3.3V-level PWM capable output (3mA max), 2 x digital inputs, protected against over-voltage. Example use: endstop switches. |

## Operating limits

|:--|:--|
| **Input voltage** | 12V to 50V |
| **VIN connector rated current** | 25A maximum, or fused limit (whichever is lower) |
| **Fuses** | 5A for V_FUSED (max 10A), 5A for V_BRAKE |
| **Stepper driver** | Up to 6.3A peak current per phase (4.45A RMS per phase; max. standstill current 4.45A) |
| **Medium current outputs** | OUT0/1 up to 2.5A each |
| **Inputs/Outputs** | Inputs are 30V-tolerant |
| **12V current limit** | 200mA |
| **5V and 3.3V current limit** | 100mA total on 5V and 3.3V |

## Compatible motors

Use motors with 1.8 or more degrees per step. **Do not use 0.9deg motors**. The positioning accuracy depends on the resolution of the encoder, not on the degrees/step of the motor.

The maximum speed at which the firmware can drive the motor reliably in closed loop mode is about 5000 full steps/second.

## Compatible Encoders

RRF 3.4 supports quadrature motor shaft encoders only. RRF 3.5 also supports Duet3D magnetic shaft encoders and linear composite encoders.

### Quadrature motor shaft encoders

Stepper motors can be purchased with integral optical shaft encoders. It is also possible to buy add-on quadrature shaft encoders, which are typically mounted on the back of dual-shaft stepper motors. The resolution is specified in counts or pulses per motor revolution. **There must be an integer number of output pulses from the encoder per 4 full steps**. For example, a 1.8deg/step motor (200 full steps/rev) could have an encoder with 1000cpr (20 pulses per 4 full steps), 2000 cpr (40 pulses per 4 full steps) or 2500 cpr (50 pulses per 4 full steps).

### Duet3D magnetic motor shaft encoders

The Duet3D magnetic shaft encoder is a small board that mounts on the back of the stepper motor. It is supplied with a diametrically-magnetised disc magnet, which must be glued to the centre of the end of the shaft at the back of the motor. A jig should be used to centre the magnet accurately while the glue sets.

### Linear composite encoder

A linear composite encoder comprises a linear quadrature encoder that tracks position on a linear axis (for example the Renishaw LM10IA or LM10IB) and a Duet3D magnetic shaft encoder. The shaft encoder handles motor commutation, the linear encoder is used to close the loop.

## Firmware notes

* Compatible RepRapFirmware versions: RRF 3.4 and later
* Firmware limitations: See [Duet 3 with CAN expansion firmware configuration limitations](/User_manual/RepRapFirmware/CAN_limitations).

## Open source

Importantly Duets are Open:

* The Duets are Open Hardware, see [our license here](https://github.com/Duet3D/Duet3-Expansion-1HCL/blob/main/LICENSE).
* All hardware [source files](https://github.com/Duet3D/Duet3-Expansion-1HCL/) are available on Github.
* The Duet hardware and RepRapFirmware are built with Open tools: designed in [KiCad](http://kicad.org/) and [Eclipse](https://eclipse.org/) using open tools means the barrier to getting involved is as low as possible.

# Physical properties

## Dimensions

[![Image showing the key dimensions of the Duet 3 1HCL](/duet_boards/duet_3_can_expansion/duet_3_1hcl/duet3_eb_1hcl_v1.0_d1.0_dimensions.png  =500x)](/duet_boards/duet_3_can_expansion/duet_3_1hcl/duet3_eb_1hcl_v1.0_d1.0_dimensions.png){target=_blank}

## 3D model

A STEP 3D model of the Duet 3 Expansion 1HCL is available [on github](https://github.com/Duet3D/Duet3-Expansion-1HCL/blob/main/v1.0/Duet3_Exp_1HCL.step){target=_blank}.

# Wiring

## Wiring Diagram

[![Image showing all the connections on a Duet 3 1HCL to aid wiring](/duet_boards/duet_3_can_expansion/duet_3_1hcl/duet3_eb_1hcl_v1.0_d1.0_wiring.png =600x)](/duet_boards/duet_3_can_expansion/duet_3_1hcl/duet3_eb_1hcl_v1.0_d1.0_wiring.png){target=_blank}

# Encoders

## Quadrature encoder

The 1HCL supports a quadrature encoder connected to the Quadrature Input interface. This works with common 5V, 1000CPR-2500CPR optical encoders that are frequently supplied with closed loop stepper motors.   (One example is the [17E1K-05](https://www.omc-stepperonline.com/p-series-nema-17-closed-loop-stepper-motor-48ncm-67-99oz-in-with-encoder-1000ppr-4000cpr-17e1k-05)), however there are many other examples).

### Connecting a Quadrature Encoder

Quadrature encoders have either a differential output (often shown as A+,A-, B+,B-,N+,N- or as A,A',B,B',N,N') or a single ended output.

*note the index signal is not currently used*

If the encoder has a single ended output the signal lines connect to the 1HCL input. A to A_INPUT, B to B_INPUT . 5V or VCC to the +5V and ground to ground. The Z or N can be left disconnected.

If the encoder has a differential output then connect the A+, B+ to the signal inputs on the 1HCL. 5V/VCC to 5V and ground to ground. The A-,B- and Z+Z-/N+N- can be left disconnected.

Here is a picture (courtesy of LDO motors) which shows a single ended and differential encoder output:

![Image showing both single ended and differential encoder connection schemes](/duet_boards/duet_3_can_expansion/duet_3_1hcl/duet_3_1hcl_encoders_01.png =600x)

## Magnetic Encoder

*This support is still experimental in RRF 3.4*

The 1HCL supports SPI connected encoders (initially just the AS5047D sensing an on motor shaft magnet will be supported). Duet3D will supply this encoder on a Nema17 form factor PCB, designed to sense a diametrically magnetised magnet glued to the back of the motor shaft.

*More details to follow, including mounting pictures once further testing is completed*

# Commissioning

See [CAN connection basics](/User_manual/Machine_configuration/CAN_connection)

All boards in the system must have different CAN addresses. 1HCL boards are shipped set to a default CAN address of 123. They will also revert to 123 if you use the jumper to force the bootloader to request new firmware. Therefore, if you have more than one new 1HCL board, only one of them must be powered up and connected to the CAN bus. So disconnect power to all but one of them (you can leave the CAN bus connected if it's easier). When you have changed the CAN address of that board, you can connect the next one; and so on.

## Startup Time

It is recommended to add the following to config.g, before any commands that reference any CAN bus connected expansion boards

`G4 S1   ;wait for expansion boards to start`

## Testing communication

Check that you can communicate with the 1HCL board, by sending:

`M115 B123`

If that fails try placing a jumper on the CAN_RST pins and powering up, then power down and remove the jumper before powering up again, this will reset the CAN-FD bus settings to the default (address 123, bus speed 1Mbps)

## Update the bootloader

Duet 3 expansion boards and tool boards have a bootstrap loader written to the start of flash so that they can load firmware from the main board via CAN. This bootloader may occasionally need to be updated in order to support new features. See [Updating the bootloader on Duet 3 expansion and tool boards](/User_manual/RepRapFirmware/Updating_bootloader).

## Updating the firmware

The 1HCL board will be shipped with firmware loaded during production. You can  check the version loaded by sending

`M115 B123`

(or B## where ## is the new CAN address of the board if you have changed it already)

To update the firmware get the [latest version from the RepRapFirmware github.](https://github.com/Duet3D/RepRapFirmware/releases) It is recommended to upgrade all the firmware in your Duet 3 system together so that the versions do not get out of sync.

Send M997 B## to carry out a firmware update, the bootloader will request the Duet3Firmware_EXP1HCL.bin from the Duet 3 main board, it needs to be in the /firmware folder.

## Set the CAN address

* Send command M115 B## to verify that the main board can communicate with the 1HCL board, where ## is the default address of 123 if it has not been changed already.
* Send command M952 B# A## where ## is the new address you want to use. Allowed CAN addresses for normal use are 1 to 119. We suggest you use addresses starting at 50 for 1HCLs. So for the first 1HCL board, if your new CAN board was at address 123, send M952 B123 A50.
* Power the system down and up again, or send M999 B123. This will cause the 1HCL board to restart with the new address.
* Send command M122 B50 (or whatever address you chose) to verify that you can communicate with the 1HCL board at its new address
* You can now power up the next 1HCL board and commission it in the same way, **choosing a different CAN address for it**.

# Firmware

The default CAN address is 123. It can be changed as described above.

## Limitations

Please see the current RepRapFirmware limitations at [Duet 3 firmware with CAN expansion configuration limitations](/User_manual/RepRapFirmware/CAN_limitations).

## Microstepping
While in closed loop mode step pulses are not sent to the stepper motor driver in the same manner as an open loop driver, however the firmware still uses microsteps internally to represent moves.

To that end the microstep setting for the 1HCL should be high enough to use the full encoder CPR. e.g. if the encoder is 1000 PPR (so 4000 CPR) and the full steps/rev of a 1.8 degree/step motor is 200, then the microstepping needs to be at least 4000/200 = 20 to make use of the full resolution of the encoder.

Microsteps must be set in powers of 2, in the same manner as open loop drivers (1,2,4,8,16,32,64,128,256)

So in the case of a 4000CPR encoder on a 1.8 degree/step motor microstepping should be set to 32. Note steps/mm should be adjusted to match the microstep setting chosen.

# Sample configuration examples

**CAUTION** before using these examples check the datasheet and user manual of the motor, encoder (and optionally brake) you are using. Especially: check compatibility of signal voltages.

## Adding a closed loop motor

[M569.1](/User_manual/Reference/Gcodes/M569_1) is used to configure the closed loop driver.

Two general types of encoder can be used for feedback:

* A quadrature encoder connected to the Quadrature Input interface. This works with common 5V optical encoders that are frequently supplied with closed loop stepper motors.
* An SPI connection that can communicate with supported encoders that communicate over SPI. Initially this is the AS5047D encoder sensing a magnet on the motor shaft. In the future other SPI encoders may be supported.

Here's an sample excerpt from a config.g file for RRF 3.4 to drive the X and Y motors from 1HCL  boards configured at CAN addresses 50 and 51, with quadrature encoders. **Note, some parameters have changed in RRF 3.5**.

```
M569.1 P50.0 T2 C5 R100 I0 D0 ; Configure the 1HCL board at CAN address 50 with a quadrature encoder on the motor shaft that has 5 steps per motor full step. 
M569.1 P51.0 T2 C5 R100 I0 D0  ; Configure the 1HCL board at CAN address 51 with a quadrature encoder on the motor shaft that has 5 steps per motor full step. 
M569 P50.0 D4 S1 ; Configure the motor on the 1HCL at can address 50 as being in closed-loop drive mode (D4) and not reversed (S1) 
M569 P51.0 D4 S1 ; Configure the motor on the 1HCL at can address 51 as being in closed-loop drive mode (D4) and not reversed (S1) 
M584 X50.0 Y51.0 ; set X and Y drivers
M917 X0 Y0 ; Set the closed loop axes to have a holding current of zero
M350 X32 Y32 ; set steps/mm to 32 to make full use of the encoder resolution
M92 X160 Y160 ; steps/mm for a 20 tooth gt2 pulley
```

Note the initial PID values show will need to be tuned to the particular motor.

In contrast to usual drivers, the closed loop axes can have their holding current set to zero using M917, with negligible detrimental effect. Whilst a normal driver may slip if it's holding current is set to zero, a closed loop driver will notice that it has slipped an apply a current to return the drive to it's intended position. Setting a holding current of zero will also mean less current is used, so the motor runs cooler. However, a holding current can still be set using M917 if desired.

## Tuning the PID for the closed loop

See [Tuning the Duet 3 Expansion 1HCL](/User_manual/Tuning/Duet_3_1HCL_tuning) for details on tuning.

## Temperature sensor

The following code could be used in config.g to set the sensor as a thermistor:

```
M308 S3 P"50.temp0" Y"thermistor" T100000 B3950 A"X Motor Temp" ; Setup temp 0 on 1HCL at CAN address 50 as sensor 3  - sensing X motor temperature
M308 S4 P"51.temp0" Y"thermistor" T100000 B3950 A"Y Motor Temp" ; Setup temp 0 on 1HCL at CAN address 51 as sensor 4  - sensing Y motor temperature
```

These sensors would be displayed in the "extras" tab in  DWC and available in the object model to query and potentially take action on for example the following could be inserted into [daemon.g](/User_manual/Tuning/Macros#daemong) to check the motor temperature every second and raise the alarm if they are higher than a set value of 70C

```
if sensors.analog[3].lastReading >70
    echo "X MOTOR Temp Alarm: ", sensors.analog[3].lastReading
    M98 P"motorovertemp.g"
if sensors.analog[4].lastReading >70
    echo "Y MOTOR Temp Alarm: ", sensors.analog[4].lastReading
    M98 P"motorovertemp.g"
G4 P1000
```

Where the "motorovertemp.g" macro can have whatever actions are appropriate. This logic can be extended to take different actions at different temperatures ( e.g. log at 70, sound alarm at 80, pause print at 100)

## Motor Brake Control

Some motors have a motor brake fitted for an holding brake solenoid. As long as the solenoid max current draw is <2.5A it can be directly controlled by out 0 or out 1. If the Brake needs a different voltage from the VIN voltage used for the motor then that can supplied on the VBRAKE connector.

This example sets out0 to control the brake using the [M569.7](https://docs.duet3d.com/User_manual/Reference/Gcodes#m5697-configure-motor-brake-port) command

`M569.7 P50.0 C"out0"`

***NOTE proceed with caution, always test these examples with low motor current and slow speeds first***

When the motor driver is enabled, the specified output port will be turned on at the same time to release the brake. When the motor driver is disabled, the output port will be turned off. Idle current mode does not count as disabled.

After M569.7 is executed, the port will be initially off. Therefore, M569.7 should be executed before the motor is first enabled.

# Revisions

## Tabs {.tabset}

### Revision v1.0

* Pin changes to allow a UART and PWM on IO_0 and I2C on IO_1
* Add a physical jumper for I2C support on IO_1
**Note:** RepRapFirmware does not currently support I2C on Duet 3 boards.
* Change the SPI header to be a 2x5 box header so an off the shelf 2x5 ribbon cable can be used.	Used the same pinout arrangement as the SPI temperature Daughterboard connector (with the quadrature pins where CS lines were).
* Add a second temperature input on PA7 (AIN7)
* Remove the button

### Revision v0.3

* First prototype to use the SAME51 processor. This revision is no longer supported by firmware.

Dimensions:
[![Image showing the key dimensions of the Duet 3 1HCL prototype v0.3](/duet_boards/duet_3_can_expansion/duet_3_1hcl/duet_3_1hcl_dimensions_01.png =500x)](/duet_boards/duet_3_can_expansion/duet_3_1hcl/duet_3_1hcl_dimensions_01.png){target=_blank}

Wiring:
[![Image showing all the connections on a Duet 3 1HCL prototype v0.3 to aid wiring](/duet_boards/duet_3_can_expansion/duet_3_1hcl/duet3_eb_1hcl_v0.3_d1.0_wiring.png =500x)](/duet_boards/duet_3_can_expansion/duet_3_1hcl/duet3_eb_1hcl_v0.3_d1.0_wiring.png){target=_blank}
