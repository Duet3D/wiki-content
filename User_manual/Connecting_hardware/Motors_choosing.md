---
title: Choosing stepper motors
description: This page provides in depth consideration for choosing stepper motors
published: true
date: 2022-06-13T16:48:37.630Z
tags: 
editor: markdown
dateCreated: 2021-10-05T11:49:00.204Z
---

# General recommendations

* Unless you will be using external stepper motor drivers, choose motors with a rated current of at most 7A for Duet 3 Mainboard 6HC and Expansion 3HC boards, 3A for the Duet 2 WiFi/Ethernet, 2.4A for Duet 3 Mini 5+, 2.0A for Duet 2 Maestro and 1.7A for Duet 3 Toolboard 1LC.
* Choose axis motors with a rated current of at least 1.2A. Lower current = higher inductance = lower speed before loss of torque.
* You can use motors with a lower rated current (eg 500mA) for geared extruder motors, as these motors don't tend to run as fast or need as much torque as axis motors.
* Plan to run each stepper motor at between 50% and 85% of its rated current.
* Size: Nema 17 is the most popular size used in 3D printers. Nema 14 is an alternative in a highly-geared extruder. Use Nema 23 motors if you cannot get sufficient torque from long Nema 17 motors. Duet 3 6HC/3HC can drive Nema 34 motors too.
* Avoid motors with rated voltage (or product of rated current and phase resistance) > 4V or inductance > 4mH.
* Choose 0.9deg/step motors where you want extra positioning accuracy, e.g. for the tower motors of a delta printer. Otherwise choose 1.8deg/step motors.
* If you use any 0.9deg/step motors, or high torque motors, use 24V power so that you will be able to maintain torque at higher speeds.
* If using a highly-geared extruder (for example, an extruder that uses a flexible drive cable to transmit the torque from the motor to a worm reduction gear), use a short low-inductance 1.8deg/step motor to drive it.

The following sections discuss stepper motor features and specifications in more detail.

# Detailed considerations

## Number of wires (unipolar/bipolar)

The Duet boards use bipolar stepper motor drivers. This means you can use stepper motors suitable for bipolar drive, which have 4, 6 or 8 wires. You cannot use motors with 5 wires, because those are intended to be driven in unipolar mode only. (Some unipolar motors can be made into bipolar motors by cutting a trace on a circuit board.)

Simplest to connect are 4-wire motors.  Inside the stepper motor are two coils, each coil having a wire connected to each end. The wire and coil pairs are called a phase. The 4 wires map to the 4 output pins of each stepper driver on the Duet (see below for identifying phases and connecting).

With 6-wire stepper motors, there are still 2 coils, but each coil has a centre tap, effectively cutting the coil in half if needed. This creates an extra wire for each coil. You can choose to run them in half-coil, by leaving two end-wires unconnected, or full coil mode by leaving the centre wires unconnected. See the motor specification to check that your Duet can supply enough current for how you want to wire them.

8-wire stepper have 4 coils, so with two wires per coil, this makes 8 wires. You can run an 8-wire stepper in half-coil (with only 2 coils connected) or full-coil mode, and in full-coil mode you can choose to wire the coils in series or in parallel. There's plenty of other documentation around the internet on how to do this, just make sure that the Duet can cope with the current requirement. Ultimately, we still need only 4 wires to connect to the Duet.

## Rated current

This is the maximum current you may pass through both windings at the same time. The maximum current through one winding (which is what really matters when using microstepping) is rarely quoted and will be a little higher. However, even with one winding driven at the quoted rated current, the motor will get very hot. So the usual practice is to set the motor current to no more than about 85% of the rated current. Therefore, to get maximum torque out of your motors without overheating them, you should choose motors with a current rating no more than 25% higher than the recommended maximum stepper driver current. This gives:

* Duet 3 Mainboard 6HC and Expansion board 3HC  (recommended maximum motor current 6.3A peak/4.45A RMS) => Stepper motor rated current <= 7A
* Duet 3 Mini 5+ (recommended maximum motor current 2.0A peak) => Stepper motor rated current <= 2.4A
* Duet 3 Tooboard (recommended maximum motor current 1.4A peak) => Stepper motor rated current <= 1.7A
* Duet 2 WiFi and Duet 2 Ethernet (maximum motor current 2.4A peak) => Stepper motor rated current <= 3.0A
* Duet 2 Maestro (maximum motor current 1.6A peak with good fan cooling) => Stepper motor rated current <= 2.0A. 

However, if you use motors with lower current (e.g. 1.0 to 1.2A) and 24V power, then the drivers will run cooler.

## Holding torque

This is the maximum torque that the motor can provide with both windings energised at full current before it starts jumping steps. The holding torque with one winding energised at the rated current is about 1/sqrt(2) times that. The torque is proportional to current (except at very low currents), so for example if you set the drivers to 85% of the motor rated current, then the maximum torque will be 85% * 0.707 = 60% of the specified holding torque.

Torque is produced when the rotor angle is different from the ideal angle that corresponds to the current in its windings. When a stepper motor is accelerating, it has to produce torque to overcome its own rotor inertia and the mass of the load it is driving. In order to produce this torque, the rotor angle must lag the ideal angle. In turn, the load will lag the position commanded by the firmware.

You will sometimes see it written that microstepping reduces torque. What this really means is that when the lag angle is assumed to be equal to the angle corresponding to one microstep (because you want the position to be accurate to within one microstep), higher microstepping implies a smaller lag angle, hence lower torque. The torque per unit lag angle (which is what really matters) does not reduce with increased microstepping. To put it another way, sending the motor a single 1/16 microstep results in exactly the same phase currents (and therefore the same forces) as sending it two 1/32 microsteps, or four 1/64 microsteps, and so on.

## Size

There are two relevant sizes: the Nema size number and the length. The Nema size number defines the square dimension of the body and the mounting hole positions. The most popular size for 3D printers is Nema 17, which has a body no more than 42.3mm square and fixing holes in a square of side 31mm.

**Nema 17** motors come in various lengths, ranging from 20mm long "pancake" motors to 60mm long motors. As a general rule, the longer a motor is, the greater its holding torque at rated current. Longer stepper motors also have greater rotor inertia. All Duets should be able to drive these, though some Nema 17 motors can be rated up to 2A, which is at the limit of the Duet 2 Maestro (though you can always run motors with less current).

**Nema 23** motors offer higher torque than Nema 17 motors. The Duet 2 (WiFi and Ethernet) can drive them if you choose them carefully, in particular in respect of rated current, up to a maximum of around 2.8A. Duet 3 6HC/3HC should be able to drive larger motors, up to 5.5A. You should use 24V power on Duet 2, and 32V power on Duet 3 6HC/3HC for larger motors.

**Nema 34** motors are even larger, with more torque, and generally used in CNC applications. Duet 3 6HC/3HC can drive these motors too, up to 5.5A. To achieve high speeds with large motors, you may need higher voltages than the 32V maximum for the Duet 3 6HC/3HC. It is possible to modify the Duet 3 6HC/3HC to increase this to 48V, and possibly 60V (which is the limit of the stepper driver), though this will invalidate your warranty; see [this forum thread](https://forum.duet3d.com/post/133293).

## Step angle

There are two common step angles: 0.9 and 1.8 degrees per full step, corresponding to 400 and 200 steps/revolution. Most 3D printers use 1.8 deg/step motors.

Aside from the obvious difference in step angle:

* 0.9deg motors have slightly lower holding torque than similar 1.8deg motors from the same manufacturer
* However, to produce a given torque, the lag angle needed by a 0.9deg motor is slightly more than half the lag angle of a similar 1.8deg motor. Or to put it another way, at small lag angles a 0.9deg motor has nearly twice as much torque as a 1.8deg motor for the same lag angle.
* At a given rotation speed, a 0.9deg motor produces twice as much inductive back emf as a 1.8deg motor. So you generally need to use 24V power to achieve high speeds with 0.9deg motors.
* 0.9deg motors need step pulses to be delivered to the drivers at twice the rate of 1.8deg motors. If you use high microstepping then the speed could be limited by the rate at which the electronics can generate step pulses. The interpolation mode at 16x microstepping of the TMC2660 drivers on the Duet 2 WiFi/Ethernet can be used to overcome this issue. The drivers on Duet 2 Maestro and Duet 3 can interpolate at any microstepping setting.

## Inductance

The inductance of the motor affects how fast the stepper motor driver can drive the motor before the torque drops off. If we temporarily ignore the back emf due to rotation (see later) and the rated motor voltage is much less than the driver supply voltage, then the maximum revs/second before torque drops off is:

revs_per_second = (2 * supply_voltage)/(steps_per_rev * pi * inductance * current)

If the motor is driving a GT2 belt via a pulley, this gives the maximum speed in mm/sec as:

speed = (4 * pulley_teeth * supply_voltage)/(steps_per_rev * pi * inductance * current)

Example: a 1.8deg/step (i.e. 200 steps/rev) motor with 4mH inductance run at 1.5A using a 12V supply, and driving a GT2 belt with 20 tooth pulley would start losing torque at about 250mm/sec. This is the belt speed, which on a CoreXY or delta printer is not the same as the head speed.

In practice the torque will drop off sooner than this because of the back emf caused by motion, and because the above doesn't allow for the winding resistance. Low inductance motors also have low back emf due to rotation.

What this means is that if we want to achieve high speeds, we need low inductance motors and high supply voltage. The maximum recommended supply voltage for Duet 2 WiFi/Ethernet is 25V, for Duet 2 Maestro is 28V, and for Duet 3 is 32V.

## Resistance and rated voltage

These are simply the resistance per phase, and the voltage drop across each phase when the motor is stationary and the phase is passing its rated current (which is the product of the resistance and the rated current). These are unimportant, except that the rated voltage should be well below the power supply voltage to the stepper drivers.

## Back emf due to rotation

When a stepper motor rotates it produces a back emf. At the ideal zero lag angle, this is 90 degrees out of phase with the driving voltage, and in phase with the back emf due to inductance. When the motor is producing maximum torque and is on the verge of skipping a step, it is in phase with the current.

Back emf due to rotation is not normally specified on the data sheet, but we can estimate it from this formula:

approximate_peak_back_emf_due_to_rotation = sqrt(2) * pi * rated_holding_torque * revs_per_second / rated_current

The formula assumes that the holding torque is specified with both phases energised at the rated current. If it is specified with only one phase energised, replace the sqrt(2) by 2.

Example: consider a 200 step motor driving a carriage via a 20 tooth pulley and GT2 belt. That's 40mm movement per rev. To achieve a speed of 200mm/sec we need 5 revs/sec. If we use a motor with 0.55Nm holding torque when both phases are driven at 1.68A, the peak back emf due to rotation is 1.414 * 3.142 * 0.55 * 5/1.68 = 7.3V.

How accurate is this formula? dc42 measured and then calculated the back emf for two types of motor:

* 17HS19-1684S: measured 24V, calculated 24.24V assuming holding torque is specified with both phases energised at rated current.
* JK42HS34-1334A: measured 22V, calculated 15.93V assuming 0.22Nm holding torque with both phases energised at rated current. Perhaps the holding torque for this motor is specified with only one phase energised, in which case the calculated value becomes 22.53V. I have also seen the holding torque for this motor given in a different datasheet as 0.26Nm , which increases the calculated value to 18.05V.

# Working out the power supply requirements

If you have a target travel speed for your printer, you can work out at least approximately what supply voltage you will need to the motor drivers. Here's how, with an example calculation:

1. Decide on your target travel speed. For this example I will use 200mm/sec.
1. From the target travel speed, work out the worst-case maximum belt speed. For a Cartesian printer, the worst case is a pure X or Y motion, so the worst case belt speed is the same as the travel speed. For a CoreXY printer, the worst case is a diagonal motion and the corresponding belt speed is sqrt(2) times the travel speed. For a delta printer the worst case is a radial move near the edge of the bed and the worst case belt speed is the travel speed divided by tan(theta) where theta is the smallest angle of a diagonal rod to the horizontal. In practice we can't use the target travel speed for radial moves right up to the edge of the bed because of the distance needed to accelerate or decelerate, so take theta as the angle when the nozzle is about 10mm from the edge of the bed opposite a tower. For my delta this is 30 degrees, so the maximum belt speed is 200/tan(30deg) = 346mm/sec.
1. Work out the motor revs per second at the maximum belt speed, by dividing the belt speed by the belt tooth pitch (2mm for GT2 belts) and the number of teeth on the pulley. My delta uses 20-tooth pulleys so the maximum revs per second is 346/(2 * 20) = 8.7.
1. Work out the peak back emf due to inductance. This is revs_per_second * pi * motor_current * motor_inductance * N/2 where N is the number of full steps per revolution (so 200 for 1.8deg motors, or 400 for 0.9deg motors). My motors are 0.9deg with 4.1mH inductance and I generally run them at 1A. So the back emf due to inductance is 8.7 * 3.142 * 1.0 * 4.1e-3 * 400/2 = 22.4V.
1. Work out the approximate back emf due to rotation. From the formula given earlier, this is sqrt(2) * pi * rated_holding_torque * revs_per_second / rated_current. My motors have rated current of 1.68A and holding torque of 0.44Nm, so the result is 1.414 * 3.142 * 0.44 * 8.7/1.68 = 10.1V
1. Preferably, the driver supply voltage should be at least the sum of these two back emfs, plus a few more volts. If you have two motors in series then the required voltage is doubled.

In my example, this gives 32.5V, which is above the 25V recommended input voltage for the Duet 2. But at least we know that for a worst-case delta move with 200mm/sec travel speed, if I use a 24V supply then that is more than 2/3 of the theoretical value, so the torque available for that move should not drop off by more than about 1/3 of the usual torque available. On the other hand, a 12V supply would clearly be inadequate - which explains why I was only able to achieve 150mm/sec before I upgraded the printer to 24V.

There is an online calculator to do this the other way round (i.e. work out the speed at which torque starts to drop off): [EMF Calculator](https://www.reprapfirmware.org/emf.html).

# Choosing an extruder drive

Selecting an appropriate extruder stepper motor and gearing for the best performance is a compromise between weight, torque, resolution and acceleration.

An extruder drive should have sufficient torque to push the filament through the nozzle at the printing speed you want to use, and sufficient resolution so that individual extruder microsteps are not visible in the print. When there is a Bowden tube between the extruder drive and the hot end, you also need sufficient acceleration to retract the filament fast quickly enough to avoid blobs.

You can calculate the  force that an extruder drive can provide before it skips steps if you know its steps/mm and the characteristics of the stepper motor it uses. If using a 1.8deg/step motor, the extruder force in Newtons (N) is:

`Extruder_force_at_rated_current = Motor_holding_torque * Extruder_steps_per_mm * 0.0014`

For a 0.9deg/step motor:

`Extruder_force_at_rated_current = Motor_holding_torque * Extruder_steps_per_mm * 0.0007`

where the extruder steps/mm is specified at 16x microstepping, and the motor holding torque is specified in Ncm with both phases energised at the rated current. The steps/mm take into account the gearing (if any) and the diameter of the hobbed shaft.

For 1.75mm PLA filament extruded through a 0.4mm or 0.4mm nozzle, aim for extruder force in the range 10N to 25N at rated motor current, and aim to run at 50% to 85% of rated motor current. For 3mm filament, aim for 3 times the extruder force, so between 30N and 75N at rated current.

If you have too little extruder force, the extruder may not be able to push the filament through the nozzle except at excessively low print speeds or at high print temperatures (which increases stringing). The problem with too much extruder force is that if the nozzle becomes temporarily obstructed, then you want the extruder to skip steps. If instead it grinds an indentation into the filament, then extrusion won't restart when the obstruction is removed.

## Un-geared extruders

These typically have steps/mm of about 100 using 1.8/deg motors @ x16 microstepping. To reach 10N force, you need a motor with 71Ncm force. Very few Nema 17 motors have this amount of torque, so you will probably have to make do with 60Ncm, giving a maximum 8.4N force.

The low steps/mm makes it more likely that individual steps will be visible in the extruded filament. With 1.75mm filament and a 0.4mm diameter nozzle, 100 steps/mm going in gives about 5 steps/mm in the extruded filament. You can use a 0.9deg motor to double the steps/mm.

An un-geared extruder design can also be used with a stepper motor with an integral planetary gearbox of about 5:1 ratio.

## Geared extruders

As well as having gearing, a geared extruder normally has a hobbed shaft with a smaller diameter than an un-geared extruder, because the hobbed insert doesn't have to fit over the stepper motor shaft. Typical steps/mm around 420 with 3:1 gearing, and 650 with 5:1 gearing. This means that you need only about 17Ncm of motor torque to exceed the target 10N force (for 1.75mm filament) with 3:1 gearing, or 11Ncm using 5:1 gearing.

Beware of using motors that have many times too much torque. You can reduce the motor current to reduce the force, however high torque motors also have high rotor inertia, so by reducing the current you also reduce the available acceleration.

## Remote direct drive extruders

These typically have gearing of 30:1 to 40:1 and steps/mm of around 4200.
