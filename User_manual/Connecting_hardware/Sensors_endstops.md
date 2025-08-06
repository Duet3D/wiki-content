---
title: Connecting endstop switches
description: 
published: true
date: 2025-08-06T09:13:23.240Z
tags: 
editor: markdown
dateCreated: 2021-10-20T14:14:43.144Z
---

# Introduction

Duet mainboard and expansion boards support a variety of endstop sensors. This page covers connecting and configuring mechanical switches (eg microswitches), Hall sensors and optical endstops.

# Duet endstop inputs

Connect your endstop to the Duet, following these board-specific notes:

# Tabs {.tabset}

## Duet 3 endstop inputs

Endstop devices can be connected to the IO_0 through IO_9 connectors. Each endstop input has a 27K pullup resistor, so the endstop devices only need to sink 0.12mA.

**Maximum input voltage on IN pin:** 30V

## Duet WiFi/Ethernet endstop inputs

Each endstop input has ground, +3.3V and sense pins in that order, labelled GND, 3V3 and STP respectively on the back of the board.

Each input has a pullup resistor and red LED between the STP pin and +3.3V. The LED will be illuminated when an endstop switch or other device connected to the input holds the voltage close to 0V (ground). **Whether the LED is illuminated when the endstop switch is triggered or not triggered depends on whether your endstop switch produces and active-high or active-low output:**

* An endstop switch with an active-high output holds the input pin at about 0V when the switch is not triggered, and about +3.3V when it is triggered. A typical example is a normally-closed microswitch. The LED on the Duet will be illuminated when the switch is connected but not triggered.
* An endstop switch with an active-low output holds the input pin at about 3.3V when the switch is not triggered, and about 0V when it is triggered. One example is a normally-open microswitch (which is not recommended, because a normally-closed switch is safer). Another example is a Hall sensor with an open-drain output that is connected directly to the Duet endstop input. The LED on the Duet will be illuminated when the switch is connected and triggered.

The endstop switches each need to be able to sink 1.5mA current for the Duet 2 WiFi and Duet 2 Ethernet. This is not usually a problem, except with some optical endstop switches that were not designed properly for 3.3V operation.

**Note:** some types of endstop (e.g. opto endstops designed for 5V operation) may pull the endstop input low enough to light the LED, but not low enough for the Duet to recognise that the input is in the LOW state.

### Maximum input voltage on STP pin

Duet 2 WiFi/Ethernet hardware rev 1.04 and later: 8.3V. Higher voltages may damage the endstop LED.

Duet 2 WiFi/Ethernet hardware rev 1.03 and earlier: 3.3V (in practice a little more is unlikely to damage the Duet)

### Endstop Mapping in RRF 2.x

Duet 2 series has endstop labels with axis names. The following table shows a conversion between axis names and numbers of all available endstop inputs. In RRF 2.x the endstop mapping is fixed; endstops cannot be reassigned. If you reassign the X axis to a different stepper driver with M584, x_stop will still be the X endstop connection.

| Number | Name | Comment |
|:---|:---|
| 0 | X |  |
| 1 | Y |  |
| 2 | Z |  |
| 3 | E0 |  |
| 4 | E1 |  |
| 5 | E2 (E2_STOP) | Expansion Header/DueX2/5 |
| 6 | E3 (E3_STOP) | Expansion Header/DueX2/5 |
| 7 | E4 (E4_STOP) | Expansion Header/DueX5 |
| 8 | E5 (E5_STOP) | Expansion Header/DueX5 |
| 9 | E6 (E6_STOP) | Expansion Header/DueX5 |
| 10 | Stop 10 (ENC_B) | CONN_LCD Header |
| 11 | Stop 11 (ENC_A) | CONN_LCD Header |


## Duet Maestro endstop inputs

Each endstop input has ground, +3.3V and sense pins in that order, labelled GND, 3V3 and STP respectively on the back of the board (same pinout as for the Duets covered in the previous section).

There is no LED provided, just a 10K pullup resistor. So the endstop devices only need to sink 0.3mA.

**Maximum input voltage on STP pin:** 30V

### Endstop Mapping in RRF 2.x

Duet 2 series has endstop labels with axis names. The following table shows a conversion between axis names and numbers of all available endstop inputs. In RRF 2.x the endstop mapping is fixed; endstops cannot be reassigned. If you reassign the X axis to a different stepper driver with M584, x_stop will still be the X endstop connection.

| Number | Name |
|:---|:---|
| 0 | X |
| 1 | Y |
| 2 | Z |
| 3 | E0 |
| 4 | E1 |

# Connecting different types of endstop switch

In the following descriptions, STP/IN refers to the IN pin of the connector if you are using a Duet 3, otherwise to the STP pin of the connector.

## Microswitch

This applies to a bare microswitch, not to a microswitch on a board with a LED.

* Duet 3: connect the switch between the IN and GND pins of your chosen IO_x connector.
* Duet WiFi, Duet Ethernet and Duet Maestro: connect the switch between GND and STP/IN. These are the outer 2 pins of the 3-pin connector. **Note**: this is **not** the same as on RAMPS.

We recommend you use the normally-closed contacts of the microswitches, which are generally the outside two connections on the microswitch.

RepRapFirmware 3.x: Set the endstop type to switch (S1) in the M574 command. If you use normally-open microswitch contacts, invert the polarity by putting a ! character at the start of the pin name in the M574 command.

RepRapFirmware 2.x and earlier: Set the signal polarity to active high (S1) in the M574 command. If you use normally-open microswitch contacts, then you will need to set the signal polarity to active low (S0) in the M574 command.

### Interference

Wires connecting normally-open endstop switches are susceptible to capacitive interference pickup, in particular from nearby stepper motor cables but to a lesser extent from heater and fan cables. You can mitigate this to some extent by using lower value pullup resistors. A better solution is to change to normally-closed endstop switches if possible, or to use shielded cable to connect the normally-open endstop switch.

## Makerbot Mechanical Endstop v1.2

Connect it to the Duet endstop connector as follows. **Note**: the pins on the Duet endstop connector are not in the same order as on RAMPS!

| Makerbot endstop pin number | Function | Duet 3 IO_x pin marking | Duet 2 endstop pin marking |
|:---|:---|
| 1 | VCC | 3V3 | 3V3 |
| 2 or 3 | GND | GND | GND |
| 4 | Output | IO_x_IN | STP |

Unfortunately the pin markings on the Makerbot endstop board are hidden underneath the connector. Pin 1 is next to the long edge of the board that does not have the microswitch on it, and pin 4 is nearest the edge with the microswitch.

These devices produce an active-low output. If using RepRapFirmware 3.x, invert the polarity by putting a ! character in front of the pin name. If using RepRapFirmware 1.x or 2.x, use the S0 parameter in your M574 command. 

## 3.3V-compatible Hall sensor

Connect Gnd to Gnd, Vcc of the Hall sensor to 3V3, and the output of the Hall sensor to STP/IN. Simple Hall sensors normally have active low outputs, so put ! at the start of the pin name if using RRF 3.x, or use S0 in the M574 command if using RRF 1.x or 2.x. 

If your Hall sensor is a circuit board with a sensitivity adjustment potentiometer on it, then it may provide an active high output instead of active low. If your hall switch is just the IC then you should also connect a 0.1uF capacitor between Vcc and Gnd close to the IC.

## 5V Hall sensor with open-collector or open-drain output

Examples: Allegro A3141, A3142, A3143, A3144 (all of these are discontinued products, but still found on eBay)

Duet 3: as for 3.3v Hall sensor, but connect Vcc to the 5V pin of the IO_connector instead of the 3.3V pin.

Other Duets: Preferably, use 3.3V-compatible Hall sensors instead. However, If your 5V Hall sensor has an open-collector or open-drain output (as the devices listed above do), or if you are using a Duet WiFi/Ethernet hardware rev 1.04 or later or a Duet Maestro, you can wire it as for a 3.3V sensor except that Vcc must be connected to +5V (available at pin 1 of the expansion connector) instead of to the 3v3 endstop connector pin.

## 3.3V-compatible optical endstop

Connect Gnd to Gnd, Vcc of the opto sensor to 3V3, and the output of the opto sensor to STP/IN. Opto sensors usually have active high outputs, so in RRF 3.0 and later set the endstop type to switch (S1) in the M574 command. In RRF 2.x and earlier, also use S1 in the M574 command, though this sets the endstop as have active high input.

Note: opto endstops made to the Generation 7 design are often claimed to be 3.3V-compatible, but in fact the design is marginal with a 5V supply and frequently doesn't work at all on 3.3V. Here are some workarounds:

* This design normally works with a 3.3V supply if you replace the 180 ohm opto switch series resistor resistor by 100 ohms. Tip: if your opto endswitch uses surface mount resistors, instead of removing the 180 ohm resistor it is easier to solder a 200 or 220 ohm resistor on top of it, so that the two resistors are connected in parallel. Make sure you pick the correct resistor to replace or solder another on top of! There is often also a load resistor, value 1K or higher.
* If you are using Duet 3 then you can use the 5V pin on the IO_ connector to provide power to the opto endstop instead of using 3.3V power.
* If you are using a Duet WiFi/Ethernet hardware rev 1.04 or later or a Duet Maestro, these boards can tolerate 5V on the endstop inputs. So you can provide them with a 5V supply instead (leaving the centre pin of the endstop connector not connected) and connect the outputs of your endstops directly to the STP/IN pins of the endstop connectors.

## Inductive or capacitive sensor with NPN output

See [NPN output normally-open inductive or capacitive sensor](/User_manual/Connecting_hardware/Z_probe_connecting#npn-output-normally-open-inductive-or-capacitive-sensor). Those instructions are for connecting a sensor used as a Z probe, but similar connections can be used for endstops. If connecting to a Duet 2 endstop input, connecting via a diode is reocmmended regardless of the board version.

## Inductive or capacitive sensor with PNP output

See [PNP output normally-open inductive or capacitive sensor](/User_manual/Connecting_hardware/Z_probe_connecting#pnp-output-normally-open-inductive-or-capacitive-sensor).

# Multiple endstops on one axis

RepRapFirmware only supports one endstop per motor per axis at a time. If your axis only has one motor, you can only have one endstop. If your axis has two or more motors, you can have an endstop per motor, if the motors are plugged into their own stepper driver, and the motors can be homed individually to their own endstop. To do this, see the 'Axis levelling using endstops' section of [Bed levelling using multiple independent Z motors](/User_manual/Connecting_hardware/Z_probe_auto_levelling#axis-levelling-using-endstops).

# Firmware configuration

Each axis endstop input can be configured in firmware for a Max (high end of axis) or Min (low end of axis) endstop, and with an active high or active low signal level. Configuration is done in config.g using the [M574](/User_manual/Reference/Gcodes/M574) command.

On most types of printers except delta, you do not need to have a Z endstop switch if you use a Z probe for Z homing.

## Tabs {.tabset}

### RepRapFirmware 3.x

Example of a simple endstop configuration, with X and Y microswitch endstops on the low end (X1 and Y1), using switch-type endstops (S1) on appropriate pins. Z also has the endstop at the low end (Z1), but has the Z probe configured as the endstop:

Duet 3:
```
; Endstops
M574 X1 S1 P"io1.in" ; configure switch-type (e.g. microswitch) endstop for low end on X via pin io1.in
M574 Y1 S1 P"io2.in" ; configure switch-type (e.g. microswitch) endstop for low end on Y via pin io2.in
M574 Z1 S2           ; configure Z-probe endstop for low end on Z
```

Duet 2:
```
; Endstops
M574 X1 S1 P"xstop" ; configure switch-type (e.g. microswitch) endstop for low end on X via pin xstop
M574 Y1 S1 P"ystop" ; configure switch-type (e.g. microswitch) endstop for low end on Y via pin ystop
M574 Z1 S2          ; configure Z-probe endstop for low end on Z
```

### RepRapFirmware 2.x

Example of a simple endstop configuration, with X and Y microswitch endstops on the low end (X1 and Y1), using active-high endstops (S1) on appropriate pins. Z also has the endstop at the low end (Z1), but has the Z probe configured as the endstop:

```
; Endstops
M574 X1 Y1 S1 ; set active high endstops
M574 Z1 S2    ; set endstops controlled by probe
```

## Endstop switch configuration by printer type

For more detail on specific types of printers see:

* [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian)
* [Configuring RepRapFirmware for a Linear Delta printer](/User_manual/Machine_configuration/Configuration_linear_delta)
* [Configuring RepRapFirmware for a  CoreXY printer](/User_manual/Machine_configuration/Configuration_coreXY)

* [Configuring RepRapFirmware for an IDEX printer](/User_manual/Machine_configuration/Configuration_IDEX)
* [Configuring RepRapFirmware for a CNC machine](/User_manual/Machine_configuration/Configuration_CNC)
* [Configuring RepRapFirmware for a laser cutter or engraver](/User_manual/Machine_configuration/Configuration_laser)
<!--* [Configuring RepRapFirmware for a SCARA printer **UPDATE LINK**]()
* [Configuring RepRapFirmware for a Polar printer **UPDATE LINK**]()
* [Configuring RepRapFirmware for a Hangprinter printer **UPDATE LINK**]()-->

# Test endstop switches

Apply power to the printer. You need only 5V power for this test, so we suggest you leave the main power turned off and just connect the printer to a PC via the USB cable. Note: if you have a PanelDue with 7" screen then USB power may not be sufficient.

Note: the following does not work if you are using stall detection instead of wired endstops.

## Tabs {.tabset}

### Testing endstop switches using the web interface

#### DWC Status panel
[![commissioning_08_endstops_01.png](/guides/commissioning/commissioning_08_endstops_01.png =50%x){.align-right}](/guides/commissioning/commissioning_08_endstops_01.png){target=_blank}In DWC v3.5 and later, the endstop status is indicated in the Status panel. If the endstop is triggered, a green square will highlight the axis that is triggered. If there is no green square, it is not triggered.
* Press and hold each endstop switch, and check the axis label changes colour.

#### DWC Old versions

To check the endstop status in older versions of Duet Web Console/RepRapFirmware, if you are using:
* Duet Web Control v3.5 or later, see above, 'M119' or 'Object model browser' tabs
* Duet Web Control v3.3 or v3.4, see 'Endstop plugin', 'M119' or 'Object model browser' tabs
* Duet Web Control v3.0 to 3.2, see 'M119' or 'Object model browser' tabs

Early versions of DWC (v1.x, v2.x) displayed the endstop status in DWC. You can also check the endstop status with M119.
* If you are using Duet Web Control v2.x, select the **Machine Specific** page. 
* For Duet Web Control v1.22.6 or earlier, select the **Settings** page, then the **Machine Properties** tab.

Check by:
* In the **Endstops** or **Endstop Hit** column you can see the state of each endstop.
* Test that the Endstop Hit value displayed is **No** when the corresponding axis is not pushed against the endstop, and **Yes** when it is


<p style="clear:both"></p>

### M119

[![wiring_d2we_06_test_endstop_01.png](/guides/wiring/wiring_d2we_06_test_endstop_01.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_01.png){target=_blank}Send [M119](/User_manual/Reference/Gcodes/M119) to check endstop status in all versions of DWC/RRF. This can be sent from DWC, a PanelDue, or over USB, if connected by serial terminal.
* In DWC, go to Control > Console and type in `M119` in the text box, then press return or the 'Send' button. You should get the endstop status response in the area below.
* If connected to the Duet by a serial terminial over USB, or PanelDue, type `M119` and press return; the Duet will respond with the endstop status.
* Press and hold an endstop switch, and sent the command again, and you should see the status response of that switch change.

<p style="clear:both"></p>

### Object model browser

[![wiring_d2we_06_test_endstop_02.png](/guides/wiring/wiring_d2we_06_test_endstop_02.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_02.png){target=_blank}[![wiring_d2we_06_test_endstop_03.png](/guides/wiring/wiring_d2we_06_test_endstop_03.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_03.png){target=_blank}You can check the endstops status in the DWC Object model browser in RRF/DWC v3.0 and later. The RepRapFirmware Object model shows all the firmware variables and values.
* Enable the Object model browser by going to 'Settings > Plugins > Integrated plugins' ('Settings > General > Built-in Plugins' in older versions of DWC) and click 'Start' on the 'Object Model Browser'.
* A new menu option 'Object Model' will appear; select it.
* Navigate to 'sensors > endstops'. Expand the numbered sections. Trigger an endstop, and it will show as 'triggered = true' if correctly configured.

<p style="clear:both"></p>

### Endstop plugin

[![wiring_d2we_06_test_endstop_04.png](/guides/wiring/wiring_d2we_06_test_endstop_04.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_04.png){target=_blank}[![wiring_d2we_06_test_endstop_05.png](/guides/wiring/wiring_d2we_06_test_endstop_05.png =50%x){.align-right}](/guides/wiring/wiring_d2we_06_test_endstop_05.png){target=_blank}In DWC/RRF 3.3 and 3.4, you can install a plugin to show endstop status.
* Go to [https://github.com/Duet3D/DSF-Plugins/releases/](https://github.com/Duet3D/DSF-Plugins/releases/){target=_blank}
* Download the "EndstopsMonitor-xxx.zip", where "xxx" is the version number, and matches the version of DWC you are running (check on the 'Setting > General' page).
* Go to 'Settings > Plugins > External plugins' ('Settings > Machine-specific > Machine-specific plugins' in older versions of DWC) and click 'Install plugin'.
* Navigate to the "EndstopsMonitor-X.X.zip" you downloaded, select and click 'Open'.
* Click through the next few windows, reading the information and warnings.
* Once installed, click on 'Start'. 
* You can now see the endstop status in 'Settings > Machine-specific > Endstops'.

<p style="clear:both"></p>

# Troubleshooting

* If the Duet fails to boot up with the endstops switches connectors, or if it disconnects from the browser as soon as you trigger an endstop switch, this usually means that you are using microswitch endstops and you have connected to the wrong pins of the endstop connector. 2-wire microswitches must be connected to the outer two pins of the connector, which is not the same as many other 3D printer control boards.
* If the endstop switches read in reverse, then the remedy depends on the type of endstop sensor:
  * If the sensor is a 2-wire microswitch, we recommend that you use the normally-closed contacts, which are the two outermost tags on the switch (leave the centre tag not connected).
  * If you are already using those contacts, or you are using a different type of endstop sensor, in the M574 command in config.g change S0 to S1 or vice versa.
* If the Endstop hit value doesn't change when you trigger the endstop:
  * Check the wiring
  * If they are optical endstops, first make sure that you have S1 in the M574 command. Then, if the endstops show as triggered all the time, your endstops are probably not 3.3V compatible and you need to modify them - see the *3.3V compatible optical endstop* section above. If they never show as triggered, check that the flag on the end of the axis blocks the slot completely, and that there is a pullup resistor between the sensor output and +3.3V (10K is usually suitable).


