---
title: Bed levelling using multiple independent Z motors
description: 
published: true
date: 2024-10-28T14:37:35.581Z
tags: 
editor: markdown
dateCreated: 2021-10-28T14:47:18.994Z
---

# Introduction

On a printer which uses two or more Z motors to raise/lower the bed or gantry, you can have the firmware probe the bed and adjust the motors individually to eliminate tilt. This feature is implemented in RepRapFirmware version 1.19 and later.

# Prerequisites

* You must have a bed or gantry that is raised/lowered by two, three or four leadscrews or similar, where each one is driven by its own Z motor
* You must have a bed probe that can probe at points near the Z leadscrews.
* Each Z motor must be driven from a separate stepper motor output on the Duet. This may require connecting an additional stepper driver or expansion board, eg Duet 3 mainboard + Duet 3 Expansion 3HC, Duet 2 Wifi/Ethernet + DueX expansion board, or Maestro + expansion board.
* As usual with this arrangement, you must use [M584](/User_manual/Reference/Gcodes/M584) to assign the 2, 3 or 4 drivers to the Z axis
* Note that on Duet 2, the Z driver has a dual socket wired in series for powering two z motors at once. Only one of the ports can be used for independent motor leveling. Jumpers must be installed on the second port when a motor is not plugged in.
* You must use the [M671](/User_manual/Reference/Gcodes/M671) command to define the X and Y coordinates of the leadscrews. The M671 command must come after the M584 command and must specify the same number of X and Y coordinates as the number of motors assigned to the Z axis in the M584 command; **and these coordinates must be in the same order as the driver numbers of the associated motors in the M584 command**. The M671 command must also come after any M667 or M669 command.
* You can optionally specify a maximum correction per leadscrew in the M671 command (S parameter). If you don't, it defaults to 1mm.
* You must set up a bed.g file in the usual way with at least as many probe points as Z motors. You can have more if you wish.
* The value of the S parameter on the final G30 command in bed.g must equal the number of Z motors.

# How it works

A valid [M671](/User_manual/Reference/Gcodes/M671) command enables auto calibration of the Z motors. When you run [G32](/User_manual/Reference/Gcodes/G32) to perform bed probing, the final G30 command (the one with the S parameter) in bed.g will then cause auto calibration to be performed. The auto calibration uses a least squares algorithm that minimises the sum of the height errors. The deviation before and expected deviation after calibration is reported.

If you have two Z motors, the calibration process adjusts the bed tilt along a line between the two leadscrew positions, on the assumption that the tilt at right angles to this will not change. With three motors, it adjusts the tilt in both directions. With four motors, it adjusts the tilt in both directions and also the twist. You should not use 4 leadscrews with a bed that is rigid enough to resist twisting strongly.

You can run G32 again to repeat the process if you wish.

If your bed is not perfectly flat or the gantry sags a little when the head is over the middle of the bed, the process will cause a small shift in the Z=0 position. To correct this, if you are using the Z probe to do Z homing, you can just re-home Z at the end of your bed.g file.

# Recommendations

* If you have 3 or 4 Z motors, in bed.g use at least one probe point close to each leadscrew.
* If you have just 2 Z motors, one at each end of the X axis, then set the Y coordinates of the leadscrews in the M671 command to be equal (the value doesn't matter, so you can use zero). Use at least two probe points, one at each end of the X axis. All your probe points should have the same Y coordinate, which should be at or near the middle of the printable range.
* Note: If you are experiencing the Z axis compensating in the opposite direction needed, that means your Z motors are swapped. You can either swap the X values in the M671 command, or swap the stepper motors plugged into the Duet.

# Examples

## Tabs {.tabset}

### For 2 motors

File config.g:

```
...
M584 X0 Y1 Z2:4 E3; two Z motors connected to driver 2 (left motor) and driver 4 (right motor) 
M671 X-20:220 Y0:0 S0.5 ; position of leadscrew/bed pivot point at left and right of X axis
M208 X-5:205 Y0:200 ; X carriage moves from -5 to 205, Y bed goes from 0 to 200
...
```

File bed.g:

```
G28 ; home
G30 P0 X20 Y100 Z-99999 ; probe near a leadscrew, half way along Y axis
G30 P1 X180 Y100 Z-99999 S2 ; probe near a leadscrew and calibrate 2 motors
```

In RRF 3.x, if you want the gantry levelling to repeat until deviation gets below a certain amount, up to a specific number of times, you could write the bed.g macro as:

```
G28 ; home
while true
    G30 P0 X20 Y100 Z-99999 ; probe near a leadscrew, half way along Y axis
    G30 P1 X180 Y100 Z-99999 S2 ; probe near a leadscrew and calibrate 2 motors
    if abs(move.calibration.initial.deviation) < 0.01 || iterations > 3
        break
```
Adjust iterations and the absolute deviation as desired.

### For 3 motors

File config.g:

```
...
M584 X0 Y1 Z2:5:6 E4; three Z motors connected to driver 2 (front left), driver 5 (rear middle) and driver 6 (front right)
M671 X-15:100:215 Y-10:190:-10 S0.5 ; position of leadscrew/bed pivot point at front left, rear middle and front right
M208 X-5:205 Y0:200 ; X carriage moves from -5 to 205, Y bed goes from 0 to 200
...
```

File bed.g:

```
G28 ; home
G30 P0 X20 Y20 Z-99999 ; probe near a leadscrew
G30 P1 X100 Y180 Z-99999 ; probe near a leadscrew
G30 P2 X180 Y20 Z-99999 S3 ; probe near a leadscrew and calibrate 3 motors
```

In RRF 3.x, if you want the gantry levelling to repeat until deviation gets below a certain amount, up to a specific number of times, you could write the bed.g macro as:

```
G28 ; home
while true
    G30 P0 X20 Y20 Z-99999 ; probe near a leadscrew
    G30 P1 X100 Y180 Z-99999 ; probe near a leadscrew
    G30 P2 X180 Y20 Z-99999 S3 ; probe near a leadscrew and calibrate 3 motors
    if abs(move.calibration.initial.deviation) < 0.01 || iterations > 3
        break
```

Adjust iterations and the absolute deviation as desired.


### For 4 motors

File config.g:

```
...
M584 X0 Y1 Z2:3:4:5 E6; four Z motors connected to driver 2 (front left), driver 3 (rear left), driver 4 (rear right) and driver 5 (front right)
M671 X-15:-15:215:215 Y-10:190:190:-10 S0.5 ; position of leadscrew/bed pivot point at front left, rear left, rear right and front right
M208 X-5:205 Y0:200 ; X carriage moves from -5 to 205, Y bed goes from 0 to 200
...
```

File bed.g:

```
G28 ; home
G30 P0 X20 Y20 Z-99999 ; probe near a leadscrew
G30 P1 X20 Y180 Z-99999 ; probe near a leadscrew
G30 P2 X180 Y180 Z-99999 ; probe near a leadscrew
G30 P3 X180 Y20 Z-99999 S4 ; probe near a leadscrew and calibrate 4 motors
```

In RRF 3.x, if you want the gantry levelling to repeat until deviation gets below a certain amount, up to a specific number of times, you could write the bed.g macro as:

```
G28 ; home
while true
    G30 P0 X20 Y20 Z-99999 ; probe near a leadscrew
    G30 P1 X20 Y180 Z-99999 ; probe near a leadscrew
    G30 P2 X180 Y180 Z-99999 ; probe near a leadscrew
    G30 P3 X180 Y20 Z-99999 S4 ; probe near a leadscrew and calibrate 4 motors
    if abs(move.calibration.initial.deviation) < 0.01 || iterations > 3
        break
```

Adjust iterations and the absolute deviation as desired.

## Note on M584 from examples above

VERY IMPORTANT! Assigning a drive using M584 does not remove its old assignment. Therefore, if you assign a drive that defaults to being an extruder drive, you should also assign the extruder drives explicitly as in the above example. Failure to do so may result in unexpected behaviour. 

See Gcode wiki entry for [M584](/User_manual/Reference/Gcodes/M584).

# Axis levelling using endstops

If you have an axis with more than one motor (doesn't have to be Z, can be X, Y or any other multi-motor axis), with each motor connected to a separate motor driver on the Duet, and each motor has an associated endstop, you can home each motor individually to level the axis.

## Tabs {.tabset}

### In RepRapFirmware 3.x

In RepRapFirmware 3, you do not need to split the axes to level them, as you do in RRF2. The firmware understands if you define the axis endstops correctly. For example, if you have a dual motor Z axis, with endstops at the axis minimum, define the Z endstop in config.g as follows:

```
M574 Z1 S1 P"zstop+e1stop" ; configure active-high endstops for low end on Z via pins zstop and e1stop
```

Make sure that the two endstops are defined in the same order in the M574 command  as the corresponding Z motors in your M584 command. For the example above, M584 Z2:4 would be correct, assuming one Z motor is plugged into driver 2 (Z driver on Duet 2) and its endstop is plugged into zstop, while the second Z motor is plugged into driver 4 (E1 driver on Duet 2) and its endstop is plugged into the E1 endstop pins.

When homing, RRF3 will home both axes, and stop each motor when each endstop is triggered. Note that DWC and M119 will only show one endstop per axis, but will show the axis endstop as triggered if either switch is triggered. So you can test them by triggering one at a time.

### In RepRapFirmware 2.x

See below for an example of Z axis levelling in RepRapFirmware 2.x, on Duet 2. This can be applied to other axes, if necessary.

**NOTE:** use G1 H# moves in RRF 2.02 and 1.23 or later, and G1 S# moves in earlier RRF versions.

#### Step 1: Connect the motors on Z and E0
See the wiring diagram for your Duet board for motor connections.
* Connect one motor to the Z motor header, and one motor to the E0 motor header.
* Don't forget to put back the 2 jumpers on the ZB socket if you had the second Z motor plugged into it it before.
* Don't do any connection with the power on, these are current drivers.
* Connect the endstops to their connectors. Make sure the endstop connected to Z is the one closest to the Z motor, and the one connected to E0 is closer to the E0 motor.

#### Step 2: Edit your homeall.g 

* Edit it first to avoid forgetting.

```
; Relative positioning
G91

; Lift Z without endstop protection
G1 H2 Z2 F6000

; Course home X and Y
G1 H1 X-215 Y-215 F4200

; Move away from the endstops
G1 X5 Y5 F6000

; Fine home X and Y
G1 H1 X-215 Y-215 F360

M98 P"homez.g"
```

#### Step 3: Edit your config.g 

* The M584 line is critical, it's combining the axes. The P3 parameter only shows 3 axes to hide the U axis.
* Because we'll split the axes during the homing we have to configure everything for U: acceleration, endstops, etc.
* Don't forget to configure the new motor too, by adding Unnn values to M350, M92, M566, M203, M201 and M906.
```
M208 X0 Y0 Z0 U0 S1 ; Set axis minima
M208 X210 Y210 Z200 U200 S0 ; Set axis maxima

;DUAL Z
M584 X0 Y1 Z2:3 U3 E4 P3

; Endstops
M574 X1 Y1 Z1 U1 S0 ; Set active low endstops
M558 P0 X0 Y0 Z0 H2 F60 T6000 ; Set Z probe type to switch, the axes for which it is used and the dive height + speeds
G31 P600 X0 Y0 Z0 ; Set Z probe trigger value, offset and trigger height
M557 X15:195 Y15:195 S20 ; Define mesh grid

; Drives
M569 P0 S1 ; Drive 0 goes forwards
M569 P1 S0 ; Drive 1 goes backwards
M569 P2 S1 ; Drive 2 goes forwards
M569 P3 S1 ; Drive 3 goes forwards
M569 P4 S1 ; Drive 4 goes forwards

M350 X64 Y64 Z64 U64 E16:16 I1 ; Configure microstepping with interpolation
M92 X320 Y320 Z1600 U1600 E92.6; Set steps per mm

M566 X600 Y600 Z12:12 U12 E300:300 ; Set maximum instantaneous speed changes (mm/min)
M203 X30000 Y30000 Z1200 U1200 E1800:1800 ; Set maximum speeds (mm/min)
M201 X2500 Y2500 Z500 U500 E3000:3000 ; Set accelerations (mm/s^2)
M906 X1000 Y1000 Z800 U800 E1000:1000 I50 ; Set motor currents (mA) and motor idle factor in per cent
M84 S2 ; Set idle timeout
```

#### Step 4: Edit your homez.g

* The first M584 splits the axes into independent Z and U. It also displays U (P4) because otherwise the controller doesn't move it.
* After splitting, we move Z and U simultaneously and at the same speed towards their respective home switch.
* The second M584 re-combines the axes as Z and hides U (P3)
```
; Lift Z relatively to current position
G91
G1 H2 Z2 F6000

; split Z motor control to Z and U
; for it to work we have to show U (param P4) in the UI
M584 Z2 U3 P4

; Move Z and U  down until the switches triggers
G1 H1 Z-205 U-205 F1000

; back to combined axes and hidden U
M584 Z2:3 P3

; Back to absolute positioning
G90

; Tell the firmware where we are
G92 Z0

; lift Z after probing, without endstop protection
G91
G1 H2 Z10 F1000
G90
```