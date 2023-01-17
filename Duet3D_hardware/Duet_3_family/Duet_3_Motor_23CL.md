---
title: Duet 3 Motor 23CL
description: A range of CAN-FD connected closed loop NEMA 23 motors for Duet 3 ecosystem.
published: false
date: 2023-01-17T14:41:39.131Z
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

| |M23CL-56-2800B|M23CL-56-2800|M23CL-76-2800B|M23CL-76-2800|
|:---|:---|:---|:---|:---|
| **Holding Torque** | 12.6 Kg.cm || 18.9 Kg.cm||
| **Detent Torque** | 0.4 Kg.cm || 0.86 Kg.cm||
| **Full Step Angle^1^** | 1.8deg ||||
| **Max current/phase** | 2.8A || 2.8A ||
| **Rotor Inertia** | 0.3Kg/cm^2 || 0.48Kg/cm^2 ||
| **Motor Section Length** | 56mm || 76mm||
| **Total Length** | TBC^2^ || TBC||
| **Brake** | 24V, 1.5Nm| No Brake fitted| 24V, 1.5Nm| No Brake fitted|
| **Ambient Temperature**|-20C - +50C|||
| **Max Motor & Controller Temperature**|85C|||
| **Input voltage** | 12V to 48V ||||
| **Processor** | Microchip ATSAME51G19A ||||
| **Processor features** | 32-bit, 120MHz ARM Cortex-M4F, 512Kb flash, 192Kb RAM, hardware single precision floating point unit ||||
| **Networking/Comms** | CAN-FD BUS for connection to the Duet 3 Mainboard. Optional on-board CAN bus termination. ||||
| **On-board stepper driver** | 1 x [TMC2160A](https://www.trinamic.com/products/integrated-circuits/details/tmc2160-ta/){target=_blank} ||||
| **Stepper driver features** | SPI controlled, can be run in open loop or closed loop mode. ||||
| **Encoder Inputs** | Hall effect 14 bit resolution ||||
| **Temperature monitoring** | 1 on board thermistor and processor temperature avaiable for temperature monitoring. ||||

^1^ In open loop mode up to 256 microstepping can be configured
^2^ See the prototpye dimensions below for the prototpye M23CL-56-2800B

## Firmware notes

* Compatible RepRapFirmware versions: RRF 3.4.4 and later
* General Firmware limitations: See [Duet 3 with CAN expansion firmware configuration limitations](/User_manual/RepRapFirmware/CAN_limitations).

# Physical properties

## Dimensions

[![Image showing the key dimensions of the Duet 3 motor 23CL prototype](/duet_boards/duet_3_can_expansion/motor23cl/duet3_motor_23cl_v0.2_dimensions.png =500x)](/duet_boards/duet_3_can_expansion/motor23cl/duet3_motor_23cl_v0.2_dimensions.png){target=_blank}

## 3D model

*to follow*

# Wiring

## Connection Diagram

[![Image showingthe connections on a Duet 3 Motor 23CL](/duet_boards/duet_3_can_expansion/motor23cl/duet3_motor_23cl_connection.png =600x)](/duet_boards/duet_3_can_expansion/motor23cl/duet3_motor_23cl_connection.png){target=_blank}

# Encoders

## Built in encoder

The M23CL incorporates a hall effect position sensor sensing a magnet on the motor shaft. The motor and the encoder need to be calibrated together as part of commissioning, see the section on doing this below. 

# Commissioning

See [CAN connection basics](/User_manual/Machine_configuration/CAN_connection)

All boards in the system must have different CAN addresses. Duet 3 Mootor 23CLs are shipped set to a default CAN address of 123. They will also revert to 123 if you use the can reset button (not exposed on prototpyes). Therefore, if you have more than one new M23CL or tother Cut3d CAN-FD connectted expansion board, only one of them must be powered up and connected to the CAN bus at a time so the address can be changed form the default. So disconnect power to all but one of them (you can leave the CAN bus connected if it's easier). When you have changed the CAN address of that M23CL, you can connect the next one; and so on. See the section: [Set the CAN address](/Duet3D_hardware/Duet_3_family/Duet_3_Motor_23CL#set-the-can-address) below for how to change the default address.

## Startup Time

It is recommended to add the following to config.g, before any commands that reference any CAN bus connected expansion boards

`G4 S2   ;wait for expansion boards to start`

The first time the M23CL starts up it may request firmware from the Duet3d mainboards. ensure you have the correct version (compatible with your mainboard firmware) in the /firmware directory on the  mainboard. see the Firmware section below for more details.

## Testing communication

Before the M23CL is connected to the CAN-FD bus, if it is powered, the STATUS (red) LED will be blinking rapidly. once it is connected (after any initial firmware update happens), it should settle down to a ~1Hz blink rate. If there are other blink patterns displayed see the [LED behaviour and error codes section of the CAN connection basics documentation] (/User_manual/Machine_configuration/CAN_connection#led-behaviour-and-error-codes}

Check that you can communicate with the 1HCL board, by sending:

`M115 B123`

If that fails try depressing the CAN_RST switch and powering up, then down again, this will reset the CAN-FD bus settings to the default (address 123, bus speed 1Mbps)

## Update the bootloader

Duet 3 expansion boards, tool boards and Motors have a bootstrap loader written to the start of flash so that they can load firmware from the main board via CAN. This bootloader may occasionally need to be updated in order to support new features. See [Updating the bootloader on Duet 3 expansion and tool boards](/User_manual/RepRapFirmware/Updating_bootloader).

## Updating the firmware

The 1HCL board will be shipped with firmware loaded during production. You can  check the version loaded by sending

`M115 B123`

(or B## where ## is the new CAN address of the board if you have changed it already)

To update the firmware get the [latest version from the RepRapFirmware github.](https://github.com/Duet3D/RepRapFirmware/releases) It is highly recommended to upgrade all the firmware in your Duet 3 system together so that the versions do not get out of sync.

Send M997 B## to carry out a firmware update, the bootloader will request the Duet3Firmware_M23CL.bin from the Duet 3 main board, it needs to be in the /firmware folder.

## Set the CAN address

* Send command M115 B## to verify that the main board can communicate with the M23CL, where ## is the default address of 123 if it has not been changed already.
* Send command M952 B# A## where ## is the new address you want to use. Allowed CAN addresses for normal use are 1 to 119. We suggest you use addresses starting at 70 for M23CLs. So for the first M23CL, if your new CAN board was at address 123, send M952 B123 A70.
* Power the M23CL down and up again, or send M999 B123. This will cause the M23CL to restart with the new address.
* Send command M115 B70 (or whatever address you chose) to verify that you can communicate with the M23CL at its new address
* You can now power up the next M23CL and commission it in the same way, **choosing a different CAN address for it**. so for the second board, e.g. M952 B123 A71 and so on.

## Calibrating the Encoder

for mre details on this please see the section on [calibrating the magnetic encoder](/User_manual/Tuning/Duet_3_1HCL_tuning#calibrating-magnetic-encoders) on the closed loop tuning page, its writted for the magnetic encoder on the 1HCL but the same theory applies.

The calibration procedure measures magnet offsets and attempts to corrects for this in software. Since the magnet's position is not affected by cycling the printer's power, this data is stored in non-volatile storage such that it only has to be run once. if course, if you change or remove and reattach the M23CL internal control board, you must re-run this tuning move.

### Running the calibration procedure

A little more than one full rotation of the motor is performed in each direction. Unlike other tuning moves it is recommended the motor is not connected to any axis or other load for this calibration routine.

Once you are satisfied that the motor can freely make up to 1.5 rotations in either direction, run the following command:

M569.6 P##.# V2 ; Where P##.# is the driver address to tune

Once this has been performed successfully, the values will be written to non-volatile memory and remembered each time the power is cycled. The tuning can be re-run by simply running the M569.6 ... V2 command again, or checked by running the M569.6 ... V3 command.

The firmware will output the highest deviation of expected positon vs encoder position recorded. 

*To follow, acceptable ranges for deviation of position*

# Firmware

Duet 3 Motor 23CL are supported in RRF 3.5 and later.

## Limitations

Please see the current RepRapFirmware limitations at [Duet 3 firmware with CAN expansion configuration limitations](/User_manual/RepRapFirmware/CAN_limitations).

## Microstepping

While in closed loop mode step pulses are not sent to the stepper motor driver in the same manner as an open loop driver, however the firmware still uses microsteps internally to represent moves.

The M23CL will autoset the correct settings for closed loop mode. In open loop mode microstepping can be set as normal using M350 and M92 should be set to match that microstepping as normal.

### Summary of control

1. RepRapFirmware on the main board rounds the endpoint or extrusion amount to whole microsteps.
1. RepRapFirmware sends the move details over the CAN-FD bus, including the move length for each motor measured in whole microsteps.
1. In open loop model microsteps are generated at the appropriate times.
1. In closed loop mode where the motor should be calculated from the movement parameters as a floating point number of full steps.

# Sample configuration examples

**CAUTION** before using these examples check the datasheet and user manual of the motor, encoder (and optionally brake) you are using. Especially: check compatibility of signal voltages.

## Adding a M23CL

[M569.1](/User_manual/Reference/Gcodes/M569_1) is used to configure the closed loop driver.

Here's an sample excerpt from a config.g file for RRF 3.5 to drive the X and Y motors from M23CLs configured at CAN addresses 70 and 71.

```
M569.1 P70.0 T3 E2:4 R100 I0 D0 ; Configure the M23CL at CAN address 70
M569.1 P71.0 T3 E2:4 R100 I0 D0 ; Configure the M23CL at CAN address 71
M569 P70.0 D4 S1 ; Set into closed-loop drive mode (D4) and not reversed (S1) 
M569 P71.0 D4 S1 ; Set into closed-loop drive mode (D4) and not reversed (S1) 
M584 X70.0 Y71.0 ; set X and Y drivers

M350 X32 Y32 ; set steps/mm to 32 for open loop mode operation
M92 X160 Y160 ; steps/mm for a 20 tooth gt2 pulley for open loop operation
```

Note the initial PID values show will need to be [tuned to the particular motion system](https://docs.duet3d.com/en/User_manual/Tuning/Duet_3_1HCL_tuning).

In contrast to usual drivers, the closed loop axes can have their holding current set to zero using M917, with negligible detrimental effect. Whilst a normal driver may slip if it's holding current is set to zero, a closed loop driver will notice that it has slipped an apply a current to return the drive to it's intended position. Setting a holding current of zero will also mean less current is used, so the motor runs cooler. However, a holding current can still be set using M917 if desired.

## Tuning the PID for the closed loop

See [Tuning the Duet 3 Expansion 1HCL](/User_manual/Tuning/Duet_3_1HCL_tuning) for details on tuning the M23CL. the section on PID tuning applies to both the 1HCL and the M23CL

## Temperature sensor

The following code can be used in config.g to monitor the internal control board temperature.

```
M308 S3 P"70.temp0" Y"thermistor"
M308 S4 P"71.temp0" Y"thermistor"
```

These sensors would be displayed in the "extras" tab in  DWC and available in the object model to query and potentially take action on for example the following could be inserted into [daemon.g](/User_manual/Tuning/Macros#daemong) to check the internal temperature every second and raise the alarm if they are higher than a set value of 70C

```
if sensors.analog[3].lastReading >70
    echo "X MOTOR Temp Alarm: ", sensors.analog[3].lastReading
    M98 P"motorovertemp.g"
if sensors.analog[4].lastReading >70
    echo "Y MOTOR Temp Alarm: ", sensors.analog[4].lastReading
    M98 P"motorovertemp.g"
G4 P1000
```

Where the "motorovertemp.g" macro can have whatever actions are appropriate. This logic can be extended to take different actions at different temperatures ( e.g. log at 60, sound alarm at 70, pause print at 80)

## Motor Brake Control

Some versions of the M23CL have a motor holding brake solenoid fitted (all prototpyes, and model numbers ending in "B") 

If a brake is present the M23CL will disable it when the motors are enabled (M17 and enable it when the motors are disabled (M18).

# Revisions

## Tabs {.tabset}

### Prototpye

* The intial M23CL witha 54mm motor body, brake and an 8mm shaft.