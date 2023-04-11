---
title: Configuring stepper motors
description: Describes firmware configuration of stepper motors
published: true
date: 2023-04-11T14:18:17.007Z
tags: 
editor: markdown
dateCreated: 2021-10-11T11:54:37.059Z
---

# Drive numbers

Drive numbers used in G-code correspond to the following driver labels on the board(s):

| Drive number | Duet 3 board label |||| Duet 2 board label ||
| | 6HC | Mini 5+ | 3HC | 1LC | WiFi/Ethernet | Maestro |
|:---|:---|
| 0 | DRIVER_0 |||| X ||
| 1 | DRIVER_1 ||| | Y ||
| 2 | DRIVER_2 ||| | ZA ZB (Two headers wired in series) ||
| 3 | DRIVER_3 || | | E0 ||
| 4 | DRIVER_4 || | | E1 ||
| 5 | DRIVER_5 | | | | E2 (On DueX2/5) | E2 (pins for external driver) |
| 6 | | | | | E3 (On DueX2/5) | E3 (pins for external driver) |
| 7 | | | | | E4 (On DueX5) |  |
| 8 | | | | | E5 (On DueX5) |  |
| 9 | | | | | E6 (On DueX5) |  |
| 10 | | | | | On LCD_CONN header |  |
| 11 | | | | | On LCD_CONN header |  |

# Drive limits

As of RepRapFirmware 3.4, the maximum number of configurable drives is:

| | Duet 3 MB6HC/6XD | Duet 3 Mini 5+ | Duet 2 WiFi/Ethernet | Duet 2 Maestro | Notes |
|---|---|
| MaxAxes | 15 | 10 | 10 | 6 | The maximum number of movement axes in the machine, usually just X, Y and Z |
| MaxDriversPerAxis | 8 | 4 | 6 (5 in RRF3.3) | 4 | The maximum number of stepper drivers assigned to one axis |
| MaxExtruders | 16 | 5 | 7 | 4 | The maximum number of extruders |
| MaxAxesPlusExtruders | 25 | 12 | 12 | 7 | The maximum number of axes and extruders |

# Defining connected motors

The [M569](/User_manual/Reference/Gcodes/M569) command is used to define motor parameters, and [M584](/User_manual/Reference/Gcodes/M584) is used to assign motors to axes, and define axes as linear or rotational. 

### Tabs {.tabset}

#### Duet 3

With Duet 3 boards, the M569 P parameter used to identify the stepper driver is formatted ***board_number.drive_number***, where the board_number relates to the CAN address of the board. A board_number of 0 relates to the mainboard, and additional drivers on toolboards and/or expansion boards will have a different board_number. For example, the default board_number for a Duet 3 Expansion 3HC is 1, while the default board_number for Duet 3 Toolboard 1LC is 121.

With all the motors defined by M569, they are assigned to their axes using M584.

```
; Drives
M569 P0.0 S1               ; physical drive 0.0 goes forwards
M569 P0.1 S0               ; physical drive 0.1 goes backwards
M569 P0.2 S0               ; physical drive 0.2 goes backwards
M569 P121.0 S1             ; physical drive 121.0 goes forwards
M584 X0.0 Y0.1 Z0.2 E121.0 ; set drive mapping
```

The above example is a simple Duet 3 setup, with X, Y and Z axis motors (one motor per axis) connected to the mainboard, and a toolboard with the extruder motor connected to it.

#### Duet 2

See the above table for the location of each drive number. Duet 2 mainboards have drivers 0 to 4 on-board. The DueX2/5 for Duet 2 WiFi/Ethernet, and the Dual Stepper Driver Expansion Module for Duet 2 Maestro allow for extra motors to be connected.

With all the motors defined by M569, they are assigned to their axes using M584.

```
; Drives
M569 P0 S1                 ; physical drive 0 goes forwards
M569 P1 S1                 ; physical drive 1 goes forwards
M569 P2 S0                 ; physical drive 2 goes backwards
M569 P5 S1                 ; physical drive 5 goes forwards
M569 P6 S1                 ; physical drive 6 goes forwards
M569 P7 S1                 ; physical drive 7 goes forwards
M569 P8 S1                 ; physical drive 8 goes forwards
M584 X0 Y1 Z2 E5:6:7:8 ; set drive mapping
```

The above example is of a Duet 2 WiFi/Ethernet with X, Y and Z axis motors (one motor per axis) connected to the mainboard, and a DueX5 expansion board with 4 x extruder motors connected to it.


## Using more than one motor on an axis with a separate driver for each motor

Note that RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour. 

### Tabs {.tabset}

#### Duet 3

```
; Drives
M569 P0.0 S1                           ; physical drive 0.0 goes forwards
M569 P0.1 S0                           ; physical drive 0.1 goes backwards
M569 P0.2 S0                           ; physical drive 0.2 goes backwards
M569 P1.0 S1                           ; physical drive 1.0 goes forwards
M569 P1.1 S1                           ; physical drive 1.1 goes forwards
M569 P1.2 S1                           ; physical drive 1.2 goes forwards
M569 P121.0 S1                         ; physical drive 121.0 goes forwards
M569 P122.0 S1                         ; physical drive 121.0 goes forwards
M569 P123.0 S1                         ; physical drive 121.0 goes forwards
M569 P124.0 S1                         ; physical drive 121.0 goes forwards
M584 X0.0 Y0.1:0.2 Z1.0:1.1:1.2 E121.0:122.0:123.0:124.0 ; set drive mapping
```

The above example is of a Duet 3 mainboard with X axis (one motor) and Y axis (two motors on separate drivers) connected, an expansion board with 3 x Z axis motors connected to it, and 4 x toolboards with an extruder motor connected to each one.

#### Duet 2

```
; Drives
M569 P0 S1                 ; physical drive 0 goes forwards
M569 P1 S1                 ; physical drive 1 goes forwards
M569 P2 S0                 ; physical drive 2 goes backwards
M569 P3 S0                 ; physical drive 3 goes backwards
M569 P4 S0                 ; physical drive 4 goes backwards
M569 P5 S1                 ; physical drive 5 goes forwards
M569 P6 S1                 ; physical drive 6 goes forwards
M569 P7 S1                 ; physical drive 7 goes forwards
M569 P8 S1                 ; physical drive 8 goes forwards
M584 X0 Y1 Z2:3:4 E5:6:7:8 ; set drive mapping
```

The above example is of a Duet 2 WiFi/Ethernet mainboard with X axis (one motor), Y axis (one motor) and Z axis (3 motors, each on individual drivers) connected, and a DueX5 expansion board with 4 x extruder motors connected to it.

## Creating additional axes

You can create additional axes as required. Use [M569](/User_manual/Reference/Gcodes/M569) to define the motor parameters, and assign it to an axis with [M584](/User_manual/Reference/Gcodes/M584). Additional drive letters can be:
* UVW in RepRapFirmware 1.16 and later
* UVWABC  in RepRapFirmware 1.19 and later
* UVWABCD available in RepRapFirmware 3.0 and later
* UVWABCDabcdefghijkl available in RepRapFirmware 3.3 and later, Duet 3 MB6HC only

### Tabs {.tabset}

#### Duet 3

```
; Drives
M569 P0.0 S1                           ; physical drive 0.0 goes forwards
M569 P0.1 S0                           ; physical drive 0.1 goes backwards
M569 P0.2 S0                           ; physical drive 0.2 goes backwards
M569 P0.3 S1                           ; physical drive 1.0 goes forwards
M569 P0.4 S1                           ; physical drive 1.1 goes forwards
M569 P121.0 S1                         ; physical drive 121.0 goes forwards
M569 P122.0 S1                         ; physical drive 121.0 goes forwards
M584 X0.0 Y0.1 Z0.2 U0.3 V0.4 E121.0:122.0 ; set drive mapping
```

The above example is of a Duet 3 mainboard with X, Y, Z, U and V axes, with one motor per axis (perhaps for a CoreXYU printer, which has two motors for each extruder), and 2 x toolboards with an extruder motor connected to each one.

**NOTE:** when configuring extra axes, you must configure steps and speeds for the additional axes, using M350, M92, M566, M203, M201 and M906. See 'Configuring steps and speeds' section below.

#### Duet 2

```
; Drives
M569 P0 S1                 ; physical drive 0 goes forwards
M569 P1 S1                 ; physical drive 1 goes forwards
M569 P2 S0                 ; physical drive 2 goes backwards
M569 P3 S0                 ; physical drive 3 goes backwards
M569 P4 S0                 ; physical drive 4 goes backwards
M569 P5 S1                 ; physical drive 5 goes forwards
M569 P6 S1                 ; physical drive 6 goes forwards
M569 P7 S1                 ; physical drive 7 goes forwards
M569 P8 S1                 ; physical drive 8 goes forwards
M584 X0 Y1 Z2:3:4 U:5 V:6 E7:8 ; set drive mapping
```

The above example is of a Duet 2 WiFi/Ethernet mainboard with X, Y, U and V axes, with one motor per axis (perhaps for a CoreXYU printer, which has two motors for each extruder), and a DueX5 expansion board with the Z axis (3 motors, each on individual drivers) and 2x extruder motors connected to it.

**NOTE:** when configuring extra axes, you must configure steps and speeds for the additional axes, using M350, M92, M566, M203, M201 and M906. See 'Configuring steps and speeds' section below.

## Disabling stepper driver reporting

If you have a damaged stepper driver on your Duet board, or have a board that does not have certain stepper drives (eg Vivedino Troodon board), you may get constant spurious error messages. These can be disabled on a per-stepper driver basis by sending:
```
; Example configuration
M569 P0 R-1 ; driver 0 is always disabled and is not monitored
```

# Configuring steps and speeds

## Setting microstepping and steps per mm

Use [M350](/User_manual/Reference/Gcodes/M350) and [M92](/User_manual/Reference/Gcodes/M92) to set microstepping and steps per mm for each axis. 

Microstepping and steps per mm settings will depend on your choice of motor, motion system and kinematics. The settings are stored in config.g, then read and set at power on. However, you can send an M350 or M92 command at any time to change them. Sending the command with no parameters will report the current setting.

For **microstepping**, we generally advise to use x16 microstepping, with interpolation; stepper motors are usually not capable of higher resolution or repeatability when using higher microstepping modes. However, there are some instances when using higher microstepping modes (x32, x64, x128 or x256) is useful, eg mixing extruders or axes that otherwise would have a low steps per mm. In some cases you may want to use lower microstepping modes (x1, x2, x4 or x8), for example if you have a highly geared motor, or need to turn a motor particularly fast.

Using interpolation will make motor movements smoother, at no cost, so is generally worth enabling if the stepper driver supports it.

For **steps per mm**, there is a calculator built into the [RepRapFirmware Congiguration Tool](https://configtool.reprapfirmware.org/) to help you calculate the correct number. 

Note that RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour. 

However, each extruder axis (E) is treated as a separate axis, and specific settings for each extruder should be configured.

In the example below, the Z axis has two motors, but takes only one value for M350 and M92, while the E has a value for each extruder motor.

```
; Example configuration
M584 X0 Y1 Z2:3 E4:5                     ; set drive mapping

M350 X16 Y16 Z16 E16:16 I1               ; configure microstepping with interpolation
M92 X80.00 Y80.00 Z400.00 E420.00:420.00 ; set steps per mm
```

## Setting motor speeds and accelerations

Use [M566](/User_manual/Reference/Gcodes/M566), [M203](/User_manual/Reference/Gcodes/M203) and [M201](/User_manual/Reference/Gcodes/M201) to set motor speeds and accelerations.

* M566 - Sets the maximum allowable instantaneous speed change (sometimes called 'jerk speed') of each motor when changing direction
* M203 - Sets the maximum feedrates in mm/min
* M201 - Sets the acceleration that axes can do in mm/second^2 for print moves

Motor speed and acceleration settings will depend on your choice of motor, motion system and kinematics. The settings are stored in config.g, then read and set at power on. However, you can send an M566, M203 or M201 command at any time to change them, for example when tuning for maximum speeds and acceleration. When tuning, it's best to start slow, then increase to find the limit (ie where each axis looses steps, or there is too much vibration), then reduce until you find a safe setting that performs as desired. Sending the command with no parameters will report the current setting.

Note that RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour. 

However, each extruder axis (E) is treated as a separate axis, and specific settings for each extruder should be configured.

In the example below, the Z axis has two motors, but takes only one value for M566, M203 and M201, while the E has a value for each extruder motor.

```
; Example configuration
M584 X0 Y1 Z2:3 E4:5                            ; set drive mapping

M566 X900.00 Y900.00 Z60.00 E120.00:120.00      ; set maximum instantaneous speed changes (mm/min)
M203 X6000.00 Y6000.00 Z180.00 E1200.00:1200.00 ; set maximum speeds (mm/min)
M201 X500.00 Y500.00 Z20.00 E250.00:250         ; set accelerations (mm/s^2)
```

## Setting motor currents

Motor currents are set using the [M906](/User_manual/Reference/Gcodes/M906) command. A typical setting is 60% to 85% of the rated current of your stepper motors, though you can set it higher or lower than this, limited by the output of the stepper driver on the Duet.
* If you set the stepper motor current too low (i.e. below 50% of rated current), the torque will be low. The microsteps may be less accurate, and the motor may skip steps if the load is heavy or you command a high acceleration.
* If you set the motor current too high (i.e. over 90% of rated current), the motors will get hot. If your printer uses printed PLA parts to mount the motors, then the heat may soften those PLA parts.

The standard motor currents you want to use are set by a M906 command in config.g. However, you can send a M906 command at any time to change them.  Sending the command with no parameters will report the current setting.

You can also use the [M913](/User_manual/Reference/Gcodes/M913) command to change the motor currents temporarily, for example during homing.

Note that RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour. 

However, each extruder axis (E) is treated as a separate axis, and specific settings for each extruder should be configured.

In the example below, the Z axis has two motors, but takes only one value for M906, while the E has a value for each extruder motor.

```
; Example configuration
M584 X0 Y1 Z2:3 E4:5             ; set drive mapping

M906 X800 Y800 Z800 E800:800 I30 ; set motor currents (mA) and motor idle factor in per cent
```
As with most other commands in RepRapFirmware, you can see the current settings by sending M906 in the console:
```
M906
Motor current (mA) - X:1200, Y:1200, Z:1000, E:1000, idle factor 30%
```

## Settings for additional axes

When adding additional axes, you must configure steps and speeds for them, using M350, M92, M566, M203, M201 and M906. Add the additional axis/axes into these commands. For example:

```
M584 X0 Y1 Z2:3:4 U:5 V:6 E7:8               ; set drive mapping

M350 X16 Y16 Z16 U16 V16 E16:16 I1           ; configure microstepping with interpolation
M92 X80 Y80 Z400 U80 V80 E420:420            ; set steps per mm
M566 X900 Y900 Z60 U900 V900 E120:120        ; set maximum instantaneous speed changes (mm/min)
M203 X6000 Y6000 Z180 U6000 V6000 E1200:1200 ; set maximum speeds (mm/min)
M201 X500 Y500 Z20 U500 V500 E250:250        ; set accelerations (mm/s^2)
M906 X800 Y800 Z800 U800 V800 E800:800 I30   ; set motor currents (mA) and motor idle factor in per cent

```
