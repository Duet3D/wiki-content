---
title: Connecting hobby servos and DC motors
description: This page deals with connecting hobby servos and DC motors to Duet mainboards.
published: true
date: 2024-04-16T12:55:24.344Z
tags: 
editor: markdown
dateCreated: 2021-10-12T16:04:36.467Z
---

![motor_servo_01.jpg](/manual/motors/motor_servo_01.jpg =500x)

*Image from [wikipedia](https://upload.wikimedia.org/wikipedia/en/2/2a/Hobby_servo_motors.jpg)*

# Introduction

This page deals with connecting hobby servos, DC motors and spindles to Duet mainboards.

RepRapFirmware allows you to connect other devices to unused outputs. Expansion boards also provide additional inputs and outputs. The following Gcodes allow re-purposing of some pins, and direct control of those pins for peripherals such as PWM hobby servos, DC motors, pumps, spindles and General Purpose Inputs/Outputs (GPIO). 
* [M950](/User_manual/Reference/Gcodes/M950) - Defines heater, fan, spindle, GPIO or servo pin (RRF 3.x)
* [M42](/User_manual/Reference/Gcodes/M42) - Defines (RRF 2.x) and controls I/O pin 
* [M280](/User_manual/Reference/Gcodes/M280) - Sets servo position

There are a number of options for extra control: 
* PWM pins that provide a 3.3V or 5V signal to control a connected peripheral, can be used for hobby servos or to control a Variable Frequency Drive (VFD) that controls a CNC spindle.
* PWM pins that control the voltage through a peripheral, such as those for heaters and fans which are switched on the GND side, can be used to control DC motors and pumps.
* Digital pins (on/off) that can be used to control switches and relays, or read inputs from external switches and triggers. For this, see [Controlling unused IO pins](/User_manual/Connecting_hardware/IO_GPIO) 

### Number of defined ports supported (as of RRF 3.4)

Within the firmware, there are limits to the number of spindles, servos and GPIO ports that can be configured. Servos and DC motors are counted as General Purpose (GP) Outputs. Spindles are separate. 

| | Spindles | GP Outputs | GP Inputs |
|---|---|
| Duet 3 Mainboard 6HC | 4 | 32 | 16 |
| Duet 3 Mini 5+ | 4 | 32 | 16 |
| Duet 2 WiFi/Ethernet | 4 | 20 | 20 |
| Duet 2 Maestro | 2 | 10 | 10 |

# Connecting servo motors

Connect servo +5V and GND either to the Duet, or power the servo from a separate +5 supply. If you need to add the 220uf capacitor (see *Recommendations*, below), you can:
* crimp both the servo wire and the capacitor leads into the terminal and add some sleeving on the exposed part of the leads; or
* solder capacitor to the bottom of the board, onto the solder joints of the servo header; or
* solder capacitor to PCB inside servo, 3d print new cover.

Connect the PWM wire to an appropriate PWM pin on the Duet. Most 5V servos seem to work fine with the 3.3V signalling most Duet I/O pins supply.
* **Duet 3 Mainboard 6HC:** There is a dedicated 5V PWM header, which provides 5V signalling, shared with OUT_9. Otherwise use any PWM-capable IO[n].out pin for 3.3V signalling. See [Duet 3 MB6HC Hardware Overview](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6HC_Hardware_Overview#inputoutput). Headers on CAN-connected expansion boards can also be used.
* **Duet 3 Mini 5+:** There is a dedicated 5V PWM header, which provides 5V signalling, shared with OUT_6. Otherwise use any PWM-capable IO[n].out pin for 3.3V signalling. See [Duet 3 Mini 5+ Hardware Overview](/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Hardware_Overview#io-port-pin-capabilities).  Headers on CAN-connected expansion boards can also be used.
* **Duet 2 WiFi/ Ethernet:** DueX2/5 and EBoB have dedicated 5V PWM headers, which provide 5V signalling. Otherwise, use spare heater pins on the expansion connector pin for 3.3V signalling. See [Duet 2 WiFi and Ethernet Hardware Overview](/Duet3D_hardware/Duet_2_family/Duet_2_WiFi_Ethernet_Hardware_Overview#inputoutput)
* **Duet 2 Maestro:** Z_PROBE_MOD and SERVO pins the expansion connector are shared, providing one PWM pin for 3.3V signalling. See [Duet 2 Maestro Hardware Overview](/Duet3D_hardware/Duet_2_family/Duet_2_Maestro#inputoutput)

It is possible to use an OUT (Duet 3) or heater/fan (Duet 2, on-board headers) port instead. As this is a PWM-to-GND pin, you would need to use a pullup resistor to the servo power voltage (typically 5V) and specify inverted output in the M950 command.

## Important considerations when connecting servos!

> **CAUTION!**
>
> 'Hobby' servos can be connected and controlled by the Duet range of boards. However, there are limitations on the size of servo that can be powered directly, due to the current draw of the servo (particularly when it stalls, i.e. 'stall current'), and the current the servo can generate when it stops or is forced to turn ('regenerative braking'). Both of these can have a damaging effect on the Duet and/or connected peripherals, eg PanelDue.
{.is-warning}

## Stall current

Stall current is the maximum current drawn when the motor is applying its maximum torque, either because it is being prevented from moving entirely or because it can no longer accelerate given the load it is under. This is 'under normal conditions', i.e. the motor isn't being asked go from max speed in one direction to max speed in the other, which can generate even higher currents.

Your servo's stall current should not exceed the Duet's available current (see *Duet board 5V current limit* below), and it's advisable to have some spare current capacity, so don't specify a servo that uses up all the remaining current. The effect of a servo drawing too much current will be to drop the voltage on the 5V rail, most likely leading to a Duet board reset, but may damage the 5V regulator.

Servos that are commanded beyond their movement limits usually stall. This heats up both the servo and the Duet. The servo is likely to burn out if held in this state. So:

* Ensure that the Duet can supply the stall current. If not, use an external power supply.
* Be very careful not to command the servo beyond its working range; often small, cheap servos have a working range of 10° to 170°, so sending `M280 P# S0` or `M280 P# S180` will result in a stall. 
* If a stall can't be avoided, command the servo to move, hold it for a short time, then use `M42 P# S0` to stop commanding the servo (replace # by the pin number or GpOut number as usual). This effectively removes power from the servo, so unless there is a force acting on the servo arm, it should just remain in place.

## Regenerative braking

When a servo stops moving, it can pump a large amount of current into the +5v rail due to regenerative braking (the servo motor effectively becomes a generator). If unchecked, this may increase the voltage of the +5V rail above the safe limit. This applies whether the servo is connected directly to the Duet or to an attached expansion board or expansion breakout board. See 'Recommendations' below.

Very generally, there is a correlation between stall current and the amount of current a servo produces in regenerative braking. However, a servo that has a stall current close to the available 5V current from the Duet might still pump enough current into the 5V rail to be a problem.

## Duet board 5V current limit

Current limit is the total for 5V and 3.3v. Subtract any current draw, including the internal current consumption (usually around 200-300mA), any PanelDue or other display, connected and powered SBC and peripherals, and any endstops/Z probes that draw significant power, to find the available current for a connected servo.

|  | 5V current limit |
|---|---|
| Duet 3 Mainboard 6HC | 3.0 A |
| Duet 3 Mini 5+ | 1.0 A |
| Duet 2 WiFi / Ethernet / Maestro | 2.0 A |

## Notes on specific Duet boards

## Tabs {.tabset}

### Duet 3 Mainboard 6HC

On Duet 3 MB6HC, the internal and external 5V are separated, but the built-in 5V regulator regulates the external +5V rail (this was to provide consistent power for an attached RPi). This means that if a servo pumps power into external +5V, the internal 5V power will drop out for a short time, causing the board to reset. Again, any other devices connected to external +5V e.g. PanelDue may be damaged. Even small '9g' servos may be able to do this.

### Duet 3 Mini 5+

On Duet 3 Mini 5+, the internal and external 5V rails are separated, however the built-in 5V regulator regulates the internal 5V power. So a servo pumping power into external 5V won't damage the Duet or cause it to reset, however it could damage other devices powered from external +5V e.g. PanelDue or 12864 display.

### Duet 2 WiFi / Ethernet / Maestro

On the Duet 2 WiFi / Ethernet / Maestro, if a servo feeds power back (regenerative braking) then the internal 5V rail voltage will increase. This could damage other devices connected to +5V (e.g. PanelDue), also if the rise is high enough it could blow the mosfet drivers (rated at 7V) and the 3.3V regulator (rated at 6.5V). If the 3.3V regulator goes, it may take out other components on the board too.

## Recommendations

* 5V rail voltage rise due to regenerative braking can be solved by connecting a Schottky diode between Duet +5V output and the servo +5V power input, and a 220uF capacitor between servo +5V and servo ground
* Both issues can be avoided by powering the servo from a separate +5 supply.

## Hobby servo specification examples

There are many different types of hobby servos, and we do not recommend any particular one. Check the specification of any servo you connect to your Duet, in particular the idle, running and stall current. Generally, so-called '9g' servos *should* be okay to connect directly (but please check your servo's specs! And see note below about Duet 3 MB6HC), but larger servos should have additional protection, or be powered independently of the Duet (see *Recommendations*).

Servos of similar specification to this should be fine to connect to a Duet without extra circuitry:

| [TowerPro SG90 9G micro servo](https://www.towerpro.com.tw/product/sg90-analog/) ||
|---|---|
| Description | 9g micro-servo |
| Idle current | 6 ± 10 mA |
| Running current | 220 ± 50 mA |
| Stall current | 650 ± 80 mA |

This motor would require extra circuitry (see *Recommendations*) or risk damage to the Duet and/or connected peripherals, particularly due to Regenerative Braking.

| [TowerPro MG995](https://www.towerpro.com.tw/product/mg995/) ||
|---|---|
| Description | 55g X-large servo |
| Current draw at idle | 10 mA |
| No load operating current draw | 170 mA |
| Stall current draw | 1200 mA |

## Firmware configuration

With the hobby servo connected to appropriate outputs on the Duet, configure the servo as follows:

## Tabs {.tabset}

### RepRapFirmware 3.x

Duet 3 MB6HC example
```
;configuration
M950 S1 C"out9" ; assign GPIO port 1 to out9 (Servo header), servo mode

;move servo with
M280 P1 S80  ; set 80deg servo position on GPIO port 1

;turn off servo with
M42 P1 S0
```
Duet 2 example
```
;configuration
M950 S0 C"exp.heater3"  ; assign GPIO port 0 to heater3 on expansion connector, servo mode

;move servo with
M280 P0 S80  ; set 80deg servo position on GPIO port 0

;turn off servo with
M42 P0 S0
```

### RepRapFirmware 2.x

Note that in RRF 2.x, Duet 2 supports 10 GPIO ports, numbers 0 to 9.

```
;configuration
M307 H3 A-1 C-1 D-1 ; disable heater 3

;move servo with
M280 P3 I1 S80  ; set Heater 3 pin to 80deg servo position, inverted

;turn off servo with
M42 P3 I1 S0
```

# Connecting DC motors

* Connect the motor between the positive and negative (PWM controlled) terminal of the chosen heater or fan connector. Fan and heater headers on expansion boards can also be used. 
* You must always use a flyback diode if you connect a brushed DC motor to a heater output. Duet 3, Duet 2 WiFi/Ethernet boards (PCB revision 1.02 and later) and Duet 2 Maestro have built-in flyback diodes on the fan outputs. 
* Make sure the output you choose can handle the current the motor requires.
* Heater outputs can supply higher current than fans, at VIN voltage.
* Fan outputs are lower current, and have selectable voltage (usually VIN, 12V or 5V).
* Set PWM frequency in M950 (RRF 3.x and later) or M42 (RRF 2.x and earlier). Brushless DC motors should be okay at 250Hz, and small brushed DC motors should operate nicely with a PWM frequency of 50Hz to 100Hz, but try a range to get the best motor response.

See the input/output section for your Duet board to determine pin name (RRF 3.x) or logical pin number (RRF 2.x), and pin capability. See the wiring diagram on the same page for the physical location of the pin:

* **Duet 3 Mainboard 6HC:** See [Duet 3 MB6HC Hardware Overview](/Duet3D_hardware/Duet_3_family/Duet_3_Mainboard_6HC_Hardware_Overview#inputoutput)
* **Duet 3 Mini 5+:** See [Duet 3 Mini 5+ Hardware Overview](/Duet3D_hardware/Duet_3_family/Duet_3_Mini_5+_Hardware_Overview#inputoutput)
* **Duet 2 WiFi/ Ethernet:** See [Duet 2 WiFi and Ethernet Hardware Overview](/Duet3D_hardware/Duet_2_family/Duet_2_WiFi_Ethernet_Hardware_Overview#inputoutput)
* **Duet 2 Maestro:** See [Duet 2 Maestro Hardware Overview](/Duet3D_hardware/Duet_2_family/Duet_2_Maestro#inputoutput)

## Cautions when connecting brushed DC motors e.g. air pumps

* Duet 2 (WiFi/Ethernet) boards prior to PCB revision 1.02, do not have built-in flyback diodes on the fan outputs, because standard brushless fan motors do not need them.
  * If you connect a *brushed* DC motor to a fan output, you must connect a flyback diode in parallel with the motor.
  * A 1N400x diode (where x = any digit in 1..7) is suitable. Connect the cathode of the diode (the end with the stripe) to the positive fan wire (the V_FAN terminal on the Duet) and anode to the negative fan wire (the FANx- terminal on the Duet).
* Brushed DC motors may have high start currents, which may exceed the rating of the mosfet.
  * Before connecting a brushed DC motor to a fan output on the Duet, use a multimeter to measure its DC resistance several times.
  * Rotate the motor shaft slightly between readings and take the lowest reading you see (ignore any transient readings while the shaft is being rotated).
  * Divide this value into the V_FAN voltage (typically you will use V_IN) to get the peak current at startup.
  * If this peak current is greater than 3A then you should connect a surge reducing thermistor in series with the fan to reduce the startup current. Here is an example of a thermistor that may be suitable: [Farnell UK](http://uk.farnell.com/epcos/b57236s0250m000/thermistor-ntc/dp/9751904).
  * Alternatively, if you have a spare heater output and you are using firmware 2.02 or later then you can use the heater output as an additional fan output (see later). You must always use a flyback diode if you connect a brushed DC motor to a heater output.

## Firmware configuration

With the DC motor connected to appropriate pins on the Duet, configure the firmware as follows:

## Tabs {.tabset}

### RepRapFirmware 3.x and later

In RRF 3.x and later, pins are undefined at startup and no GPIO ports are allocated by default. Define the heater or fan connector as a GPIO port using [M950](/User_manual/Reference/Gcodes/M950). Control the port using [M42](/User_manual/Reference/Gcodes/M42), where the P parameter is the GPIO port number.

Duet 3:
```
M950 P0 C"out4" Q100 ; allocate GPIO port 0 to OUT_4 (fan output), 100Hz
...
M42 P0 S0.5 ; set 50% PWM on GPIO port 0
```

Duet 2:
```
M950 P0 C"fan0" Q100 ; allocate GPIO port 0 to FAN 0, 100Hz
...
M42 P0 S0.5 ; set 50% PWM on GPIO port 0
```

### RepRapFirmware 2.x and earlier

Starting with RepRapFirmware version 1.16, pin numbers in the [M42](/User_manual/Reference/Gcodes/M42) command are logical pin numbers, not Arduino-like pin numbers. 

*Note that these logical pin numbers are not the physical pin numbers on the expansion header, or the internal pin numbering within the firmware.*

For a list of logical pin numbers, see the 
* **Duet 2 WiFi/ Ethernet:** See [Duet 2 WiFi and Ethernet Hardware Overview](/Duet3D_hardware/Duet_2_family/Duet_2_WiFi_Ethernet_Hardware_Overview#inputoutput)
* **Duet 2 Maestro:** See [Duet 2 Maestro Hardware Overview](/Duet3D_hardware/Duet_2_family/Duet_2_Maestro#inputoutput)

Free up the pin for use as a general purpose I/O pin that can be controlled using M42:
* Disable a heater using `M307 H# A-1 C-1 D-1` to make the pin available, where # is the heater number, and make sure that no tool is configured to use that heater.
* Disable a fan using `M106 P# I-1` to make the pin available, where # is the fan number.

Then use M42 to control the pin, with P, S, F and I parameters.

Example:
```
M307 H2 A-1 C-1 D-1 ; Disable Heater 2 (e1heat)
...
M42 P2 I1 S0.5 F100  ; set Heater 2 pin to 50% PWM at 100Hz, inverted
```

# Connecting a spindle

See [Configuring RepRapFirmware for a CNC machine](/User_manual/Machine_configuration/Configuration_CNC).

