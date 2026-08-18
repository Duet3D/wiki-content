---
title: INDX Toolboard
description: The INDX Toolboard controls of all functions of the nozzle-swapping Bondtech INDX toolhead.
published: true
date: 2026-08-18T10:18:02.344Z
tags: 
editor: markdown
dateCreated: 2026-02-09T09:34:17.141Z
---

# INDX Tool Board

>Load cell Z probing support is still work in progress. The configuration commands and macros described on this page are likely to change during the RepRapFirmware 3.7 beta cycle{.is-info}

This page is about using the Bondtech INDX tool board with Duet 3 or other electronics running RepRapFirmware.

The INDX documentation from Bondtech is [here](https://github.com/BondtechAB/INDX). It should be read in conjunction with this page.


## Features
- Controls induction heater, thermopile sensor, load cell and heatsink fan integrated with INDX toolhead
- Drives INDX tool head extruder motor, with closed loop option if a suitable diametrically-magnetised magnet is attached to the end of the motor shaft
- RGB LEDs on VF board displaying status information, visible via light pipes in the INDX toolhead
- Output for a print cooling fan with optional tacho
- Output for WS2812 or similar (Neopixel) LED string
- Supports connection of sensor coil for scanning inductive sensor
- On-board accelerometer
- Uncommitted input for endstop or similar

## Operating limits

|---|---|
| **Stepper driver** | Maximum 1.0A peak current, 0.71A RMS
| **FAN output on MCU board maximum current** | TBD |
| **Input power voltage** | 24V +/- 2V |
| **Power input max current** | 4A
| **Inputs** | IO_0 is 30V-tolerant |
| **Fuses** | None onboard. Use INDX Link board (4A fuse fitted), Duet 3 Tool Distribution Board (4A fuse fitted), or if directly connected to a power supply use an inline fuse holder with 4A or lower fuse depending on required current draw. |
| **5V (LED port) maximum load current** | TBD |
| **3.3V (ENDSTOP/IO0_IN port) maximum load current** | TBD |
| **Maximum ambient temperature** | 80°C |

## Hardware notes

The INDX tool board comprises two PCBs connected by two 20-way FFCs (Flexible Flat Cables). These will normally be supplied ready-mounted on a tool head.

The VF board is connected to the induction heater, IR temperature sensor, heatsink fan, and load cell. Do not make any other connections to the VF board, or remove the existing connections. The heatsink fan is connected to the VF board and defaults to running continuously; therefore it will run whenever no firmware is installed on the board, or firmware is being updated, or no configuration commands have been received from the main board.

The MCU board is connected to the rest of a Duet/RepRapFirmware system using a single XT30 2+2 connector. This provides power to the board (thick red and black wires, positive and ground respectively) and CAN FD (yellow and white wires, CANH and CANL respectively).

The board requires 24V nominal power, fused externally at 3A or 4A. We recommend that you use a Duet 3 Tool Distribution Board or INDX Link Board because they include the necessary fuse and simplify wiring. If the INDX is the only CAN-connected expansion in your system then you can instead use a direct CAN connection to the main board and an inline auto fuse in the positive supply wire.

The MCU board also provides the following connections:
- 4-pin connector for the stepper motor in the INDX toolhead
- 3-pin JST PA connector for the print cooling fan with optional tacho
- 3-pin IO0 connector, for an endstop or other input device
- 3 Pin connector for WS2812 (aka Neopixel) or similar LEDs
- 4-pin FFC connector for optional scanning inductive sensor coil. This requires a **same side FFC** (A-A) to connect to a standard Duet3D coil or the Bondtech SZP coil.
- 5-pin USB OUT connector. This is not used when running RepRapFirmware.

### Pin diagram

[![Bondtech PCB pinout](https://github.com/BondtechAB/INDX/raw/main/images/bondtech-indx-pcb-pinout.jpg)](https://github.com/BondtechAB/INDX#electrical-requirements){target=_blank}

*this is linked from the [Bondtech documentation](https://github.com/BondtechAB/INDX#electrical-requirements) for convenience*,

### Connecting the 20-way FFCs

If you need to disconnect and reconnect the FFCs linking the two boards, be aware of the following:
- Be sure to place the contact side of the FFC against the contacts in the socket
- Be sure to insert the cable straight into the middle of the connector. It is easy to insert a cable so that it is skewed and shorts the pins out. If you do this and then power up the board, it is likely to be damaged.
- The latching mechanisms on the vertical FFCs on the MCU board are counter-intuitive. They are unlatched when the latch is in the up position (away from the PCB). After inserting the FFCs, push the latch down towards the PCB to lock the FFC in place.

### Switch

RRF supports the CAN connection, so set the `CAN <-> USB` switch on the MCU board to the `CAN` position.

### Jumpers

The following jumper blocks are provided:
- 2-pin CAN_RESET jumper. Only install this if the firmware running on the board is non-functioning. When the board is powered up with this jumper installed, it tells the bootloader to reset the CAN address to default (121) and then fetch and install new firmware even if firmware is already installed.
- 2-pin CAN_TERM jumper. Install this if the board is the last board at one end of the CAN bus.

## Wiring

The Bondtech INDX tool head is normally supplied with an associated Link board. This board provides the following:
* 4A fuse in the VIN supply to the INDX tool head
* USB isolator to protect a host computer USB port from damage in the event of a USB malfunction (in particular, a broken ground connection). This is not required when using RRF because RRF does not use the USB port.
* Protection against VIN reverse polarity at the VIN terminal block.

### To use the Link board:
* Set the CAN<->USB switch on the Link board to CAN
* Connect the power and signal connectors of the cable supplied to the VOUT and DATA OUT pins of the Link board
* Connect the VIN power supply to the 2-way terminal block
* Connect the CAN IN connector to the CAN bus from your main board
* If the INDX tool is the last board on the CAN bus, do not connect anything to the CAN OUT port on the Link board, and install the termination jumper on the IND MCU board
* If the INDX tool is not the last board on the CAN bus, connect the CAN OUT port to the next board in the chain, and do not fit the termination jumper on the INDX MCU board.
* Do not connect anything to the USB port on the Link board.

### To use a Duet Tool Distribution Board instead of the Link board 
>You will not have VIN reverse polarity protection.{.is-warning}
* Choose an output on the Tool Distribution Board to connect the INDX tool to
* Replace the 5A fuse in that position of the Tool Distribution Board with the 4A fuse from the Link board
* Connect the power and data connectors of the supplied cable to the selected position on the Tool Distribution Board. If using a version 1.0 Tool Distribution Board then the connectors are compatible. If using a version 0.5 board then you will need to change the 2-pin PA connector on the cable to a 4-pin PH connector.
* See the Tool Distribution Board instructions for generic instructions for connecting tool boards

## LED indications

Aside from the status LEDs mounted on the VF board, LEDs are provided on the MCU board to indicate the following:

| Label | Colour | Function |
|--|--|--|
| **VIN** | Blue | Indicates presence of VIN power |
| **3.3V** | Green | Indicates presence of 3.3V power from on-board regulator |
| **ACT / LED 1** | Green | Indicates activity (other than regular time sync messages) on the CAN-FD bus |
| **STATUS / LED 0** | Red | Status LED. See description below |

**Status LED:** In normal use, the red LED flashes slowly (approx 1Hz) in sync with the main board to indicate that it has CAN time sync, or flashes continuously and rapidly to indicate that it doesn't. It also flashes startup error codes, for example if the bootloader doesn't find valid firmware on the board. For a list of these error codes see [CAN_connection basics](https://docs.duet3d.com/User_manual/Machine_configuration/CAN_connection#led-behaviour-and-error-codes).

## Software notes
The RepRapFirmware binary file for this board is called **Duet3Firmware_TOOLINDX.bin**.

The bootloader file for this board is called **Duet3Bootloader-SAME5x_CAN_USB.bin**.

The minimum RepRapFirmware version for this board is 3.7.0-beta.1. However, **firmware 3.7.0-beta.3 or later is highly recommended as it includes additional protection against overheating**. This applies to the firmware running on the main board too. If older main board firmware is used then some of the functionality may be missing, in particular the heater and the load cell are unlikely to work.

The default CAN address (which is also the CAN address after the reset jumper is used) is 121.

The inductive heater is fast and powerful, therefore the standard RepRapFirmware default tool heater model is inappropriate. **Heater tuning must be run before using the INDX tool.** When running firmware 3.7.0-beta.3 and later, the heater must also be calibrated before it can be used, to account for small manufacturing differences between heaters. This calibration step is run when heater tuning is commanded. 

**CAUTION!** The inductive heater is fast and powerful. It can easily heat the nozzle or other metalwork placed inside the heater coil to dangerously high temperatures. Use only the correct firmware versions, and keep the firmware up to date. If the nozzle assembly is not fully inserted into the heater coil or is misaligned, this can result in the temperature being under-read, resulting in heating to a higher temperature than was intended. Do not allow paper or other flammable material to enter the heater coil area.


### Pin names

For more information on pin names, see [Pin Names](https://docs.duet3d.com/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names).

RepRapFirmware 3 uses pin names for user-accessible pins, rather than pin numbers, to communicate with individual pins on the PCB. Pins can be defined for use by a number of gcode commands, e.g. M308, M574, M558, M950.

The RepRapFirmware 3 uses the pin name format *expansion-board-address.pin-name* to identify pins on expansion board, where *expansion-board-address* is the numeric CAN address of the board. A pin name that does not start with a sequence of decimal digits followed by a period, or that starts with *0.* refers to a pin on the Duet 3 main board.

| Function | Pin location | RepRapFirmware pin name | Notes |
|---|---|---|
| Outputs | FAN (on VF board) | hsfan | Heatsink fan, VIN voltage |
| ^^ | ^^ | hsfan.tach | Pulled up to +5V |
| ^^ | FAN (on MCU board) | pcfan | Intended for print cooling fan,  VIN voltage |
| ^^ | ^^ | pcfan.tach | Pulled up to +5V |
| ^^ | LED | led | 5V drive for WS2812 or similar LED strings |
| Inputs | IO_0 | io0.in | Input with 3.3V power provided, 30V tolerant |
| ^^ | (internal) | boardtemp | MCU board temperature |
| ^^ | Coil FFC | coiltemp | Scanning Z probe coil temperature |

# Configuration


>If you change the CAN address, the CAN address in the following commands will need to change from `121` to match{.is-info}

Some of these functions require the INDX macro pack to be installed. See the [INDX Macros](/Duet3D_hardware/Duet_3_family/INDX_Toolboard#indx-macros) section below.

## Induction heater and IR temperature sensor

The thermopile sensor is configured using the M308 command with sensor type `"thermopile_tpis.object"` and pin name `"i2c"`. As well as the main output which provides nozzle temperature, it has two additional outputs which may be used for monitoring. Auxiliary output 1 has type `"thermopile_tpis.ambient"` and is the ambient temperature reported by the thermopile sensor. Auxiliary output 2 has type `"thermopile_tpis.environment"` and is the temperature of the nozzle surround reported by the auxiliary thermistor.

As at 2026-06-29 the M308 command to configure the thermopile sensor accepts the following parameters, however many of these are likely to be withdrawn in future. Only the S parameter should be needed in normal use.

- **S** Sensor number
- **R** Radiation exponent, must be between 3.8 and 4.4. The theoretical value is 4.0 but the sensor manufacturer recommends 4.2.
- **F** Object field of view times emissivity, expressed as a fraction of 256. Normally in the range 110 to 120.
- **W** Surroundings (as measured by the aux thermistor) field of view times emissivity, expressed as a fraction of 256. The sum of the F and W parameters should not exceed 256.
- **T** Aux thermistor resistance at 25C
- **B** Aux thermistor B parameter
- **C** Aux thermistor C parameter

The inductive heater is configured using the M950 command with pin name `"nozzleheat"`. The temperature sensor number in the M950 command must refer to the thermopile sensor primary output.

Example configuration, using sensor #1 for the nozzle temperature, heater #1, and the default CAN address (121):

```
M308 S1 Y"thermopile_tpis.object" P"121.i2c" A"INDX"                       ; configure thermopile main output
M308 S2 Y"thermopile_tpis.ambient" P"121.S1.1" A"Thermopile ambient"       ; configure thermopile ambient output (optional)
M308 S3 Y"thermopile_tpis.environment" P"121.S1.2" A"Hot end surround"     ; configure nozzle environment output (optional)
M950 H1 C"121.nozzleheat" T1                                               ; configure induction heater
```

### Onboard temperature sensor
This helps monitor chamber and INDX MCU board temperature.

```
M308 S10 Y"thermistor" P"121.boardtemp" A"INDXboardtemp"                   ; Onboard INDX board sensor 
```
The location of the thermistor is shown here:
![indx_thermistor.png](/duet_boards/duet_3_can_expansion/indx_thermistor.png =400x)

It is not immune from self heating on the INDX PCB, so it is not an absolute measure of the chamber temperature, but is a useful data point about the temperature of INDX mcu board which is useful, especially if running INDX in a heated chamber close to the design limits set by Bondtech.

## Extruder setup


Use the following commands, adjust if you have changed the CAN address
```
M584 E121.0  ; set extruder mapping
M350 E16 I1  ; configure microstepping with interpolation
M92 E561.4   ; equivalent to a rotation distance of 5.7mm at 16 microstepping
M566 E600    ; set maximum instantaneous speed changes (mm/min)
M203 E9000   ; set maximum speeds (mm/min)
M201 E3500   ; set accelerations (mm/s^2)
M906 E600    ; 600mA - If bondtech specify a different current use the one they recommend

```

## Fans

### Heatsink cooling Fan


The heatsink fan should be configured to run at full PWM when the nozzle is significantly above ambient temperature (e.g. above 45C). Here are suitable commands to configure it as fan #1, assuming again that the nozzle temperature sensor is sensor #1:
```
M950 F1 C"121.hsfan+hsfan.tach"     ; heatsink fan
M106 P1 C"Heatsink" H1 T45 S1       ; turn on when nozzle temperature is >= 45C
```

### Part cooling Fan

Directly connected fans
```
M950 F0 C"121.pcfan"
M106 P0 C"Part" S0                  ; turn off print cooling fan
```

if you use a directly connected part cooling solution with a tacho then:

```
M950 F0 C"121.pcfan+pcfan.tach"
```

## Neopixel or other WS2812 LED strings

Use this command to configure an LED string connected to the LED port of the INDX board:
```
M950 E0 T1 C"121.led"
```
Then use M150 commands to set the LED colours.

## Accelerometer

### Configuration

Add the following to your config.g:
```
M955 P121.0 I16 ; Add INDX accelerometer 
```
See [M955](/User_manual/Reference/Gcodes/M955) for how to setup and configure the accelerometer.

#### Orientation

![duet3_indx_v1.0_accelerometer.png](/duet_boards/duet_3_can_expansion/duet3_indx_v1.0_accelerometer.png)

In the normal INDX mounting orientation, with tools picked up from the front Z+ of the accelerometer is +Y on the machine, and +X is oriented to -Z. So the correct command is 
```
M955 P12.1 I16
```
If you have tools mounted on the rear instead and the INDX head mounted backwards, then Z+ of the accelerometer is -Y, and +X is oriented to -Z. so the correct command is
```
M955 P12.1 I56
```
### Calibration and usage

For an overview of using accelerometers to capture data on axis movement see: [Connecting an accelerometer](/User_manual/Connecting_hardware/Sensors_Accelerometer)

## Loadcell

The load cell in the INDX toolhead is used as a Z probe: the nozzle probes the bed directly and the probe triggers when the contact force reaches the configured threshold. Load cell probing needs RepRapFirmware 3.7.0-beta.3 or later on both the INDX tool board and the main board; with older firmware on either side the probe will not start.

Add the following to your config.g:

```
M558 K0 P12 C"121.loadcell" V0.11
G31 K0 P70 Z0
```

Probe type 12 is a load cell probe. The trigger comparison runs on the tool board at the full ADC sample rate (about 1.3kHz), so the trigger latency is around a millisecond and probing speeds of 300mm/min are practical.

`M558 V` is the load cell scale in grams per raw ADC count and is required for this probe type. The INDX calibration macros described below determine it from the known tool locking force (about 1600g). The sign of V must be chosen so that the force reported in the object model (`sensors.probes[0].loadCell.force`, shown in DWC) goes positive when the nozzle is pushed towards the bed. Test this by pressing the nozzle upwards by hand with a tool locked; if the force reading goes negative, negate V. Pin inversion (`!`) is not supported on the load cell input.

`G31 P` is the trigger force in grams. The firmware tares the load cell automatically when a probing move starts, so the threshold is relative to the resting force at that moment and no manual tare is needed before probing. Between probing moves the baseline tracks slow drift by itself, so the displayed force stays near zero while the machine is idle; a step change such as locking or unlocking a tool is absorbed within a few seconds, or immediately by sending `M558.4 K0`. 40 to 70g is a reasonable starting point.

Optionally `M558 U<low>:<high>` sets a safe window in grams for the preload, i.e. the resting force latched by the tare (`sensors.probes[0].loadCell.preload`). A probing move is refused if the preload is outside the window when the move starts. This catches probing without a locked tool or with a badly seated tool.

>Test in the air before the first real probe: start a probing move well above the bed and press the nozzle upwards by hand. The move must stop immediately. This verifies the threshold and the sign of V without risking a head crash.{.is-warning}


## SZP

The scanning z probe coil, if attached, is setup as a second Z probe. It integrates the same inductive sensing chip as the [Duet 3 Scanning Z Probe](/Duet3D_hardware/Duet_3_family/Duet_3_Scanning_Z_Probe). It allows for a point mesh of the bed to be built up quickly as no movement in Z is required to read the bed distance, and individual readings happen very quickly.

### Mounting

The INDX tool has an optional mount for the SZP coil that should be used. It ensures correct mounting distance from the bed. It places an offical Bondtech SZP coil 3mm above the nozzle, centered on X and 35.1mm on +Y relative to the nozzle, assuming the tool is mounted to pick up tools at Ymin (as is conventional). (Measured in CAD)

If an alternative mounting solution is used then aim for a 3mm offset between the nozzle and the coil.

### Configuration

Add the following to your config.g:
```
; Scanning Z probe
M558 K1 P11 C"121.i2c.ldc1612" F12000 T12000
M308 S10 Y"thermistor" P"121.coiltemp" A"SZP coil temp" ; thermistor on SZP coil
M558.2 K1 S15 R134990
G31 K1 X0 Y35.1 Z3.5 ; set SZP probe trigger value, offset and trigger height
; Mesh Bed Compensation
M557 X-100:100 Y-100:100 S10 ; define grid for mesh bed compensation probe 2
```
>The M558.2 parameters need to be calibrated, see the next section.
>
>The M557 mesh parameters need to be set to your bed co-ordinates that the coil can reach. The example is for a 200x200 bed with the zero point in the center{.is-info}

### Calibration and usage

For general information about SZP calibration and usage, see [Scanning Z Probe calibration](/User_manual/Tuning/scanning_z_probe_calibration)


## Endstop

The endstop input on tool can be used for any digital IO function. The most common use is to home the tool along the X axis. The configuration line for this is:
```
M574 X1 P"121.io0.in" S1 ; configure X axis endstop on the low end of the X axis
```

## Motor encoder

To follow. This requires a diametrically polarised magnet attached to the back of the motor shaft and the INDX MCU mounted ~1mm from the magnet. At the time of writing (11 August 2026) this magnet was not being provided in INDX units.

For testing the following command will report the angle and encoder status are in M122 after the encoder is configured
```
M569.1 P121.0 T3
```

# INDX Macros

These macros are a work in progress. This section describes the macros as a whole, see individual function parts of the documentation for how to use them.

## Global variables

Global variables are used to synchronise information between the various macros for INDX calibration and tasks such as load cell probing To make it easier to manage these variables are contained in `0:/sys/INDX_variables.g` which is put in the sys directory as part of the macros bundle. Add `M98 P"INDX_variables.g` to the end of config.g to run this file on startup.

### INDX Write State

Some global variable values that are set during calibration routines or tool changes need to persist between machine reboots. The `0:/sys/INDX_WRITE_STATE.g` macro writes these variables to `0:/sys/indx-state.g` which is run at the end of `0:/sys/INDX_variables.g` to restore saved variables.

Currently the active tool is written every tool change. This will be made optional in the future to reduce SD card wear.

## Tool management macros
`0:/sys/INDX_OPEN.g` - Open the tool
`0:/sys/INDX_CLOSE.g` - Normal close of the tool

## Loadcell Macros


### Calibration
In order to calibrate and then probe with the load cell the following macros are used:
`0:/sys/INDX_LC_CALIBRATE.g` - A guided calibration routine that prompts the user to take steps to achieve load cell calibration and saves the calibration
`0:/sys/INDX_TARE.g` - Stores the raw load cell value when no tool is mounted
`0:/sys/INDX_CLOSE_CAL.g` - Locks + seats the full ~1600 g force onto the cell
`0:/sys/INDX_LC_CAL.g` - Computes grams/count against the known force.

### Z Probing

`0:/sys/homez.g` - Due to the requirements to tare just before each probe, and carry out multiplle probes to establish Z0 base your homez.g off this macro.
`0:/sys/bed.g`  - for 3 point bed levelling (e.g. on a voron trident).
`0:/sys/mesh.g`  - for bed mesh using the loadcell (as opposed to the SZP.
`0:/sys/INDX_LC_ZTRIGGER.g` carry out a Z probe with a tare just before the movement.




