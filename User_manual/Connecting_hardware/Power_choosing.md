---
title: Choosing the power supply
description: 
published: true
date: 2024-07-04T23:28:54.714Z
tags: 
editor: markdown
dateCreated: 2021-10-28T16:29:11.925Z
---

# Introduction

The two main decisions to make when choosing a power supply are the voltage and total power required. In general, 24V is recommended and a power supply that is rated for at least 20% more than is required provides some headroom.

# Considerations

## Voltage

Minimum and maxium input voltages for Duet boards.

| | Minimum input voltage | Max input voltage | 
|---|---|
| Duet 3 Mainboard 6HC | 11V | 48V (v1.02 and later), 32V (v1.01 and earlier) | 
| Duet 3 Mainboard 6XD | 11V | 30V | 
| Deut 3 Mini 5+ | 11V | 25V | 
| Duet 3 Expansion 3HC | 12V | 48V (v1.02 and later), 32V (v1.01 and earlier) |
| Duet 3 Expansion 1XD | 12V | 48V |
| Duet 3 Expansion 1HCL | 12V | 50V  |
| Duet 3 Toolboard 1LC | 12V | 32V  |
| Duet 2 WiFi/Ethernet | 11V | 25V | 
| Duet 2 Maestro | 11V | 25V | 

Usually, 12V or 24V is used because other components (heaters, fans etc.) are readily available at these voltages. There are three factors that may make 24V preferable to 12V:

* A 24V bed heater takes half the current of a 12V heater of the same power. So you can go to double the heating power before you need to use an SSR to control the bed heater. This means that 24V is generally advisable for printers with a bed larger than about 200mm square unless you use an AC mains voltage bed heater. See [Choosing a bed heater](/User_manual/Connecting_hardware/Heaters_bed)
* You may need to use 24V to achieve the travel speed you want without losing too much torque. See [Choosing stepper motors](/User_manual/Connecting_hardware/Motors_choosing).
* If you intend to use the resume-after-power-fail facility, a 24V supply will be able to power your stepper motors for longer before the voltage falls too much. This makes it more likely that it will be able to complete the power fail script.

The Duet 2 Maestro uses TMC2224 drivers, which work best with lower current motors (hence higher inductance) motors. For this reason, we recommend you use 24V power with the Duet 2 Maestro, especially if the printer is a delta. If you use 12V power, the maximum travel speeds you can achieve may be lower.

## Total power needed

* Bed heating power: this is effectively zero if you will be using an AC mains voltage heater. Otherwise, use the rated power of your chosen bed heater. If you might want to turn the voltage up so as to increase heating power, allow for this (power rises as the square of the voltage).
* Extruder heater power: Use the power rating of your heater cartridges. If you will have dual extrusion, allow for heating both up together. If you will be turning up the power supply voltage to improve bed heating power, remember that this increases extruder heater power too.
* Stepper motors: at full rated current, the power needed for each motor is its rated current times its rated voltage (if no rated voltage is specified, use the square of the rated current times the phase resistance). This is the power at standstill. To allow for driver losses and the extra power needed to create acceleration, add 50%. Multiply by the number of stepper motors.
* Fans: 3W per fan is a generous allowance for small axial fans. For blower fans, work out the power requirement from the datasheet. Note the fan outputs are rated to 1.5A maximum.
* PanelDue: with a 7" screen attached it draws about 3.5W.
* Duet: allow 2W.
* Add the power requirement of any lighting and any servos etc. that you intend to use.

## Inrush current

Inrush current is the maximum current drawn by an electrical circuit at the time it is turned on. This applies more for boards with lots of capacitance, and especially systems with a number of boards and M23CLs where there is a large total capacitance.

Generally, this applies if you use a relay between the DC PSU and Vin to control power to the machine, i.e. the power supply is already switched on. It also applies to switching power to anything with a lot of capacitance across the power lines. It's not the same if power is applied by switching on the power supply, as those are likely to have current limits/soft start/over current protection of some sort.

If your PSU is switching on power with a relay then you should use an inrush current thermistor in series with the Vin line, e.g. [https://www.mouser.co.uk/c/circuit-protection/thermistors/inrush-current-limiters/](https://www.mouser.co.uk/c/circuit-protection/thermistors/inrush-current-limiters/){target=_blank}

The thermistor should be matched to the Duet board. For example, the M23CL is fused internally to 2A, so a 2A current limiting thermistor is appropriate.

# Types of power supply

## MeanWell or similar quality power supply

These can be purchased from MeanWell resellers. The usual electronic component distributors sell similar power supplies.

This is typically the best option if you are comfortable with wiring the mains voltage terminals and with the price. These power supplies are invariably CE-approved and typically include power factor correction. They are available in 12V and 24V versions with a wide range of power ratings. They usually include a voltage adjustment potentiometer.

## CCTV/LED power supply

These are similar to the MeanWell supplies but are made to lower quality standards. They are available very cheaply from eBay suppliers. They usually claim to be CE-compliant, but take this with a pinch of salt because some of them use leaded solder. They include voltage adjustment potentiometers.

Expect these to be less reliable than MeanWell and similar quality PSUs. On the other hand, they may also give years of service, and if they do fail they are cheap to replace. If you choose one of these then it is recommended that you get one with at least 20% more power capacity than you expect to need.

![power_supply_01.jpg](/manual/wiring/power_supply_01.jpg =400x)

Here's an example of what may happen when they fail. The output inductor was clearly under-rated for this application. The PCB below was badly burned.

## ATX power supply

Widely available from eBay, Amazon etc. and from PC parts sellers. You can get cheap no-name ATX power supplies, and also ones from a respectable brand such as Corsair.

Advantages compared to the other types listed above:

* No mains wiring needed, just plug in an IEC mains cable.
* All high voltage wiring is enclosed
* They include a PS_ON connection, so they can be turned off by the printer without needing further electronics
* They also provide a 5V output, although with a Duet you don't need this because the Duet has its own 5V regulator

Disadvantages:

* Not suitable if you need 24V power
* No voltage adjustment potentiometer
* You need to put a dummy load on at least the 5V output, otherwise the power supply is likely to cut out
* With the cheaper ATX PSUs, the regulation on the 12V output is poor because the 5V and 12V outputs are regulated together
* The fans in the power supply can be quite noisy. The other types of power supply may be available in fanless versions, depending on the power rating you need.

The power rating of an ATX supply is based on a combined load on the 12V, 5V, and 3.3V outputs. So you need to ensure that the 12V output alone is capable of supplying the entire power requirement of your printer.