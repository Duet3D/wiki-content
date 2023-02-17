---
title: Connecting a Z probe
description: This page describes how to connect a variety of Z probes to the Duet hardware.
published: true
date: 2023-02-17T14:30:55.816Z
tags: z probe
editor: markdown
dateCreated: 2021-04-28T10:34:14.769Z
---

# Overview

A Z probe can be used to accurately, automatically and repeatably set the Z height, assist in the manual adjustment of the bed level, and/or allow automatic correction/compensation for errors of bed squareness and flatness. On a delta machine, it can automatically calibrate the machine dimensions.

Differing Z probe technologies vary in their ability to deliver reliability, simplicity, accuracy and low cost and no single type predominates. For a comparison of advantages and disadvantages for various Z probe options see [Choosing a Z probe](/User_manual/Connecting_hardware/Z_probe_choosing).

Once you have the probe connected, see how to:

- [Test and calibrate a Z probe](/User_manual/Connecting_hardware/Z_probe_testing)
- [Setting up automatic probing of the print bed](/User_manual/Connecting_hardware/Z_probe_auto_probing)
- [Using the Manual Bed Levelling Assistant](/User_manual/Connecting_hardware/Z_probe_manual_levelling)
- [Bed levelling using multiple independent Z motors](/User_manual/Connecting_hardware/Z_probe_auto_levelling)
- [Mesh bed compensation](/User_manual/Connecting_hardware/Z_probe_mesh_bed)

If you don't have a Z probe, you can still use mesh bed compensation and other features normally associated with a Z probe. See Mode 0 below.

# Duet Z probe pins

You can connect a Z probe directly to your Duet.

- On **Duet 3** boards, you can connect a probe to any IO connector. Some probes may need specific capabilities e.g. analog input or PWM output; see the hardware overview for your board for IO connector capabilities.
- The **Duet 2 WiFi / Ethernet** provides a dedicated 4-pin connector.
- The **Duet 2 Maestro** provides a dedicated 5-pin connector.

> **Caution! The following table shows the pinout out by function, and NOT the physical order. Check the wiring diagram of your Duet for the correct pinout order. For example, the pinout of the 5-pin IO_ connectors on Duet 3 is NOT the same as the pinout of the 5-pin Z Probe connector on Duet 2 Maestro!**
{.is-warning}

| Duet 3 IO_x pin label | Duet 2 WiFi/Ethernet pin label | Duet 2 Maestro pin label | Purpose |
|:---|:---|:---|:---|:---|:---|
| IN | IN | IN | Input from Z probe |
| GND | GND | GND | Ground |
| OUT | MOD | MOD | Control signal for some types of Z probe |
| 3.3V | 3.3V | 3.3V | Power to Z probe |
| 5V | - | 5V | 5V power to Z probes that need it (e.g. BLTouch) |

> Note: The MOD pin on the Duet 2 WiFi/Ethernet is NOT PWM capable, so can't be used to control a servo. The OUT pin on Duet 3 and the MOD pin on the Duet 2 Maestro are PWM capable, and can control a servo (eg to deploy BLTouch).
{.is-info}

# Z probe modes selectable in firmware

The [M558](/User_manual/Reference/Gcodes/M558) Gcode configures the firmware for your Z probe. Using the P parameter configures the firmware to recognise the output mode of the Z probe you are using. For example, M558 P1 selects Z probe mode 1.

* You can use a Z probe with either an analog or a digital output; just set the mode in M558 accordingly. 
* Some types of Z probe with digital output should be connected to the E0 endstop connector instead of the Z probe connector.
* In firmware versions before 3.2, when using a probe with analog output, the firmware will slow down the probe speed when it is getting close to trigger height.

### Tabs {.tabset}

#### RepRapFirmware 3.x

* If updating from RRF 2.x, and the probe used type 4, 6 or 7, use type 5 or 8 instead, and define the pin the probe is connected to with the C parameter in M558.
* Probes connected to Duet 3 expansion boards must be probe type 8 or 9 (stall homing not currently supported on expansion boards).
* In firmware versions from 3.2, you can use the F parameter in M558 to specify a fast Z feedrate for the first probing move, followed by a slower second probing move, for any probe. eg M558 F600:120

The following table gives an overview of the different Z probe modes.

| Mode | Probe type | Control signal (MOD or OUT pin) |
|:---|:---|
| 0 | No probe | Not used |
| 1 | Analogue probe | HIGH |
| 2 | Analogue probe with modulation | Modulation (IR LED on/off control) |
| 3 | Analogue probe | LOW |
| 5 | Digital probe | HIGH during probing, LOW at other times |
| 8 | Digital probe, unfiltered | HIGH during probing, LOW at other times |
| 9 | BLTouch | OUT (Duet 3) and MOD (Duet 2 Maestro) can be configured to control deployment/retraction. MOD on Duet 2 WiFi/Ethernet is not PWM capable, so use heater pin on expansion port instead. |
| 10 | Z motor stall detection | Not used |

**Z Probe Mode details**

##### Mode 0

Select this mode if you have no Z probe. When the firmware tries to execute a command to probe the bed, it will instead show a dialogue in Duet Web Control, and also on Panel Due, asking you to jog the head down until the nozzle just touches the bed and then press the OK button.

##### Mode 1

This is a probe with an analog output connected to the Z probe connector. The probe output must rise as it gets closer to the bed. If the probe output falls as it gets closer, invert the probe output by prefixing the input pin name (C parameter) with ! character, in the M558 command. The control signal is driven HIGH.

##### Mode 2

This is a probe with an analog output that requires the Duet to provide modulation signal and demodulate the returned signal. The probe output rises as it gets closer to the bed. If the probe output falls as it gets closer, invert the probe output by prefixing the input pin name (C parameter) with ! character RRF 3.x in the M558 command. The Duet drives the control signal with a 250Hz square wave. The firmware extracts that part of the analog signal received on the IN pin that is in phase with the modulation.

##### Mode 3

As mode 1 except that the control signal is driven LOW.


##### Mode 5

A switch or digital output device connected between the STP or IN and GND terminals of the connector. The only pullup resistor is the 100K nominal pullup in the microcontroller so the sink current requirement is tiny. The input must be active high when triggered.

Select the input pin with the C parameter in the M558 command. Invert the probe output to select active low by prefixing the input pin name (C parameter) with ! character in the M558 command

##### Mode 8

Similar to mode 5 except that the input is not filtered, for slightly faster response. Supported in firmware 1.20 and later.

##### Mode 9

Special mode for BLTouch probe. Supported in firmware 1.21 and later.

##### Mode 10

Use the Z motor stall detection as the Z probe trigger. Supported in firmware 1.21 and later. There are limitations to [stall detection](/User_manual/Connecting_hardware/Sensors_stall_detection) and it is not always appropriate for accurate Z probing, however there is a [detailed discussion on the forum](https://forum.duet3d.com/topic/4772/motor-stall-detection-as-z-probe) and some users have had success.

#### RepRapFirmware 2.x

The following table gives an overview of the different Z probe modes.

| Mode | Probe type | Control signal (MOD or OUT pin) |
|:---|:---|
| 0 | No probe | Not used |
| 1 | Analogue probe | HIGH |
| 2 | Analogue probe with modulation | Modulation (IR LED on/off control) |
| 3 | Analogue probe | LOW |
| 4 | Digital probe connected to E0 endstop input, or endstop input set by C parameter | Not used |
| 5 | Digital probe connected to Z Probe input | HIGH during probing, LOW at other times |
| 6 | Digital probe connected to E1 endstop input | Not used |
| 7 | Switch triggering Z endstop | Not used |
| 8 | Digital probe connected to Z Probe input, unfiltered | HIGH during probing, LOW at other times |
| 9 | BLTouch with output connected to Z Probe input | OUT (Duet 3) and MOD (Duet 2 Maestro) can be configured to control deployment/retraction. MOD on Duet 2 WiFi/Ethernet is not PWM capable, so use heater pin on expansion port instead. |
| 10 | Z motor stall detection | Not used |

**Z Probe Mode details**

##### Mode 0

Select this mode if you have no Z probe. When the firmware tries to execute a command to probe the bed, it will instead show a dialogue in Duet Web Control, and also on Panel Due if you are running recent Panel Due firmware, asking you to jog the head down until the nozzle just touches the bed and then press the OK button.

##### Mode 1

This is a probe with an analog output connected to the Z probe connector. The probe output must rise as it gets closer to the bed. If the probe output falls as it gets closer, invert the probe output by adding the I1 parameter in the M558 command. The control signal is driven HIGH.

##### Mode 2

This is a probe with an analog output that requires the Duet to provide modulation signal and demodulate the returned signal. The probe output rises as it gets closer to the bed. If the probe output falls as it gets closer, invert the probe output by adding the I1 parameter, in the M558 command. The Duet drives the control signal with a 250Hz square wave. The firmware extracts that part of the analog signal received on the IN pin that is in phase with the modulation.

##### Mode 3

As mode 1 except that the control signal is driven LOW.

##### Mode 4

The trigger signal is read from the E0 endstop circuit. You can choose whether the signal is high or low when triggered. In firmware 1.15 and earlier this is done by using M574 E0 S1 to select active high, or M574 E0 S0 to select active low. In firmware 1.16 to 2.x, use I1 in the M558 command to select active low, otherwise it defaults to active high.

The device connected to the E0 STP pin must be able to sink 1.5mA for the Duet 2 WiFi and Duet 2 Ethernet, 0.3mA for the Duet 2 Maestro, or 2.1mA for the Duet 0.6 or 0.8.5. In firmware 2.03 and later you can choose a different endstop input using the C parameter of the M558 command.

##### Mode 5

A switch or digital output device connected between the STP or IN and GND terminals of the connector. The only pullup resistor is the 100K nominal pullup in the microcontroller so the sink current requirement is tiny. The input must be active high when triggered.

Similar to mode 4 except that the Z probe connector is used. In firmware 1.16 and later you can use the I1 parameter in the M558 command to invert the probe signal to select active low.

##### Mode 6

Similar to mode 4 except that the E1 endstop input is used. Deprecated in firmware 2.03 and later, use mode 4 with parameter C4 instead.

##### Mode 7

Similar to mode 4 except that the Z endstop input is used.  Deprecated in firmware 2.03 and later, use mode 4 with parameter C2 instead. 

##### Mode 8

Similar to mode 5 except that the input is not filtered, for slightly faster response. Supported in firmware 1.20 and later.

##### Mode 9

Special mode for BLTouch probe. Supported in firmware 1.21 and later.

##### Mode 10

Use the Z motor stall detection as the Z probe trigger. Supported in firmware 1.21 and later. There are limitations to [stall detection](/User_manual/Connecting_hardware/Sensors_stall_detection) and it is not always appropriate for accurate Z probing, however there is a [detailed discussion on the forum](https://forum.duet3d.com/topic/4772/motor-stall-detection-as-z-probe) and some users have had success.

### Configuring multiple probes

From RRF 3.1.0, you can define multiple probes.

* The K parameter in M558 selects the Z probe number. If there is no K parameter then 0 is used. You can ignore this parameter if you have only one Z probe.
* Z probe #0 can use probe types 0, 1, 2, 3, 5, 8, 9 or 10. All other probes must be probe type 0, 8, 9 or 10.

# Connecting different types of Z probe

## Mini differential IR sensor by dc42

**With Duet 2 and earlier Duets:** Connect the VCC, GND and OUT pins of the sensor to 3.3V, GND and IN respectively on the Z probe connector, and select mode 1 in the M558 command.

**With Duet 3 main board, expansion board or tool board:** Connect the VCC, GND and OUT pins of the sensor to 3.3V, GND and IN respectively on an IO probe connector, and select mode 8 in the M558 command. On Duet 3, the IR probe is always in digital mode.

See also [DC42's web page on the IR sensor](https://miscsolutions.wordpress.com/mini-height-sensor-board) for more on setting up this probe.

## Duet3D Smart Effector

See this guide for configuring the strain gauge-based probing on the Smart Effector: [Smart effector and carriage adapters for delta printer](/Duet3D_hardware/Accessories/Smart_Effector)

## RepRapPro 3-wire IR sensor (as shipped with Ormerod 1)

Connect as shown in the RepRapPro Ormerod 1 wiring instructions, using the table above to translate between expansion connector pins and Z probe connector pins if necessary, and select mode 1.

## RepRapPro 4-wire IR sensor (as shipped with Ormerod 2, Huxley Duo and Mendel)

Connect as shown in the RepRapPro Ormerod 2/Huxley Duo/Mendel wiring instructions, using the table above to translate between expansion connector pins and Z probe connector pins if necessary, and select mode 2.

## Force sensitive resistor(s)

If using JohnSL's trinket board: Connect its Vcc, Output and Ground pins to 3.3V, IN and GND on the Z-probe connector respectively and select mode 5.

If **not** using JohnSL's trinket board: Connect the FSR(s) in parallel, between +3.3V and the IN pin of the Z-probe connector, and connect a potentiometer or fixed resistor between the IN pin and the GND pin. Select mode 1. Choose the value of the fixed resistor to give reliable triggering.

With RRF 1.16 and later it is possible to connect the FSRs between IN and ground instead, and the fixed resistor as a pullup resistor between IN and +3.3V. In this case, invert the probe output by adding the I1 parameter (RRF 1.16 to 2.x), or by prefixing the input pin name (C parameter) with ! character (RRF 3.x), in the M558 command.

## Microswitch

See [Connecting endstop switches](/User_manual/Connecting_hardware/Sensors_endstops)

**RRF 3.x**: Connect it between the STP or IN and GND pins of the chosen connector and select mode 5. Select the input pin with the C parameter in the M558 command. Prefix the input pin name (C parameter) with ! character in the M558 command if you are using a normally-open switch.

**RRF 1.16 to 2.x**: Connect it between the STP and GND pins of the E0 connector and select mode 4.  Include parameter I1 in the M558 command if you are using a normally-open switch.

**RRF 1.15 or earlier**: Connect it between the STP and GND pins of the E0 connector and select mode 4. If it is a normally-closed microswitch, then use M574 E0 S1 to select active high trigger level. For normally-open contacts (not recommended), send M574 E0 S0 instead.

## Piezoelectric sensors

Most Piezo kits come with a number of sensors that connect to a small controller board. The controller board connects to the Duet. See these threads for general advice wiring up and configuring Precision Piezo sensors:

[https://forum.duet3d.com/topic/2671/](https://forum.duet3d.com/topic/2671/)

[https://forum.duet3d.com/topic/734/](https://forum.duet3d.com/topic/734/)

## NPN output normally-open inductive or capacitive sensor

**Duet 3, Duet 2 WiFi/Ethernet revision 1.04 or later, Duet 2 Maestro:** you can connect the output of the sensor directly to the Z-probe IN pin.

**Duet WiFi/Ethernet revision 1.03 or earlier:** Connect the output wire of the sensor to the cathode of a diode, and the anode of the diode to the Z probe IN pin. The diode should preferably be a small-signal Schottky diode such as BAT43 or BAT85, but a small signal silicon diode such as 1N4148 works for some people.

[![sensors_probe_npn.jpg](/manual/sensors/sensors_probe_npn.jpg =500x)](/manual/sensors/sensors_probe_npn.jpg){target=_blank}

**All Duets:**  Connect the sensor ground wire to a ground pin on the Duet, and the sensor's + power wire to a suitable voltage (typically to VIN because these sensors usually need between 6 and 30V power).

Select mode 5 (P5) in the M558 command. Invert the probe output by prefixing the input pin name (C parameter) with ! character (RRF 3.x), or by adding the I1 parameter (RRF 1.16 to 2.x), in the M558 command

## PNP output normally-open inductive or capacitive sensor

Connect the sensor output wire to one end of a resistor (call this R1), and connect one end of another resistor (call this R2) to GND on the Z-probe connector. Connect the free ends of R1 and R2 together and to the IN pin of the Z-probe connector.

For **Duet 3**: all IOx.in inputs have a 27K pullup resistor to +3.3V, therefore the value of R2 must be low enough to defeat this. We suggest R2 = 2.2K. Then the value of R1 should be 4.7K to 6.8K if the sensor is powered from +12V; or use 10K to 15K if the sensor is powered from +24V.

For **Duet 2 WiFi/Ethernet** and **Duet 2 Maestro**: The values of R1 and R2 should be chosen so that about +3V appears at their junction when triggered. If R2 is 10K, then suitable values of R1 are 30K if the sensor is powered from +12V, and 68K if it is powered from +24V. If you are using the Duet 2 Maestro or the Duet 2 WiFi/ Ethernet revision 1.04 or later, the resistor values are less critical and you can use 10K for both.

Connect the sensor ground wire to Duet ground, and the sensor's + power wire to a suitable voltage (typically to VIN because these sensors usually need between 6 and 30V). An always-on fan connector can be used for this, but make sure you get the polarity right.

Select mode 5 in the M558 command. The signal should not need to be inverted.


>**Caution!** Make quite sure that you connect the sensor output to the IN pin on the Z probe connector. There are 2 versions of the Duet WiFi wiring diagram, and one has the connector reversed with respect to the other. Make sure that the diagram you are using is for the 1.04 revision of the Duet. **Connecting the sensor output to the +3.3V pin by mistake will destroy the Duet!** Due to the risk of mis-connection, we recommend the wiring method described earlier instead, using two 10K resistors. 
{.is-warning}

> Note: on the **Duet 2 Maestro** and on **Duet 2 WiFi/Ethernet revision 1.04 or later**, you can instead connect the output of the sensor directly to the IN pin of the Z probe connector because the IN pin is 30V-tolerant. You must still connect a pulldown resistor between IN and GND of the Z probe connector. 10Kohms is a suitable value.{.is-info}


## BLTouch

See also: [BLTouch Troubleshooting](/User_manual/Troubleshooting/BLTouch_troubleshooting)

**NOTE:** CHECK YOUR WIRING! If using a clone BLTouch, or one supplied with a manufacturer's kit, the wiring colours may not match those listed below, which are correct for genuine BLTouch from [www.antclabs.com](http://www.antclabs.com).

For example, recent BLTouch kits for Creality printers use a cable with different colours and wiring. For these, check your wiring based on the following chart (see genuine BLTouch image below):

![sensors_probe_bltouch.png](/manual/sensors/sensors_probe_bltouch.png =150x)

| | GND (Left) | +5V (Left mid) | Control (Middle) | GND (Right mid) | Out (Right)
|:--|:--|:--|:--|:--|:--
| Genuine BLTouch | Brown | Red | Yellow | Black | White |
| Creality kit (some versions) | White | Black | Yellow | Blue | Red |
| Creality kit (other versions) | White | Black | Yellow | Red | Blue |

### Wiring the BLTouch

### Tabs {.tabset}

#### Duet 3

Connect the BLTouch to a PWM-capable IO header. For more information on pin header capability, see the 'Input/Output' section of the hardware overview of the board you are connecting the BLTouch to.

| Duet 3 board</br>(link to hardware overview) | PWM-capable IO headers |
|:--|:--|:--|
| [Duet 3 MB6HC](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6HC_Hardware_Overview){target=_blank} | IO_4, IO_5 or IO_7 (v0.6 and later) |
| [Duet 3 6XD](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6XD_Hardware_Overview){target=_blank} | IO_4 or IO_7 |
| [Duet 3 Mini 5+](/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Hardware_Overview){target=_blank} | IO_1, IO_2 or IO_3 |
| [Duet 3 Toolboard 1LC](/Duet3D_hardware/Duet_3_family/Duet_3_Toolboard_1LC){target=_blank} | IO_0 |
| [Duet 3 Expansion 3HC](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_3HC){target=_blank} | IO_1 or IO_4 | 
| [Duet 3 Expansion 1XD](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1XD){target=_blank} | IO_0 or IO_2 |

Connect as follows:

| IO_[n] | connector pin | BLTouch pin	Colour |
|:--|:--|:--|
| 3.3V | - | - |
| IN | OUT | White | 
| GND | GND | Black + (Brown or Blue) | 
| OUT | Control | Orange or Yellow | 
| +5V | +5V | Red | 

With older BLTouch (before V3.0), the output is 5V by default. All Duet 3 IO_inputs are 30V tolerant, so it doesn't matter whether you reduce the output of the BLTouch to 3.3V or leave it at 5V.

#### Duet 2 WiFi/Ethernet without expansion board

Connect the BLTouch as follows:

Duet Z Probe connector pin | Duet expansion connector pin | BLTouch pin | Colour || 
 :------------------------ | :--------------------------- | :---------- | :----- |
IN | | Out | White | [![sensors_probe_bltouch_d2we.png](/manual/sensors/sensors_probe_bltouch_d2we.png =500x)](/manual/sensors/sensors_probe_bltouch_d2we.png){target=_blank} |
GND | | GND | Black | ^^ |
| | 5V (pin 1) | +5V | Red | ^^ |
| | Heater3 (pin 8) | Control | Orange or Yellow | ^^ |
| | GND (pin 2) | GND | Brown or Blue | ^^ |

##### For all Duet 2 WiFi/Ethernet boards prior to version 1.04

If you have the older BLTouch (before V3.0), the output is 5V by default. However, the Z probe input on Duet 2 WiFi/Ethernet boards prior to version 1.04 is not 5V tolerant.

* **Either:** Connect a 240 ohm resistor between Probe GND and Probe IN.
* **Or:** Cut the PCB trace on the BLTouch to reduce the output to 3.3V as described in the instructions for your BLTouch version. See https://www.antclabs.com/

All BLTouch V3.0 and later are 3.3V output by default, though can be set to 5V output.

The Z probe input on the Duet 2 WiFi/Ethernet rev 1.04 and later is 30V tolerant, so it doesn't matter whether you reduce the output of the BLTouch to 3.3V or leave it at 5V.

#### Duet 2 WiFi/Ethernet with Duex 2, Duex 5 or Expansion Breakout Board

First, you need to allocate an unused heater expansion channel to reconfigure as the servo control for the BLTouch. For example, if the E2Heat output is unused, you can use heater 3 which corresponds to the PWM1 connector.

If you have a Duex v0.9 or v0.9a board, check that you have a jumper on the "5V AUX JUMPER SELECT PINS" between the 5V AUX and 5V INT pins. See [this thread](https://forum.duet3d.com/topic/10654/bl-touch-and-the-duex-5/64){target=_blank} on the forum for details.

Connect the BLTouch as follows:

| Duet Z Probe connector pin | DueX PWM connector pin | BLTouch pin | Colour |
|:---|:---|:---|:---|
| IN | - | Out | White|
| GND | - | GND | Black|
| - | +5V | +5V | Red|
| - | PWM | Control | Orange or yellow|
| - | GND | GND | Brown or blue |

##### For all Duet 2 WiFi/Ethernet boards prior to version 1.04

If you have the older BLTouch (before V3.0), the output is 5V by default. However, the Z probe input on Duet 2 WiFi/Ethernet boards prior to version 1.04 is not 5V tolerant.

* **Either:** Connect a 240 ohm resistor between Probe GND and Probe IN.
* **Or:** Cut the PCB trace on the BLTouch to reduce the output to 3.3V as described in the instructions for your BLTouch version. See https://www.antclabs.com/

All BLTouch V3.0 and later are 3.3V output by default, though can be set to 5V output.

The Z probe input on the Duet 2 WiFi/Ethernet rev 1.04 and later is 30V tolerant, so it doesn't matter whether you reduce the output of the BLTouch to 3.3V or leave it at 5V.

#### Duet 2 Maestro

Connect the BLTouch to the Z probe connector as follows:

| Duet Z Probe connector pin | BLTouch pin | Colour ||
|:--|:--|:--|:--|
| IN | Out | White | [![sensors_probe_bltouch_d2m.png](/manual/sensors/sensors_probe_bltouch_d2m.png =300x)](/manual/sensors/sensors_probe_bltouch_d2m.png){target=_blank} |
| GND | GND | Black + (Brown or Blue) | ^^ |
| MOD | Control | Orange or Yellow | ^^ |
| 3.3V | - | - | ^^ |
| +5V | +5V | Red | ^^ |

With older BLTouch (before V3.0), the output is 5V by default. The Z probe input on the Duet 2 Maestro is 30V tolerant, so it doesn't matter whether you reduce the output of the BLTouch to 3.3V or leave it at 5V.
  
### Software setup

### Tabs {.tabset}

#### RepRapFirmware 3

##### Software setup

1. Identify the name of the output pin that you connected the control input of the BLTouch to.

* On **Duet 3** it is the io[n].out pin, eg io7.out
* On a **Duet 2 WiFi/Ethernet** without an attached expansion board, it will be one of exp.heater3 thru exp.heater7
* If you have a **DueX** attached, one of duex.pwm1 thru duex.pwm5
* If you have a **Expansion Breakout Board**, it's !exp.heater6 or !exp.heater7 ('!' inverts the output, and is needed). 
* On a **Duet Maestro** it is zprobe.mod. 

If you use one of these pin names, you will not need to invert the output (except on BoB).

2. Create a GPIO/Servo pin index using your chosen control pin, for example:

```
M950 S0 C"io7.out" ; Duet 3 MB6HC
M950 S0 C"io3.out" ; Duet 3 Mini 5+
M950 S0 C"exp.heater3" ; Duet 2 WiFi/Ethernet
M950 S0 C"duex.pwm1" ; Duet 2 WiFi/Ethernet + DueX2/5
M950 S0 C"!exp.heater6" ; Duet 2 WiFi/Ethernet + BoB
M950 S0 C"zprobe.mod" ; Duet 2 Maestro
```

The above examples use GPIO/Servo index 0. If you have other GPIO/Servo pins already configured, change the index to an available number.

3. The Z probe input pin will be:

* io[n].in pin on **Duet 3** eg io7.in
* ^zprobe.in on a **Duet 2** 

You only need to enable the pullup resistor, using the ^ character in front of the pin name, on Duet 2 when using the zprobe.in. 
**Do not enable the pullup resistor if you are using a Duet 3 input, or a Duet 2 endstop input**.

4. In your M558 command, specify probe type 9 and the input pin, for example:

```
M558 P9 C"io7.in" H5 F120 T6000  ; Duet 3 MB6HC
M558 P9 C"io3.in" H5 F120 T6000 ; Duet 3 Mini 5+
M558 P9 C"^zprobe.in" H5 F120 T6000 ; Duet 2 WiFi/Ethernet, DueX2/5
M558 P9 C"^zprobe.in" H5 F120 T6000 ; Duet 2 Maestro
```

5. Set up the probe offsets, trigger height and trigger value in G31:

`G31 X20 Y0 Z3.3 P25`

In this example the trigger height is 3.3mm (you will need to measure it) and the probe is offset from the head reference point by 20mm in the +X direction.

6. Create a deployprobe.g file (in the /sys folder of the SD card) it should contain the following single command:

`M280 P0 S10`

* 'P0' is the GPIO/Servo index 0, defined by M950 S0 earlier. If you have changed the GPIO/servo index, change the P parameter in M280 to match.
* 'S10' is the signal sent to the BLTouch to deploy the probe.

7. Create a retractprobe.g file (in the /sys folder of the SD card) it should contain the following single command:

`M280 P0 S90`

* 'P0' is the GPIO/Servo index 0, defined by M950 S0 earlier. If you have changed the GPIO/servo index, change the P parameter in M280 to match.
* 'S90' is the signal sent to the BLTouch to retract the probe.

##### Commissioning & Testing

To test the setup with the nozzle maybe a 100mm up from the bed you can check if the trigger signal is reaching the Duet by attempting to extend the pin by using the following command:

`M280 P0 S10`

If all is well the web interface should report a value of ”0” or "N/A" in the Z probe column when the pin is extended. You can then lightly touch the extended probe pin to check that the value in the Z probe Column changes to “1000”

You can retract the pin again with

`M280 P0 S90`

the above two commands are also what should be in /sys/deployprobe.g and /sys/retractprobe.g, respectively. Test these are working correctly by sending M401 to deploy the probe, and M402 to retract the probe.

Before trying to home (and smashing your hot end into the bed repeatedly when it doesn't work), conduct the tests and calibrate the Z probe as described here; see [Test and calibrate the Z probe](/User_manual/Connecting_hardware/Z_probe_testing)

#### RepRapFirmware 2

##### Software setup (Duet 2 WiFi/Ethernet running RepRapFirmware 2)

**Important**: In the M280 commands in the following, leave out the I1 parameter if the BLTouch is connected to a servo output on a DueX board.

1. In your software config.g file input the following G-Code commands to allow the setup to work:

```
M307 H3 A-1 C-1 D-1
M558 P9 H5 F100 T2000
G31 X0 Y0 Z0 P25
```

* **Important**: mode 9 is supported in firmware 1.21 and later only. If you are using earlier firmware, replace P9 in the M558 command by P5.
* In the G31 command the X and Y are the offset of the sensor in relation to the nozzle (so input your own) the number for Z is the trigger height (read on for this). This is the height that is obtained from reading the height the nozzle is from the bed when the sensor pin contacts the bed and retracts, P is the signal threshold.
* Ensure in your config-override.g file (in the /sys folder of the SD card) does not contain an M307 H3 command that overrides the above configuration.
* In the M280 commands (see below), you must invert the servo signal using “I1” in the M280 commands if you connect the BLTouch servo input to the expansion header pins, but not if you connect the BLTouch servo input to a Duex.

2. Create a deployprobe.g file (in the /sys folder of the SD card) it should contain the following single command:

`M280 P3 S10 I1`

**See the important note earlier about including the I1 parameter or not.**

3. Create a retractprobe.g file (in the /sys folder of the SD card) it should contain the following single command:

`M280 P3 S90 I1`

##### Software setup (Duet 2 Maestro running RepRapFirmware 2)

As for the Duet 2 WiFi/Ethernet with the following changes:

* The M307 line is not required
* In all M280 commands, use P64 instead of P3, and leave out the I1. This applies to the following section too.

##### Commissioning & Testing (RepRapFirmware 2)

To test the setup with the nozzle maybe a 100mm up from the bed you can check if the trigger signal is reaching the Duet by attempting to extend the pin by using the following command:

`M280 P3 S10 I1`

Then put the Sensor in to test mode by inputting the following command:

`M280 P3 S120 I1`

If all is well the web interface should report a value of ”0” in the Z probe column when the pin is extended. You can then lightly touch the extended probe pin to check that the pin retracts, the value in the Z probe Column should change to “1000”

The BL Touch will continue to cycle deploy in the test mode, to end the test mode enter:

`M280 P3 S160 I1`

Test deployprobe.g and retractprobe.g  are working correctly by sending M401 to deploy the probe, and M402 to retract the probe.

Before trying to home (and smashing your hot end into the bed repeatedly when it doesn't work), conduct the tests and calibrate the Z probe as described here; see [Test and calibrate the Z probe](/User_manual/Connecting_hardware/Z_probe_testing)

## Touch-Mi

The Touch-Mi  is based on an IR end stop with a metal pin for probing in the z direction. While the probe is documented to run at 5v, it can also be run at 3.3v (this has been confirmed with hotends.fr, the manufacturer of the Touch MI). As such, the Touch MI can be connected to the z probe headers of the duet board as shown below.

[![sensors_probe_touchmi.jpg](/manual/sensors/sensors_probe_touchmi.jpg =400x)](/manual/sensors/sensors_probe_touchmi.jpg){target=_blank}

Alternately, you can connect the Touch-Mi to a 5V pin (from the PanelDue header or from the expansion header) for VCC and to the E0 endstop for GND and IN (use the outer pins of the E0 endstop header).

### Configuration

The Touch-Mi probe is active low. If connected to the z probe port, the M558 configuration should be like this:

`M558 P8 F500 H10 I0 T4000`

`H` should be at least 3mm, but during the first runs with a new probe (or after changing out your bed), it is advisable to increase H to a safe diving height. The example sets diving height  at a comfortable triple minimum distance, and you can reduce H if you are sure the probing pin won't catch after deploying and/or drag across the bed surface during moves.

Also, set trigger value, nozzle offset, trigger offset using [G31](/User_manual/Reference/Gcodes/G31):

``G31 P500 Xnnn Ynnn Znnn``

See the calibration section for a method to determine z offset.

The Touch-Mi is deployment is triggered via a magnet. As this magnet is usually installed so it triggers deployment by hitting X0 or X(max), the probing should be at a distance to X0/X(max). As such, it is recommended your mesh definition happens a bit inside the outer edges of your bed parameters. For a 220x220 size bed,

[M557](/User_manual/Reference/Gcodes/M557) could look like this for a 6x6 grid:

``M557 X10:210 Y10:210 S40``

M557, M558 and G31 P... as described here should be added to *config.g*.

### Touch-Mi macros

*deployprobe.g:*

```
; deployprobe.g
; this is a manual probe deployment by touching the Touch-Mi Probe
; with the magnet  included. 

G91                       ; relative positioning
G1 Z7 F600 S2             ; lift Z relative to current position
G90                       ; absolute positioning
; G1 X0 F1800               ; move X to zero
M291 P"Trigger Magnet" S3 ; prompt for magnet action
```

*retractprobe.g*

```
; retractprobe.g
G0 Z0.5             ; dive to retract probe with magnetic ball bearing
```

*homez.g*

```
; homez.g
; called to home the Z axis

G91                ; relative positioning
G90                ; absoute position
M401               ; deploy probe
G1 X163 Y129 F1800 ; move to middle of bed plus probe offsets
G30                ; home z0
M402               ; retract probe
```

*homeall.z*

```
; homeall.g
; called to home all axes

G91                      ; relative positioning
G1 H1 X-225 Y-225 F1800  ; move quickly to X and Y axis endstops and stop there (first pass)
G1 X5 Y5 F6000           ; go back a few mm
G1 H1 X-225 Y-225 F360   ; move slowly to X and Y axis endstops once more (second pass)
G90                      ; absoute position
M401                     ; deploy probe
G1 X163 Y129 F1800       ; move to middle of bed plus probe offsets 
G30                      ; home z0
M402                     ; retract probe
```

### Calibrating the Touch-Mi

1. Calibrate nozzle to zero manually (paper test).

2. Set Z to zero (``G92 Z0``)

3. Lift nozzle by 5 to 10 mm (``G0 Z10``)

4. Determine probe trigger height (``G30 S-1``) and note the result shown on the G-Code console.

5. Repeat steps 3 and 4 a couple times to ensure the result of ``G30 S-1`` has acceptable repeatability (variance smaller or equal to 0.03mm). Note the average of these repeated measurements.

6. Set the trigger height in [G31](/User_manual/Reference/Gcodes/G31) in your config.g

## TH3D EZABL

See the connection and configuration guide on the [TH3D site](https://support.th3dstudio.com/helpcenter/duet-board-setup-information/).