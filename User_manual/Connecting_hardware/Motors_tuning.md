---
title: Tuning stepper motor drivers
description: This article covers tuning Trinamic stepper motor drivers in Duet 3 MB6HC, EB3HC, Toolboard 1LC and Mini 5+, and Duet 2 WiFi / Ethernet and Maestro
published: true
date: 2022-05-12T11:19:51.268Z
tags: 
editor: markdown
dateCreated: 2021-10-12T15:42:36.812Z
---


**THIS PAGE IS A WORK IN PROGRESS!**

# Scope

**This document is relevant to:** Duet 3 MB6HC and EXP3HC, Duet 3 Toolboard 1LC, Duet 3 Mini 5+, Duet 2 WiFi / Ethernet, Duet 2 Maestro

**Firmware versions:** RepRapFirmware 3.1.1 and later, may apply to earlier versions

# Overview

The Duet range of boards feature Trinamic stepper drivers, which have a number of features that, once tuned, can result in improved performance and/or reduced noise.

## Table of features

| Board | Driver chip and Datasheet link | Microstep Interpolation | stealthChop | spreadCycle | stallGuard | coolStep |
|:---|:---|
| Duet 3 MB6HC Duet 3 EB3HC | [TMC2160](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC2160A-datasheet_Rev1.06.pdf) or [TMC5160](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC5160A_Datasheet_Rev1.14.pdf) | Y | Y (SC2) | Y | Y (SG2) | Y |
| Duet 3 Toolboard | [TMC2209](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC2209_Datasheet_V105.pdf) | Y | Y (SC2) | Y | Y (SG4) | Y |
| Duet 3 Mini 5+ | [TMC2209](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC2209_Datasheet_V105.pdf) | Y | Y (SC2) | Y | Y (SG4) | Y |
| Duet 2 WiFi  and Ethernet | [TMC2660](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC2660_datasheet_Rev1.07.pdf) | 16x only | N | Y | Y (SG2) | Y |
| Duet 2 Maestro | [TMC2224](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC220x_TMC2224_Datasheet_Rev1.10.pdf) | Y | Y (SC2) | Y | N | N |

Please note that currently (RRF 3.2), stall detection on Duet 3 tool boards (Duet 3 TOOL1LC) and expansion boards (Duet 3 EB3HC) are not yet reported and cannot be used for homing. See [Duet 3 with CAN expansion firmware configuration limitations](/User_manual/RepRapFirmware/CAN_limitations).

## Microstep Interpolation

Using the ``I1`` parameter in [M350](/User_manual/Reference/Gcodes/M350) will enable microstep interpolation. This smooths movement between microsteps, and is noticeably quieter. There is no loss in performance or extra processor burden.

## stealthChop

stealthChop is a quiet mode of operation for stepper motors at standstill and at low  velocities. It is based on a voltage mode PWM. With stealthChop2, the driver automatically adapts to the application for best performance, but can also be set manually using M569.

**stealthChop is not supported on TMC2660 drivers in Duet 2 WiFi / Ethernet.**

## spreadCycle

While stealthChop is a voltage mode PWM controlled chopper, spreadCycle is a cycle-by-cycle current control. It can react extremely fast to changes in motor velocity or motor load. spreadCycle will give better performance in medium to high velocity range for motors and applications which tend to resonance.

## stallGuard

stallGuard provides a measurement of the load on the motor. It can be used for stall detection as well as other uses at loads below those which stall the motor, such as CoolStep load-adaptive current reduction.

See [Stall detection and sensorless homing](/User_manual/Connecting_hardware/Sensors_stall_detection)

**stallGuard is not supported on TMC2224 drivers in Duet 2 Maestro.**

## coolStep

coolStep allows energy savings by automatically adapting the motor current to the load. Requires stallGuard to be tuned first, as it relies on measuring the motor load.

**coolStep is not supported on TMC2224 drivers in Duet 2 Maestro.**

## Trinamic parameters

| Parameter | Description | Gcode |
|:---|:---|
| toff | General  enable  for  the  motor  driver,  the  actual value does not influence stealthChop. |  |
| thigh | | |
| tcoolthrs | | |
| tblank | | |
| hstart | Hysteresis start value |  |
| hend | Hysteresis end (low) value |  |
| hdec | Hysteresis decrement interval |  |
| pos | | |
| tpwmthrs | Sets the upper velocity for stealthChop voltage PWM mode. | M569 V[nnn] |

# Tuning the stepper drivers

## Standard configuration

[M569](/User_manual/Reference/Gcodes/M569) is used to set stepper driver settings. The following parameters are used to enable tuning of the Trinamic drivers:

* Dnn (firmware 2.0 and later, only applies to TMC2660, TMC22xx, TMC2160, TMC5160 and TMC5161 stepper drivers) Driver mode: 0=constant off time, 1=random off time, 2=spread cycle, 3=stealthChop or stealthChop2 (mode 3 for TMC22xx/TMC2160/TMC516x only). The default is spreadCycle for all drivers. In stealthChop mode the drivers will switch over to spreadCycle automatically at high speeds, see the V parameter.
* Cnnnn (firmware 2.0 and later, only applies to TMC2660, TMC22xx, TMC2160 and TMC516x stepper drivers) Lowest 17 bits of the chopper control register value.
* Bnn (firmware 2.02RC1 and later) Blanking time (tbl) in the chopper control register, 0 to 3. See the TMC driver datasheet.
* Hnn (firmware 2.02RC2 and later) thigh parameter for those stepper driver chips that support it, e.g. TMC5160 and TMC2160. Send M569 P# (where # is the driver number) with no additional parameters to see how this translates into mm/sec. See also the V parameter.
* Yaa:bb or Yaa:bb:cc (firmware 2.02RC2 and later) Hysteresis start, end and decrement values in the chopper control register. See the TMC driver datasheet for the meaning.
* Vnnn (firmware 2.02RC1 and later) tpwmthrs parameter for those stepper driver chips that support it. This is the interval in clock cycles between 1/256 microsteps below which the drivers will switch from stealthChop to to spreadCycle mode. Only applies when the driver is configured in stealthChop mode. Typical value are from 100 (high speed) to 4000 (low speed). Send M569 P# (where # is the driver number) with no additional parameters to see how this translates into mm/sec.

Duet 3 MB6HC output from ``M569 P0``

`
Drive 0 runs forwards, active low enable, step timing fast, mode spreadCycle, ccr 0x08053, toff 3, tblank 1, hstart/hend/hdec 5/0/0, pos 0, tpwmthrs 2000 (4.7 mm/sec), thigh 200 (46.9 mm/sec)
`

Duet 2 WiFi output from ``M569 P0``

`
Drive 0 runs forwards, active low enable, step timing fast, mode spreadCycle, ccr 0x101b4, toff 4, tblank 2, hstart/hend/hdec 3/3/0, pos 0
`

Duet 2 Maestro output from ``M569 P0``

`
Drive 0 runs in reverse, active low enable, step timing fast, mode stealthChop, ccr 0x00053, toff 3, tblank 0, hstart/hend/hdec 5/0/0, pos 72, tpwmthrs 2000 (4.3 mm/sec)
`

## Configuring stealthChop

### Considerations

TMC2224, TMC5160 and TMC2209 have stealthChop. It's a little more complicated to configure on the TMC5160 because this chip also has coolStep. Also, stall detection doesn't work in stealthChop mode, so you have to switch to spreadCycle while doing stall detect homing.

TMC2209 is a little different because it doesn't have coolStep and because stall detection only works in stealthChop mode.

In both cases there are some limitations:

* stealthChop doesn't work well at high speeds. That's why there is a changeover speed defined. Above that speed, the driver switches over to spreadCycle. Unfortunately there is a jolt when that happens. The higher the changeover speed, the worse the jolt.

 From the TMC2160A datasheet:
> A jerk occurs when switching at higher velocities, because the back-EMF of the motor (which rises with the velocity) causes a phase shift of up to 90° between motor voltage and motor current. So when switching at higher velocities between voltage PWM and current PWM mode, this jerk will occur with increased intensity. A high jerk may even produce a temporary overcurrent condition (depending on the motor coil resistance). At low velocities (e.g. 1 to a few 10 RPM), it can be completely neglected for most motors. Therefore, consider the switching jerk when choosing TPWMTHRS. Set TPWMTHRS zero if you want to work with StealthChop only.
{.is-info}


* stealthChop can't react quickly to changes in load. So if your printer is executing a travel move and the nozzle hits a blob or overhang, it is likely to skip steps if the driver is in stealthChop mode.

We suggest the use of one of the following configurations:

1. stealthChop changing over to spreadCycle a very low speed. Compared to running in spreadCycle always, this reduces standstill noise with some motors.
1. stealthChop changing over to spreadCycle at a little over your normal maximum printing speed. It will switch to spreadCycle for travel moves, except very short ones. This will only be viable if the jerk at switchover is tolerable.
1. Limit your travel speed so that you can keep the printer in stealthChop mode always.

If you have interpolation enabled, you may find that your motors are very quiet already. To be able to hear the difference that enabling stealthChop, try reducing microstepping to x8 and disable interpolation.

### stealthChop tuning

These are the conditions you need for the drivers to run in stealthChop mode:

1. tpwmthrs low enough for the speed you are using. This is set by the M569 V parameter. Lower values increase the speed at which the driver will switch over to spreadCycle. M569 with just a P parameter will translate the existing tpwmthrs to mm/sec belt speed for you.
1. thigh low enough for the speed you are using. This is set by the M569 H parameter. The units are the same as for tpwmthrs. M569 with just a P parameter will translate the existing thigh to mm/sec belt speed for you.
1. tcoolthrs low enough for the speed you are using. This is set by the M915 T parameter. The units are the same as for the M569 H and V parameters. Although M915 with P and/or axis parameters sets the value correctly, there is a bug in firmware 3.1.x when you use M915 with just a drive and/or axis parameter. The bug is that the value reported as "coolstep" should be the existing T parameter, but isn't. The value of tcoolthrs defaults to 2000, which is usually too high if you want to use stealthChop. The reason it is this high is to allow stall detection to work. Stall detection is not compatible with stealthChop on the TMC5160.
1. You must execute the proper tuning sequence after switching to stealthChop. This means: apply motor current, pause for more than 130ms, then execute a move at reasonable speed.
1. You must enable stealthChop using M569 P# D3.
1. stealthChop cannot function properly at speeds high enough that it can't supply as much current as it wants to to the stepper motor due to insufficient power supply voltage.

So to use stealthChop, set tpwmthrs, thigh and tcoolthrs all to the same value, for example:

```
M569 P0 D3 H50 v50
M915 P0 T50
```

You can send M569 P0 to see what belt speed the H and V parameters correspond to. What you are satisfied that you have the threshold high enough, execute the tuning procedure. If necessary, you can execute a very short move (just 1 microstep is enough) to power up the motors at the start.

In principle you could use different values of thigh and tcoolthrs so that at speeds too high for stealthChop you still use coolStep, however i'm not sure that there are any speeds at which coolStep provides any advantages over stealthChop.

If you are using stall-detect homing, then in the homing file you must increase the M915 T parameter to a value that corresponds to a speed below the speed of your homing move. [I have not yet checked whether you also need to switch to spreadCycle mode explicitly]. After homing, reduce the M915 T parameter again, then execute the tuning procedure.

## Configuring spreadCycle

## Configuring stallGuard

See [Stall Detection and Sensorless Homing](/User_manual/Connecting_hardware/Sensors_stall_detection).

## Configuring coolStep

coolStep is configured using the T parameter of [M915](/User_manual/Reference/Gcodes/M915). This sets the coolStep control register, with a 16-bit unsigned integer. See the stepper driver documentation for sensible values. As coolStep needs to use the motor loads to dynamically adjust the motor current, stallGuard needs to be set up and tuned first. See [Stall Detection and Sensorless Homing](/User_manual/Connecting_hardware/Sensors_stall_detection).

# Links and Further reading

Forum thread [https://forum.duet3d.com/topic/16297/help-needed-with-duet-3-6hc-tmc5160-stealthchop-config](https://forum.duet3d.com/topic/16297/)