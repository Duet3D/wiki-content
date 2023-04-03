---
title: Connecting and configuring extruder heaters
description: 
published: true
date: 2022-02-11T14:48:26.636Z
tags: 
editor: markdown
dateCreated: 2021-09-20T14:22:52.182Z
---

# Choosing an extruder heater

The extruder heater outputs on Duet boards should be capable of supporting most standard 3D heater cartridges, or old-style heating resistors. These usually come in capacities specified in Watts, and for either 12V or 24V supply. From this you can work out the resistance and current draw of the heater. 

Some high capacity heaters, such as the E3D 12V SuperVolcano 80W Heater, can draw up to 8A, and are usually supplied with an external MOSFET board to provide power. Consult manufacturer's documentation for wiring. 

## Example heater cartridge ratings

| Watts | Volts | Resistance (Ohms) | Current (Amps) |
|---|---|
| 30W | 24V | 19.2 | 1.25A |
| 40W | 24V | 14.4 | 1.66A |
| 80W | 24V | 7.2 | 3.33A
| 30W | 12V | 4.8 | 2.5A |
| 40W | 12V | 3.6 | 3.33A |
| 80W | 12V | 1.8 | 6.66A |

> **WARNING! DO NOT** run a 12V heater on 24V! 
{.is-danger}

As you can see from the above table, the 30W 12V heater has a resistance of 4.8 ohms. If you run this on 24V, the current draw is 24V / 4.8 ohms = 5A and power is 24V x 5A = 120W! Even running with a 25% PWM duty cycle, this means the heater will be on full power 25% of the time. PWM does not modify the voltage, it modifies the amount of time the output is turned on. Use a heater with a resistance that suits the supply voltage.

## Heater wiring

Most cartridge heaters come with long leads to connect to your controller, which should be suitable. If you need to lengthen wiring, make sure your heater wiring is capable of handling the current the heater will pull. Here are some things to consider:
- Wiring that moves (ie from the frame to the X carriage) should not be single core/strand; it will fatigue and break quickly.
- Multi-strand wire can carry less current than single strand of the same size/gauge.
- Long wire runs using thin wire will increase resistance, so the voltage to the heater will drop, and the wire may heat up.

As a general rule of thumb, 20AWG / 0.5mm^2^ multistrand wire can carry 3.5A. 18AWG / 0.8mm^2^ multistrand wire can carry 5A. But please check the specification of the wire you are using.

# Connecting extruder heaters

The extruder heater connectors vary with the Duet version.

| Board | 'Extruder heater' outputs | Connector type | RRF3 pin name | RRF2 heater number
|:---|:---|
| Duet 3 Mainboard 6HC | 3 x 6A | JST VH | out1, out2, out3 | - |
| Duet 3 Mini 5+ | 2 x 5A | JST VH |out1, out2 | - |
| Duet 3 Expansion 3HC | 3 x 6A | JST VH |out0, out1, out2 | - |
| Duet 3 Toolboard 1LC | 1 x 5A | screw terminal | out0 | - |
| Duet 2 WiFi/Ethernet | 2 x 6A | screw terminal | e0heat, e1heat | 1, 2 |
| DueX2 and DueX5 | 2 (DueX2) or 5 (DueX5) x 6A | screw terminal |duex.e2heat to duex.e6heat | 3 to 7 |
| Duet 2 Maestro | 2 x 6A | screw terminal | e0heat, e1heat | 1, 2 |

## Wiring extruder heaters

- Make sure heater wires do not short out anywhere; to other wiring, or the heater block. A short may cause uncontrolled heating, or for VIN voltage to be feed into the low voltage rails (3.3V, 5V) of the Duet, causing a failure of the Duet.
- Check your wires are sufficient to handle the heater current
- Polarity does not matter for hotend heaters

## For crimped connections 

**Duet 3 Mainboard 6HC**, **Duet 3 Mini 5+** and **Duet 3 Expansion 3HC** use JST VH series connectors for connecting the extruder heater wires. You will need to crimp the supplied pins on the end of the heater wires, and push them into the supplied connector housing.

- You will need a suitable crimping tool for the crimp pins, for example Engineer PA21 (use the 2.2mm jaw opening to crimp the bare wire and the 2.5mm on to crimp the insulation).
- Alternatively you can solder the wire to the crimp pin
- The JST VH series of connectors require a minimum of 22AWG wire (20AWG / 0.5mm^2^ or 18AWG / 0.8mm^2^ recommended). If your wires are not thick enough, you can double the stripped part of the wire back on itself to bulk it up, and put a small length of heatshrink sleeving over the insulation to bulk up the insulation.

## For screw terminal connections

**Duet 3 Toolboard 1LC**, **Duet 2 Wifi/Ethernet** and **Duet 2 Maestro** use screw terminals for connecting the extruder heater wires.

- It is HIGHLY recommended to use the included ferrules, by crimping them to the wires before putting the wires in the terminal block. Failure to do so could allow the wires to creep over time, become loose, and could possibly short circuit and start a fire.
- Do not tin (add solder to) these wires.
- Be sure not to twist the terminal block while tightening the screws. It can help to hold the terminal block while tightening.
- Check the screws after a few days/week of operation to ensure they are still snug.

# Configuring firmware

# Tabs {.tabset}

## RepRapFirmware 3.x

In RRF 3.x, no heaters or temperature sensors are defined by default. 
- [M308](/User_manual/Reference/Gcodes/M308) defines and configures the temperature sensor. 
- [M950](/User_manual/Reference/Gcodes/M950) defines and confiures the extruder heater and associates it with the temperature sensor.
- [M307](/User_manual/Reference/Gcodes/M307) sets the heating process parameters.
- [M143](/User_manual/Reference/Gcodes/M143) sets a maximum heater temperature. 
- Finally, create a tool with [M563](/User_manual/Reference/Gcodes/M563) using the heater (including associated temperature sensor), motor, and fan.

### Tabs {.tabset}

#### Duet 3 example

```
; Heaters
M308 S1 P"temp1" Y"thermistor" T100000 B4725 C7.06e-8 ; configure sensor 1 as thermistor on pin temp1
M950 H1 C"out1" T1                                    ; create nozzle heater output on out1 and map it to sensor 1
M307 H1 B0 S1.00                                      ; disable bang-bang mode for heater  and set PWM limit
M143 H1 S280                                          ; set temperature limit for heater 1 to 280C

; Tools
M563 P0 D0 H1 F0                                      ; define tool 0
G10 P0 X0 Y0 Z0                                       ; set tool 0 axis offsets
G10 P0 R0 S0                                          ; set initial tool 0 active and standby temperatures to 0C 
```

#### Duet 2 example

```
; Duet 2
; Heaters
M308 S1 P"e0temp" Y"thermistor" T100000 B4725 C7.06e-8          ; configure sensor 1 as thermistor on pin e0temp
M950 H1 C"e0heat" T1                                            ; create nozzle heater output on e0heat and map it to sensor 1
M307 H1 B0 S1.00                                                ; disable bang-bang mode for heater  and set PWM limit
M143 H1 S280                                                    ; set temperature limit for heater 1 to 280C

; Tools
M563 P0 D0 H1 F0                                                ; define tool 0
G10 P0 X0 Y0 Z0                                                 ; set tool 0 axis offsets
G10 P0 R0 S0                                                    ; set initial tool 0 active and standby temperatures to 0C
```

## RepRapFirmware 2.x

In RRF 2.x, heater outputs are defined by default. Use [M305](/User_manual/Reference/Gcodes/M305) to configure the temperature sensor for the heater channel. Use [M143](/User_manual/Reference/Gcodes/M143) to set a maximum heater temperature. Finally, create a tool with [M563](/User_manual/Reference/Gcodes/M563) using the heater (including associated temperature sensor), motor, and fan.

```
; Heaters
M305 P1 T100000 B4725 C7.060000e-8 R4700 ; set thermistor + ADC parameters for heater 1
M143 H1 S280                             ; set temperature limit for heater 1 to 280C

; Tools
M563 P0 D0 H1 F0                         ; define tool 0
G10 P0 X0 Y0 Z0                          ; set tool 0 axis offsets
G10 P0 R0 S0                             ; set initial tool 0 active and standby temperatures to 0C
```

# Tuning heater control

[M307](/User_manual/Reference/Gcodes/M307) sets the heating process parameters. Tuning this with [M303](/User_manual/Reference/Gcodes/M303) should allow for finer control of the heater; more stable temperatures during printing, and less overshoot when setting temperatures. Each heating controller also performs temperature monitoring to try to detect fault conditions such as a heater of thermistor falling out of an extruder heating block. The temperature monitor relies on the model parameters to decide what is reasonable behaviour. See the wiki page on [Tuning the heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning).