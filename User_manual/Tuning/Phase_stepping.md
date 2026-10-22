---
title: Phase Stepping
description: In phase stepping mode, RepRapFirmware drives the motor coils directly
published: true
date: 2026-10-22T10:43:55.040Z
tags: 
editor: markdown
dateCreated: 2026-10-22T10:43:55.040Z
---

# What is phase stepping?

In step and direction mode, RepRapFirmware sends step pulses to the stepper driver. The driver counts the pulses and sets the motor coil currents from its internal microstep table.

In phase stepping mode, RepRapFirmware drives the motor coils directly. At short, fixed intervals it calculates the position that the motor should be at, and writes the coil currents for that position to the driver. The driver does not use step pulses or its microstep table in this mode.

Phase stepping and third-order motion control are different things. Third-order motion control refers to how moves are planned: acceleration changes gradually, instead of changing abruptly at the start and end of each acceleration phase. RepRapFirmware needs phase stepping to use third-order motion control, because it cannot calculate step times for third-order motion quickly enough. Phase stepping does not need third-order motion control, so a driver can use phase stepping with second-order motion. See [Support for third-order motion control](/User_manual/RepRapFirmware/Third_order_motion).

Phase stepping has these advantages:
* It allows third-order motion control to be used on the Duet 3 MB6HC.
* In RRF 3.7.0 and later, [M970.3](/User_manual/Reference/Gcodes/M970_3) can correct the coil current waveform for motor and driver non-linearity, so that the motor position follows the commanded position more closely.
* RepRapFirmware scales the motor current to the speed and acceleration of the move, see [Motor current](#motor-current).

# Supported boards

| Board | Phase stepping |
|:---|:---|
| Duet 3 MB6HC | RRF 3.6.0 and later. In RRF 3.7.0 and later, also when the board runs in expansion mode |
| Duet 3 Expansion 3HC | RRF 3.7.0 and later |
| Duet 3 Expansion 1HCL, M23CL, Duet 3 Tool Board INDX | RRF 3.7.0 and later, when the driver is in open loop mode |
| Duet 3 Mini 5+, Duet 3 MB6XD | Not on their own drivers. In RRF 3.7.0 and later, these boards can configure phase stepping on CAN-connected boards that support it |
| Duet 3 Tool Board 1LC, Duet 3 Roto Toolboard, Duet 2 | Not supported |

An axis or extruder must use only drivers on the main board, or only drivers on CAN-connected boards. M970 rejects an axis with both, with the message "Phase stepping is not supported on axes with both local and remote drivers".

On the Duet 3 Expansion 1HCL, M23CL and Duet 3 Tool Board INDX, M970 is rejected for a driver in closed loop or assisted open loop mode.

# Enabling phase stepping

Use [M970](/User_manual/Reference/Gcodes/M970) to select phase stepping for each axis and extruder. M970 must come after the M584 command that maps the drivers of that axis. In RRF 3.7.0 and later, an M584 command that changes the drivers of an axis returns that axis to step and direction mode.

```
M584 X0 Y1 Z2 E1.0      ; extruder on driver 0 of the expansion board at CAN address 1
M970 X1 Y1 E1           ; use phase stepping for X, Y and the extruder
```

While a driver is phase stepping, stealthChop is off. When phase stepping is disabled, the driver returns to the mode set by [M569](/User_manual/Reference/Gcodes/M569) D. Set the driver mode with M569 before M970. CAN-connected boards reject M569 D for a driver that is phase stepping.

M970 with no parameters reports the step mode of each axis and extruder, for example "Axis step mode - X:1, Y:1, Z:0, E:1". The object model fields `move.axes[].phaseStep` and `move.extruders[].phaseStep` are true for axes and extruders that use phase stepping.

# Motor current

In step and direction mode, the driver uses the current set by [M906](/User_manual/Reference/Gcodes/M906) while the motor moves, and the percentage set by [M917](/User_manual/Reference/Gcodes/M917) while it is standing still. In phase stepping mode, RepRapFirmware calculates the current at every update from the speed and acceleration of the motor:

`current = M906 current × (S + (1 − S) × min((V + A) / 256, 1))`

where:
* S is the M917 standstill percentage divided by 100.
* V = |speed| × Kv × 100, where speed is in full steps per step clock and Kv is set by [M970.1](/User_manual/Reference/Gcodes/M970_1) (default 1000).
* A = |acceleration| × Ka × 10000, where acceleration is in full steps per step clock² and Ka is set by [M970.2](/User_manual/Reference/Gcodes/M970_2) (default 50000).
* The step clock runs at 750 kHz on all Duet 3 boards.

So the current never exceeds the M906 current, and never falls below the M917 standstill current. In units of full steps per second, the speed term alone gives full current at 1920 × 1000 / Kv full steps per second. The acceleration term alone gives full current at 288000 × 50000 / Ka full steps per second².

To convert to mm, divide the steps per mm set by [M92](/User_manual/Reference/Gcodes/M92) by the microstepping set by [M350](/User_manual/Reference/Gcodes/M350). This gives full steps per mm. For example, an axis with 80 steps per mm at x16 microstepping has 5 full steps per mm. With the default Kv and Ka, the speed term alone gives full current at 384 mm/s, and the acceleration term alone gives full current at 57600 mm/s². At 100 mm/s with no acceleration and M917 at 71%, the current is 79% of the M906 current.

Increasing Kv or Ka makes the current reach the M906 value at a lower speed or acceleration. If the current is too low for the load, the motor lags the commanded position and can lose steps. In that case, increase Kv, Ka or the M917 percentage.

# Waveform correction

In RRF 3.7.0 and later, [M970.3](/User_manual/Reference/Gcodes/M970_3) adds a correction to the commanded electrical angle of a motor, to compensate for motor and driver non-linearity. One electrical cycle is 4 full steps. For each harmonic S, with magnitude J and phase O in degrees, the correction is J × sin(S × electrical angle + O). Up to 4 harmonics can be corrected for each driver.

The correction is set for each driver, not for each axis. It is not saved over a reset, so put it in config.g. On the Duet 3 Expansion 1HCL, M23CL and Duet 3 Tool Board INDX, the correction is also applied in closed loop and assisted open loop modes, except while tuning or calibrating.

For a driver in step and direction mode, [M569.2](/User_manual/Reference/Gcodes/M569_2) applies a similar correction to the driver's microstep table. That correction is more restricted: only harmonics that are multiples of 4, with a phase of 0 or 180 degrees, can be represented.

# Limitations

## Stall detection

Stall detection is not used on drivers in phase stepping mode, because the driver does not update its stall status in that mode. In RRF 3.6, stall detection on those drivers uses out of date values and is not reliable.

If an axis uses stall detection homing, select step and direction mode for that axis in the homing file, home it, then restore the previous mode. For example, in homex.g:

```
var xPhaseStep = move.axes[0].phaseStep
M970 X0                                 ; step and direction mode for stall detection homing
; ... stall detection homing moves for X ...
M970 X{var.xPhaseStep ? 1 : 0}          ; restore the previous step mode
```

Homing with endstop switches and Z probes works in phase stepping mode.

## Leadscrew adjustment

In RRF 3.6, leadscrew adjustment moves (G32 with [M671](/User_manual/Reference/Gcodes/M671)) have no effect on Z drivers that use phase stepping. This is fixed in RRF 3.7.0.

## Speed

RepRapFirmware updates the coil currents at a fixed rate, so at high motor speeds the commanded position changes by a larger amount at each update. [M122](/User_manual/Reference/Gcodes/M122) reports the update rate in the line "Phase step loop runtime".

## Third-order motion on CAN-connected boards

In RRF 3.7.0, CAN-connected boards receive second-order motion, even when their drivers use phase stepping. See [Support for third-order motion control](/User_manual/RepRapFirmware/Third_order_motion).
