---
title: Connecting Digital Humidity and Temperature (DHT) sensors
description: Describes choosing, connecting and configuring Digital Humidity and Temperature (DHT) sensors.
published: true
date: 2021-12-15T15:07:59.499Z
tags: 
editor: markdown
dateCreated: 2021-09-14T16:07:27.411Z
---

# Scope

**This document is relevant to:** Duet 2, Duet 3
**Firmware versions:** RepRapFirmware 1.20 and later

# Introduction

DHT sensors are low-cost digital temperature and humidity sensors. They use a capacitive humidity sensor and a thermistor to measure the surrounding air, and transmit a digital signal on the data pin to the host, so no analog input pins are needed.

RepRapFirmware from v1.20 supports DHT11, DHT21 and DHT22 sensors.

# General recommendations

* DHT 22 is recommended. DHT21 is also supported. DHT11 is supported in firmware up to RRF 3.3, but removed from RRF 3.4 onward.
* Connect using 3 wires. As well as +3.3V and ground, the DHT sensor needs a combined input-output line. This is usually connected to one of the SPI CS lines on the Temperature Daughterboard connector, or one of the IOx.OUT pins.
* In RRF 3, the humidity channel of the same sensor is configured as a piggyback sensor off the main one.

# Choosing a DHT sensor

Which sensor you use will depend on your application. DHT sensors can be bought as a bare sensor, on a PCB, or as a wired version in a large plastic case.

# Tabs {.tabset}

## DHT22

Recommended. More precise, more accurate and works in a bigger range of temperature/humidity. Larger and more expensive than DHT11, and only updates once every 2 seconds.

* Humidity: 0-100% humidity readings with 2-5% accuracy
* Temperature: -40 to 80°C temperature readings ±0.5°C accuracy
* Sensing period: 0.5 Hz sampling rate (once every 2 seconds)
* Dimensions: 32mm x 15mm x 7mm

**NOTE: Cloned DHT22 sensors.** One customers reported problems getting a DHT22 sensor working with RRF, even though the sensor worked with an Arduino. The issue was investigated and it was discovered that the sensor did not conform to the specification of the DHT22 sensor as on the datasheet provided by Adafruit.

![dht_22_clone.jpg](/hardware/dht/dht_22_clone.jpg =400x)

The customer's sensor is pictured above (top in picture) and a sensor that works  (below in picture). The working sensor carries the identification "AOSONG AM2302" whereas the customer's sensor carries no markings at all. The vast majority of images of DHT22 sensors on Amazon and eBay do carry the part identification, but a few do not and look like the sensor received from the customer. Therefore, if you need a DHT22 sensor for your Duet-based system, please make sure to buy a genuine one bearing the correct part identification. See [this forum thread](https://forum.duet3d.com/topic/24398/warning-about-dht22-sensors) for more information.

## DHT21

Usually packaged in a large case, with three wires. Similar specification to DHT22, but less accurate.

* Humidity: 0-100% humidity readings with 3-5% accuracy
* Temperature: -40 to 80°C temperature readings ±1°C accuracy
* Sensing period: 0.5 Hz sampling rate (once every 2 seconds)
* Dimensions: 59mm x 27mm x 13.5mm

## DHT11

Usually cheapest, with limited range for temperature and humidity, but small and updates every second. Not supported in RRF 3.4. or later

* Humidity: 20-80% humidity readings with 5% accuracy
* Temperature: 0-50°C temperature readings ±2°C accuracy
* Sensing period: 1 Hz sampling rate (once every second)
* Dimensions: 22.5mm x 12mm x 5.5mm

# Connecting a DHT sensor

## Cable

Connect the sensor to +3.3V and GND. Do not use 5V unless you use a bidirectional level shifter between the Duet and the DHT22.

The one-wire bus used by the DHT sensor for data is sensitive to noise, so if you use unshielded cable it is very likely to pick up interference from any stepper motor cables that it runs close to. We recommend using shielded cable (stereo microphone cable should work), using the shield for the ground connection.

Bare DHT sensors usually come with a pullup resistor of between 4k7 and 10k ohms. DHT sensors on a PCB, or encased and pre-wired, usually have the pullup resistor already fitted on the PCB. The pullup resistor goes between the signal line and 3.3V.

Cable capacitance should not exceed about 2000pF. Using a lower value resistor will allow longer cable runs, eg a 2k2 ohm, but be more susceptible to noise. Shielded cables normally specify the capacitance/meter, so you should be able to work out the highest value pullup resistor you can use for a specific length and type of cable. If a higher pullup resistor is used then the max cable length is reduced. In testing, dc42 uses a 10K pullup resistor and 400mm of cable.

# Tabs {.tabset}


## Duet 3 MB6HC

Connect the DHT I/O line to either one of the SPI CS lines on the Temperature Daughterboard connector, or one of the IOx.OUT pins. Both of these connectors also provides +3.3V and ground.

## Duet 3 Mini 5+

Connect the DHT I/O line to both IOx.IN and the IOx.OUT pin of an IO connector. The IO connector also provides +3.3V and ground. Using the Temperature Daughterboard connector is NOT supported. RepRapFirmware 3.3beta1 or later is required.

## Duet 2

Connect the DHT I/O line to one of the SPI CS lines on the temperature daughterboard connector, which also provides +3.3V and ground. For systems on RRF 3 without a DueX, some of the expansion connector pins could be used instead, for example the E2 to E6 endstop pins (E0 and E1 on the Duet cannot be used).

# Configuring a DHT sensor

# Tabs {.tabset}

## RepRapFirmware 3.x

In RRF 3, [M308](/User_manual/Reference/Gcodes/M308) is used to define the DHT sensor. Use the following parameters:

* **Sn** Sensor number.
* **P"pin_name"** See section above for pins to use with each Duet version.
* **Y"sensor_type"** "dht11" (RRF 3.3 and earlier), "dht21" or "dht22", and "dhthumidity"
* **A"name"** Sensor name (optional), displayed in the web interface

All DHT variants have a primary output for temperature and a secondary output that delivers the humidity values. "dhthumidity" will be attached to an existing DHT sensor's secondary output by using its full sensor number (including the leading S) and the output's index separated by a dot.

Example:

```
;DHT Sensor on Temperature Daughterboard SPI CS1 pin
M308 S10 P"0.spi.cs1" Y"dht22" A"Filament Temp" ; define DHT22 temperature sensor
M308 S11 P"S10.1" Y"dhthumidity" A"Filament Hum[%]" ; Attach DHT22 humidity sensor to secondary output of temperature sensor
```

On **Duet 3 Mini 5+**, as you connect the DHT data line to both IOx.out and IOx. in, you need to specify both pins in the M308 command.

```
;DHT Sensor on IO4 on Duet 3 Mini 5+
M308 S10 P"io4.out+io4.in" Y"dht22" A"Chbr Temp[C]"
M308 S11 P"S10.1" Y"dhthumidity" A"Chbr Hum[%]"
```

## RepRapFirmware 2.x

In RRF 2 and earlier, [M305](/User_manual/Reference/Gcodes/M305) is used to define the DHT sensor. In RRF 2, every temperature sensor belongs to a heater. For sensors with no controllable heater (e.g. DHT sensors) you have to create a "virtual heater" in order to be able to use the sensor. Use the following parameters:

* **Pnnn** Heater number (0, 1, 2...) or virtual heater number (100, 101, 102...)
* **S"name"** Heater name (optional). Named virtual heaters are shown in Duet Web Control; anonymous virtual heaters are not.
* **Xnnn** Heater ADC channel, or thermocouple or PT100 adapter channel; defaults to the same value as the P parameter
* **Tnnn** (for thermistor sensors) The thermistor resistance at 25oC

The X parameter tells the firmware which temperature sensor channel to use. For DHT sensors, set as follows:

* Channels 400, 401... are DHTxx temperature channels. The DATA line of the DHTxx must be connected to one of pins CS1, CS2... on the SPI bus. 
* Specify the sensor type (11 for DHT11, 21 for DHT21 or 22 for DHT22) via the T-parameter.
* Channels 450, 451... are as 400, 401... but specify the corresponding humidity sensor of the DHTxx

Example:

```
;DHT Sensor on Temperature Daughterboard SPI CS1 pin
M305 P103 X400 T22 S"DHT Temperature" 
M305 P104 X450 T22 S"DHT Humidity [%]"
```

# Displaying DHT data

![dht_dwc.png](/hardware/dht/dht_dwc.png =600x)

View the DHT sensor data in DWC, in the main Printer Status section, in the 'Extra' view of the 'Tools' section (see [Duet Web Control manual](https://docs.duet3d.com/User_manual/Reference/Duet_Web_Control_Manual#extra-view)).