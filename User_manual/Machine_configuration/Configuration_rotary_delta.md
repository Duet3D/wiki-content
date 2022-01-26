---
title: Configuring RepRapFirmware for a Rotary Delta
description: 
published: true
date: 2022-01-26T15:14:45.550Z
tags: 
editor: markdown
dateCreated: 2022-01-26T15:14:26.484Z
---

# Introduction

A rotary delta printer has three radial upper arms that rotate in a vertical plane about bearings at their inner ends.  These joints are driven by motors, which control the angle of each arm above or below the horizontal. The outer end or each arm is jointed to the top end of a pair of rods. The bottom ends of the  rods are jointed to the effector.

Rotary Delta mechanisms are rarely used for 3D printing (unlike Linear Delta mechanisms), however they are commonly used in pick-and-place machines.

As for linear deltas, the firmware assumes that seen from above the motors and arms go anticlockwise in the order X, Y, Z; and that if the machine is viewed so that +X motion of the effector is from left to right, the Z joint is that the back of the machine.

Support for rotary deltas was added in RRF 2.02. RRF 3.3 added auto calibration (experimental) and the use of segmented moves for Z.

# Configuration

## M669 command

To select rotary delta mode and configure the machine parameters use the [M669](/User_manual/Reference/Gcodes/M669) command in config.g:

`M669 K10 Uxx:yy:zz Lxx:yy:zz Rrr Aaa:bb  Hxx:yy:zz Xxx Yyy Zzz`

The parameters have the following meanings:

* **K10** selects rotary delta mode
* **Uxx:yy:zz** sets the lengths of the X, Y and Z arms measured between each inner bearing and the midpoint of the outer joints. If only one value is provided, that value will be used for all 3 arm lengths.
* **Lxx:yy:zz** sets the lengths of the rod pairs (X rods, Y rods, Z rods) measured between joint centres. If only one value is provided, that value will be used for all 3 rod pairs.
* **Aaa:bb** sets the minimum (aa) and maximum (bb) angles of the upper arms, measured in degrees above the horizontal of the joints relative to the bearing. The maximum angle is also the nominal position at which the homing switches trigger.
* **Rrr** is the delta radius. This is the radius on which the upper arm bearings are located, less the effector radius measured to the midpoint of each pair of rod joints.
* **Hxx:yy:zz** are the effective heights of the upper arm bearings above the effector joints when the effector is at the height corresponding to the desired Z=0 position. If only one value is provided, it will be used for all three bearing heights. Another way to calculate it is as the height of the upper arm inner bearings above the floor, less the height of the rod ends bearings on the effector above the tool tip.
* **Xxx, Yyy, Zzz** (optional, default zero) are the upper arm inner joint angle corrections relative to the standard angles of -150, -30 and +90 degrees, measured in degrees anticlockwise seen from above.
* **Bxxx** is the safe printing radius

## M666 command

As for linear deltas, the [M666](/User_manual/Reference/Gcodes/M666) command may be used to trim the homing switch positions:

`M666 Xxx Yxx Zzz`

The X, Y and Z parameters in this case are the angles (in degrees) that must be added to the nominal upper angle at which the homing switches trigger to get the actual trigger angles.

## M92 command

The X, Y and Z values in the [M92](/User_manual/Reference/Gcodes/M92) command must be the number of **microsteps per degree of arm rotation**. This is calculated as the microstepping selected (default 16) divided by the motor step angle (normally 1.8 or 0.9 degrees), and multiplied by the ratio of any gearing between the stepper motor shaft and the joint.

## M208 command

The [M208](/User_manual/Reference/Gcodes/M208) Z parameters should be set to the maximum and minimum permitted Z coordinates.

# Commissioning a rotary delta

## Test the motors individually

Test the three arm motors individually by putting the printer in relative mode ([G91](/User_manual/Reference/Gcodes/G91) command) and then using the [G1](/User_manual/Reference/Gcodes/G1) command with H2 parameter (S2 in RRF 2.01 and earlier). For example, sending `G1 H2 X5 F60` should move the X arm up 5 degrees at 1 degree per second (60 degrees per minute), and sending `G1 H2 X-5 F60` should move the X arm down 5 degrees. Similarly for Y and Z if you replace X by Y or Z in the above commands. If a motor moves the wrong way, you can reverse it by changing the corresponding [M569](/User_manual/Reference/Gcodes/M569) command in config.g. For example, if the X motor moves the wrong way, change `M569 P0 S1` to `M569 P0 S0` or vice versa.

## Test endstop switches

Use the [M119](/User_manual/Reference/Gcodes/M119) command to read the state of the endstop switches. When the X arm is in its maximum up position so that it is pressing against the endstop switch, it should report "X: at max stop". At any other position it should report "X: not stopped". Similarly for Y and Z.

## Test homing

Set up your homedelta.g file as you would for a linear delta (see [Configuring RepRapFirmware for a Linear Delta Printer](/User_manual/Machine_configuration/Configuration_linear_delta)) except that in all G1 H1 and G1 H2 commands, the F parameter should be the speed in degrees per second. Then test the Home All function. The arms should move to the maximum position until they all trigger the switches, then back off a little, then move more slowly to the maximum position. Finally the effector will drop 5mm and be centred.

## Adjust endstop offsets

After homing the printer, send `G90` then `G1 S2 X0 Y0 Z0 F60`. It should move all 3 arms to the horizontal position (at 1deg/second). The next step is to adjust the endstop offsets so that that the arms are all exactly horizontal after sending this command. So check them, for example using a spirit level. **Note: "horizontal" here means that a line between the rotation centres of the joints at either end of the arm is horizontal.**

To get an arm to be horizontal, you need to adjust what the firmware thinks the arm angles are when the endstop is triggered. The arm angles are given by the second number in the A parameter of the M669 command. For example, you may have used A-25:30 meaning that the minimum arm angle is 25 degrees below the horizontal, and the maximum arm angle (which is the angle at which the endstop triggers) is 30 degrees above the horizontal. If after sending G1 S2 X0 Y0 Z0 F60 you find all arms are below the horizontal, subtract the angular error from the second A value. For example, using the above figures, if the command places the arms 2 degrees below the horizontal then change A-25:30 to A-25:28. Similarly, if the command leaves the arms above the horizontal, increase the second A parameter by the angular error.

You can fine-tune individual arms using the M666 command.  When used in config.g, this command must come **after** the M679 command. For example, `M666 X0.1 Y0.2 Z-0.3` will change the position (after homing and sending `G1 S2 X0 Y0 Z0 F60`) such that the X arm is 0.1 degree lower, Y is 0.2 degree lower, and Z is 0.3 degree higher.

After making any changes to the M679 or M666 values,  home the printer and send  G1 S2 X0 Y0 Z0 F60 again to see the effect of the changes.

## Test some simple X, Y and Z moves

You can now try out some real moves to make sure that everything is functioning correctly.

## Adjust Z=0 position

If you find that the Z=0 position is too high, increase the H parameter in the M669 command by the amount of the error. If it is too low, reduce the H parameter.

## Experimental: auto-calibration

Similar to auto-calibration on a linear delta, auto-calibration on rotary delta is supported from RRF 3.3b2. You will need a Z probe; follow the auto calibration advice on the linear delta page: [Calibrating a delta printer](/User_manual/Tuning/Delta_calibration) but note the following differences:

### Setting up the bed.g file

The bed.g file defines the points that will be probed, and the S parameter in the last [G30](/User_manual/Reference/Gcodes/G30) command defines number of factors that will be calibrated. RepRapFirmware supports the following calibration schemes:

* 3-factor (S3): Adjust the 3 arm endstop position offsets  (M666 XYZ parameters).
* 4-factor (S4): As S3 plus adjustment of the bearings height (the bearings are assumed to be all at the same height) (M666 XYZ parameters and M669 H parameter)
* 5-factor (S4): As S4 plus delta radius (M666 XYZ parameters and M669 H and R parameter)
* 7-factor (S7): As S5 plus X and Y bearing position adjustments (M666 XYZ parameters and M669 H, R, X and Y parameters)