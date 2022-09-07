---
title: Connecting thermistors and PT1000 temperature sensors
description: 
published: true
date: 2022-09-07T15:13:45.435Z
tags: 
editor: markdown
dateCreated: 2021-09-07T16:02:20.373Z
---

# Scope

**This document is relevant to:** all Duet boards
**Firmware versions:** all firmware versions

# Introduction

This section describes how to connect, configure and calibrate thermistors and PT1000.

**Note:** PT100 sensors are not the same as PT1000 sensors and cannot be connected directly to thermistor inputs. See [Connecting PT100 temperature sensors](/User_manual/Connecting_hardware/Temperature_connecting_PT100) for how to use PT100 sensors.

# Connecting thermistors or PT1000 sensors

Connect thermistors or PT1000 sensors to the appropriate pin headers on the Duet board. 

* **Duet 3 Mainboard 6HC** has four temperature inputs, temp0 to temp3.
* **Duet 3 Mini 5+** and **Duet 3 Expansion board 3HC** have three temperature inputs, temp0 to temp2. 
* **Duet 3 Toolboard 1LC** has two temperature inputs, temp0 and temp1. 
* **Duet 2 WiFi / Ethernet and Maestro**, use thermistor inputs 1, 2 and 3, which are labelled BED_TEMP, E0_TEMP and E1_TEMP. 

Thermistors and PT1000 sensors are not polarised, so can be connected either way around.

Make sure that there are no short circuits between your temperature sensor wiring and any other wiring (particularly heater and fan wiring, which will be at a higher voltage), or you may damage your Duet board.

## PT1000 sensor resolution

PT1000 sensors connected to thermistor inputs have lower resolution than PT100 sensors connected via the PT100 daughter board. The accuracy of PT1000 sensors should be very good on the Duet 3 and Duet 2 Maestro and generally good on the Duet 2 Wifi and Ethernet. You can calibrate the Duet's on-board analogue-to-digital converter to improve accuracy; see "Temperature calibration and ADC tuning" below.


# Firmware configuration

To achieve accuracy in temperature reporting, it is essential that the firmware has an accurate model of the temperature sensor. This section covers commands needed to configure the firmware to do this.

## General configuration

## Tabs {.tabset}

### RepRapFirmware 3.x

In RepRapFirmware 3 you first create a sensor using [M308](/User_manual/Reference/Gcodes/M308) then assign it to a heater created using [M950](/User_manual/Reference/Gcodes/M950).

**Thermistor** and heater example:

```
;Duet 3 
M308 S0 P"temp0" Y"thermistor" T100000 B4725 C7.06e-8 ; sensor 0
M950 H0 C"out1" T0 ; create heater 0 and map sensor 0

;Duet 3 with CAN-connected toolbard
M308 S1 P"121.temp0" Y"thermistor" T100000 B4725 C7.06e-8 ; sensor 1 (toolboard)
M950 H1 C"121.out0" T1 ; create heater 1 and map sensor 1 (toolboard)

;Duet 2
M308 S1 P"e0temp" Y"thermistor" T100000 B4092 ; sensor 1
M950 H1 C"e0heat" T1 ; create heater and map sensor 1
```

**PT1000** and heater example:

```
;Duet 3 with PT1000
M308 S1 P"temp1" Y"pt1000" ; configure sensor 1 as PT1000 on pin temp1
M950 H1 C"out1" T1 ; create heater 1 and map sensor 1

;Duet 3
M308 S1 P"121.temp0" Y"pt1000" ; sensor 1 (toolboard)
M950 H1 C"121.out0" T1 ; create heater and map sensor 1 (toolboard)

;Duet 2
M308 S1 P"e0temp" Y"pt1000" ; sensor 1
M950 H1 C"e0heat" T1 ; create heater and map sensor 1
```

### RepRapFirmware 2.x

In RepRapFirmware 2.x on Duet 2, BED_TEMP, E0_TEMP and E1_TEMP map, by default, to Bed temperature, first heater temperature and second heater temperature respectively. You can use different temperature input pins for a heater, by remapping the temperature sensor input pins; see "Remapping temperature sensors to different pins" below. 

The thermistor or PT1000 types are configured using the [M305](/User_manual/Reference/Gcodes/M305) commands in config.g. The heaters and temperature sensor channels are numbered 0 for the bed, then 1, 2, 3... for each extruder.

For PT1000, use the M305 command as for a thermistor, but add parameter X500+n where N is the channel number. Other parameters are not required.

**Thermistor** example:

```
M305 P0 T100000 B3950 ; set bed thermistor parameters
```

**PT1000** example:

```
M305 P1 X501 ; heater 1 uses a PT1000 connected to thermistor channel 1
```


## Configuring multiple sensors and heaters

## Tabs {.tabset}

### RepRapFirmware 3.x

In RRF 3.x, a typical config.g would define the bed and hot end temperature sensor definitions with M308 and map them to the relevant heater created with M950:

```
M308 S0 P"bedtemp" Y"thermistor" T100000 B3950 ; sensor 0
M950 H0 C"bedheat" T0  ; create bed heater and map sensor 0
M308 S1 P"e0temp" Y"thermistor" T100000 B4725 C7.06e-8 ; sensor 1
M950 H1 C"e0heat" T1 ; create heater and map sensor 1
M308 S2 P"e1temp" Y"thermistor" T100000 B4138 ; sensor 2
M950 H2 C"e1heat" T2 ; create heater and map sensor 2
```

### RepRapFirmware 2.x

In RRF 2.x, the P parameter in M305 refers to the temperature sensor AND heater channel, where P0 is the bedtemp pins, P1 is e0temp (first hotend) and P2 is e1temp (second hotend). A typical config.g might be:

```
;RRF 2.x
M305 P0 T100000 B3950 ; set parameters for heater 0
M305 P1 T100000 B4725 C7.060000e-8 ; set parameters for heater 1
M305 P2 T100000 B4138 ; set parameters for heater 2
```

## Remapping temperature sensors to different pins

## Tabs {.tabset}

### RepRapFirmware 3.x

In RRF 3.x, you can simply change the pin allocation in the P parameter in the M308 command to use a different pin. For example, if you want the bed thermistor connected to e0temp (assuming it is available), use:

```
;RRF 3.x
M308 S0 P"e0temp" Y"thermistor" T100000 B3950
```

### RepRapFirmware 2.x

In RRF 2.x, use the X parameter in M305 to do this, where X is the desired thermistor input channel. See [M305](/User_manual/Reference/Gcodes/M305) for a list of available input channels:

```
;RRF 2.x
M305 P0 X1 T100000 B3950 ; set bed thermistor parameters
```



## Configuring a temperature sensor with no heater

## Tabs {.tabset}

### RepRapFirmware 3.x

In some cases, you'll want a temperature sensor that is not mapped to a heater.

RRF3.x handles this by allowing sensors to be defined independently of heaters, up to a max of 32 sensors. These will be shown on the 'Extras' section of the 'Tools + Extras' tab in DWC. Example:

```
;RRF 3.x
M308 S3 A"Chamber" P"e1_temp" Y"thermistor" T100000 B3950
```

### RepRapFirmware 2.x

In some cases, you'll want a temperature sensor that is not mapped to a heater.

In RRF 2.x, for sensors with no controllable heater, you need to create a "virtual heater" in order to be able to use the sensor. Use P parameter with number 103 or higher (101, 102 and 103 are reserved for the MCU and stepper driver temperatures see [Heaters](/User_manual/Connecting_hardware/Heaters_overview)), define a sensor name with the S parameter (which will make it show up in DWC), define the "heater" ADC channel with the X parameter (ie the pins the temperature sensor is connected to), and then define temperature sensor values (T, B, C). eg:

```
;RRF 2.x
M305 P103 S"Chamber" X2 T100000 B4725 C7.060000e-8
```

## Using multiple temperature sensors for a single heater

There are three ways of using two thermistors to control one heater:

1. Connect them in series. The temperature readings will be averaged with a bias in favor of the colder one. If any wire breaks, you will get the usual -273C reading and the heater will fault.
1. Connect them in parallel. The temperature readings will be averaged with a bias in favor of the hotter one. If one of the thermistor wires breaks, the temperature will under-read, so you will end up heating too much.
1. Use one to control the temperature, and the other as an over-temperature cutout, configured using M143.

Note: if connecting 2 in series, the resistance of the thermistors needs to be combined in config.g. So if each thermistor is 100k at room temperature, they need to be set as 200k when declaring the sensor with M308. And if connecting in parallel, the resistances need to be half their combined average (or just half of one if both are the same).

However, when connecting thermistors in series or in parallel, although the B value remains the same, the effective C value changes. So it's easier to adjust the R parameter instead. For example, on a Duet 3 the normal R value is 2200. If you connect two identical thermistors in parallel, you can specify the usual T, B and C parameters for the thermistors, but double R to 4400. Likewise, if connecting two thermistors in series, halve the R value.

## Thermistor parameters

## Tabs {.tabset}

### RepRapFirmware 3.x

In RRF 3.x, the parameters of the [M308](/User_manual/Reference/Gcodes/M308) command define the temperature sensor characteristics. For example, to set the first hot end thermistor parameters, use a command like this:

```
;RRF 3.x
M308 S1 P"e0temp" Y"thermistor" T100000 B4725 C7.06e-8 ; sensor 1
```

The **S** parameter configures the sensor number the command applies to.

The **P** parameter defines the pins the sensor is connected to.

The **Y** parameter defines the sensor type, "thermistor" or "pt1000".

The **T** parameter (T100000) means that the resistance at 25C is 100Kohms.

The **B** parameter (B4725) means that the thermistor B value over the temperature range of interest is 4725.

### RepRapFirmware 2.x

In RRF 2.x, the parameters of the [M305](/User_manual/Reference/Gcodes/M305) command define the temperature sensor characteristics. For example, to set the first hot end thermistor parameters, use a command like this:

```
;RRF 2.x
M305 P1 T100000 B4725 C7.060000e-8 ; set parameters for heater 1
```

The **P** parameter identifies the heater/thermistor channel that the command applies to, where P0 is bedtemp, P1 is E0TEMP, P2 is E1TEMP. P3 to P7 are the temperature inputs on the Duex expansion board.

The **T** parameter (T100000) means that the resistance at 25C is 100Kohms.

The **B** parameter (B4725) means that the thermistor B value over the temperature range of interest is 4725.

The **C** parameter is optional, but should give greater accuracy if quoted by the manufacturer (RRF v1.17 and later).

## Typical parameter values 

Manufacturers of thermistors typically quote the B value over quite a small temperature range. This is fine for the bed thermistor, but the effective B value over the temperature range of the extruder may be somewhat different.

Here are some suitable T, B and C values for popular thermistors:

| Thermistor type | Used by | T parameter (resistance @ 25C) | B parameter | C parameter |
|:---|:---|
| Semitec 104GT2 | E3D hot ends | 100000 | 4725 | 7.06e-8 ^1^ |
| Epcos B57863S0103F040 | RepRapPro Ormerod heated bed | 10000 | 3988 |  |
| Honeywell 135-104QAD-J01 | RepRapPro Ormerod/Huxley/Mendel hot end | 100000 | 4138 |  |
| Typical Chinese bed heater thermistor | Various bed heaters | 100000 | 3950 |  |

^1^ The C parameter can be written in [Scientific notation](https://en.wikipedia.org/wiki/Scientific_notation) as shown here or in normal decimal notation (in this example C0.0000000706, just be careful to use the right number of zeros); see below for an explanation of the C parameter.

If you have a datasheet for the thermistor you are using, and if it provides a resistance-versus-temperature table, you can calculate the correct value for the B and C parameters. The simplest way is to use the calculator on the heater tab of https://configurator.reprapfirmware.org/.

For the technically minded: firmware 1.17 and later uses the Steinhart-Hart thermistor model. The M305/M308 C parameter is the Steinhart-Hart **c** parameter, the M305/M308 B parameter is the reciprocal of the Steinhart-Hart **b** parameter, and the Steinhart-Hart **a** parameter is calculated from the resistance at 25C. If you don't provide a C parameter, then C defaults to zero and the Steinhart-Hart equation reduces to the beta-value equation.

## The M305/M308 R parameter

The **R** parameter is the value (in ohms) of the series resistor, which is part of the temperature sensing circuit on the controller electronics. You do not need to specify this value as it is set by the firmware which detects the hardware used and sets the value accordingly.

The default values for Duet boards are:

| Duet version | R value | Notes |
|:---|:---|:---|
| Duet 3 | 2200 | |
| Duet 2 WiFi / Ethernet | 4700 | |
| DueX2 and DueX5 expansion boards (up to v0.10) | 4700 | |
| DueX5 expansion board (from v0.11)  | 2200 | See [PCB revision notes](https://docs.duet3d.com/Duet3D_hardware/Duet_2_family/DueX2_and_DueX5#pcb-revision-history). |
| Duet 2 Maestro | 2200 | |

# Testing temperature sensors

## At room temperature

To test that the temperature sensors are working at room temperature:

- Apply 5V power only to the Duet by connecting it to a PC via the USB cable.
- Connect to the Duet web interface using a browser
- Look at the Dashboard -> Printer Status -> **Tools** section
- Tools, beds and/or chambers that are correctly configured with temperature and associated heater should be displayed. The temperature reading in the **Current** column should be close to room temperature.
- Temperature sensors not related to Tools, beds or chambers are listed on the **Extras** tab.
- Each temperature reading should fluctuate by no more than about 0.2C, except that at temperatures below about 10C the reading from a thermistor may fluctuate by more than this.
- If you get a temperature of -273°C, this indicates an open circuit, i.e. nothing is connected to the defined pins:
  - Check that the temperature sensor is connected to the correct pins
  - Check the wiring for breaks
  - Measure the resistance of the wires that connect to the Duet, and that it corresponds with what the firmware expects
  - Check that the configuration is set correctly for the temperature sensor.
- If you get a temperature of +2000°C, this indicates a dead short between the temperature sensor pins on the Duet, or mis-configured firmware.
  - Check your M305/M308 configuration
  - Check the wiring isn't grounding out to something

Don't worry if your heaters show temperatures that differ by a few degrees - this is quite common, especially when the sensors are thermistors (which are not very accurate at room temperature).

## When heating

Once the thermistors have been checked for proper operation when cold, check they report correctly when the associated heater is turned on. Mains power will need to be connected and turned on.

- On the Dashboard -> Printer Status -> Tools section, enter a number in the "Active" box for the first heater. Start off with a low number such as 35°C.
- You should see the corresponding temperature begin to rise. It is possible that it will overshoot the set temperature a bit, this is OK.
- Complete this step by selecting "Tool 0" and "Deselect Tool" to put the tool into standby.
- Repeat this step for the other tools, bed and chamber heaters.

> **WARNING:** If you enable the heater but do not observe an increase in the temperature reading, turn off the controller immediately and check your wiring.
{.is-warning}

It is recommended that you tune your heaters after ensuring their functionality; see [Tuning heater control](/User_manual/Connecting_hardware/Heaters_tuning)



# Temperature calibration and ADC tuning

The analog-to-digital converters (ADCs) in microcontrollers have gain and offset errors that vary between one chip and another. These errors in turn introduce errors in the temperature readings taken from thermistors and PT1000 sensors that are connected to the thermistor input pins.

Duets may need their analog-to-digital converters (ADCs) calibrated to report accurate temperatures. Using M305 (RRF 2.x) or M308 (RRF 3.x) H and L parameters, it is possible to tune the Duet's ADCs high and low gain offset to improve readings from thermistors and/or PT1000 temperature sensors. 

There has also been discussion of a number of workarounds; a search of the forum may yield some good suggestions. For really satisfactory results you may need to switch to a thermocouple, PT100  or PT1000 sensor.

## When to calibrate

Duets provide automatic, semi-automatic (in some cases) and manual methods of calibration. Under some conditions the residual errors may be high enough to warrant correction. In particular:

* When high resistance thermistors are used (e.g. the ones sold by Dyze Design and Slice Engineering), the readings at room temperature may be wildly inaccurate and cause heater faults unless the high-end ADC error is corrected.
* Duet 3 Mainboard 6HC and 6XD have two banks of ADC inputs, and sometimes exhibit different errors on different ADC banks. The on-board hardware can only apply the same compensation to both banks. As a result, the hardware self-calibration isn't perfect.

## Auto-calibration

All Duets have some degree of self-calibration to measure and cancel out gain and offset errors, either in the ADC itself or in external hardware.

* **Duet 3** and **Duet Maestro** boards have auto-calibration hardware built into the board. This is quite effective, but not perfect (see note above on 6HC/6XD).
* **Duet 2 WiFi/Ethernet** has auto-calibration built into the chip, but the lack of calibration hardware built into the board makes this less accurate.

If you find the auto-calibration is not accurate enough for your requirements, we suggest:

* For **Duet 3** and **Duet Maestro** boards, the semi-automatic calibration procedure (see below) can be done, which involves using the M308 H999 and L999 parameters with the thermistor input open and shorted respectively. 
* **Duet 3** boards have that calibration procedure run during initial factory testing, and the results are stored in flash memory to provide the default L and H parameters. Those values get overwritten if you re-run the calibration procedure.
* For **Duet 2 WiFi/Ethernet** we recommend the manual calibration procedure (see below), that uses resistors to calibrate if greater accuracy is needed.

## Semi-automatic calibration

This facility is supported in RepRapFirmware 3.2 and later for Duet 3 and Duet Maestro main boards, and for Duet 3 EXP3HC and TOOL1LC boards. The procedure is:

1. Configure the inputs to be calibrated as thermistors or PT1000 sensors
1. Disconnect the thermistors and PT1000 sensors from all thermistor inputs on the Duet main board or Duet expansion board to be calibrated
1. For each input to be calibrated send **M308 Snn H999** where nn is the sensor number. RepRapFirmware will report the measured H correction and store it in nonvolatile memory.
1. Place a jumper across each thermistor input to be calibrated
1. For each input to be calibrated send **M308 Snn L999** where nn is the sensor number. RepRapFirmware  will report the measured L correction and store it in nonvolatile memory.
1. You can now remove the jumpers and reconnect the thermistors or PT1000 sensors. When you use M308 commands to configure thermistors or PT1000 sensors, RepRapFirmware will default the L and H parameters to the values that were stored in nonvolatile memory.

## Manual calibration

The following is for RepRapFirmware 3.x for all boards except the Duet Maestro, but should also work in RepRapFirmware 2.x, by changing M308 command to the appropriate M305 commands.

1. Configure the input to be calibrated as a thermistor
1. Connect a resistor to the input, with the resistance being the same as the thermistor resistance at 25C. For example, if you are using a standard 100K thermistor as supplied with E3D hot ends, use 100K. If you are using a Dyze Design or Slice Engineering high temperature thermistor, use 500K.
1. Send **M308 Snn Hhhh** (where nn is the sensor number) for various value of hhh until you find the value of hhh that gives the reading closest to 25C. Higher values of hhh increase the temperature reading. The allowed range of hhh is -127 to 127.
1. If you wish to calibrate the low-end error, then replace the resistor with a value corresponding to a high temperature, for example 100 or 220 ohms. Then send **M308 Snn Lvvv** for various values of vvv until you find the value that gives a reading close to the expected value for that resistance using the thermistor settings you have chosen. For example, if you are using the standard parameters for E3D thermistors (T100000  B4725 C7.06e-8) then the expected reading is 285C for 100 ohms, or 236.5C for 220 ohms.
1. Include the L and H parameters you found in your M308 commands in config.g.
