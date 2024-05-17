---
title: Tuning the heater temperature control
description: 
published: true
date: 2024-05-17T12:18:26.795Z
tags: 
editor: markdown
dateCreated: 2021-09-22T13:50:06.140Z
---

# Introduction

This wiki page gives guidance on:
* how the heating control operates
* Setting PID values in [M307](/User_manual/Reference/Gcodes/M307){target=_blank} (heating process parameters)
  * automatically using [M303](/User_manual/Reference/Gcodes/M303){target=_blank} to auto tune hot end and bed heaters
  * manually
* Troubleshooting advice
* Heater feedforward using [M309](/User_manual/Reference/Gcodes/M309)
* Setting legacy heater PID parameters with [M301](/User_manual/Reference/Gcodes/M301)

# Scope

**This document is relevant to:** all Duet boards

**Firmware versions:** RepRapFirmware 1.15 and later

# How the heating control operates

Each heating controller in RepRapFirmware 1.15 and later can work either in bang-bang mode or in PID mode. When operating in PID mode, it can get its PID parameters in either of two ways:

* Use two sets of PID parameters computed from a first-order + delay time (FOPDT) model of the heating system. The FOPDT model parameters may be derived using auto-tuning, or configured manually using M307.

* Use one set of legacy PID parameters configured manually using [M301](/User_manual/Reference/Gcodes/M301){target=_blank}. This provides backwards-compatibility with previous versions, and provides a fallback if the behaviour of your heating system cannot be approximated well enough by a FOPDT model.

PID parameters are set in config.g manually, or in config_override.g when saved with M500. If there are no heater definitions or parameters in config.g:

* In RepRapFirmware 3.x, no heaters are defined by default; set up the temperature sensor and heater with M308 and M950 respectively, then set PID parameters with M307.

* In RepRapFirmware 2.x and earlier, the bed heater controller defaults to bang-bang mode and the extruder heaters use the default PID parameters. 

If you use the M301 command to set the legacy PID parameters, the controller for that heater switches over to use them. If you run a successful auto tune or you set the model parameters manually using the M307, the controller switches back to using model-derived PID parameters.

To see which set of parameters is being used, run M307 H# where H is the heater number, and that will tell you either that the model is in use (so model-based PID parameters are being used for that heater), or not in use (so legacy PID parameters are being used).

Each heating controller performs temperature monitoring to try to detect fault conditions such as a heater or temperature sensor falling out of an extruder heating block. The temperature monitor relies on the model parameters to decide what is reasonable behaviour. Therefore, even if you decide to use legacy PID parameters or bang-bang control, you should still define an approximate model of the heater to provide good heater monitoring without false alarms.

# Setting the model parameters by auto tuning

## Warnings

> **Warning!** During auto tuning there is minimal protection against heating faults. Therefore you should not leave the printer unattended during auto tuning.
{.is-warning}

> **Warning!** Tuning will always overshoot the target temperature. You need to be aware of this when choosing the target temperature. The heater itself is switched off when the target temperature is reached, so the overshoot is purely due to excess thermal mass in the heater and poor coupling to the thermistor.
{.is-warning}

## Running auto-tuning

## Tabs {.tabset}

### RepRapFirmware v3.2 and later

#### Overview

RepRapFirmware 3.2 introduced a new heater tuning algorithm. This algorithm is more accurate than the old one (especially in measuring the dead time), often completes more quickly than the old algorithm, and is more portable to expansion and tool board firmware (auto-tuning of heaters connected to Duet 3 expansion and tool boards is implemented in RRF 3.3). 

Auto tuning is initiated by the [M303](/User_manual/Reference/Gcodes/M303) command, eg:

```
M303 H1 S240 ; auto tune heater 1, default PWM (100%), 240C target

M303 ; report the auto-tune status or last result
```

#### Tuning tools

RRF 3.2 also allows for tuning a tool, rather than just a heater, which tunes the heater with related fans both off and on. The purpose of this is to allow the heater control to implement feedforward, which monitors fan PWM changes and adjusts the heater power, before the PID algorithm recognises that something has changed.

```
M303 T0 S205 ; tune the primary heater of tool 0
```

#### Notes

* The new algorithm allows for the setting of the ambient temperature (A parameter) for tuning a heater that has been on and has not cooled down to ambient temperature. The tuning cycle hysteresis (Y parameter) and Fan PWM (F parameter) can also be set.

* When tuning, auto tuning will perform a number of phases (heating, settling, fan off, fan on), cycling between the target temperature and 5C below it. It measures the heating and cooling rates on the last 3C each cycle, and accumulates the mean and standard deviation. Typically it will cycle heating/cooling 8 times, but can be as many as 30, if the readings are not consistent (noisy thermistor, or a large thermal mass). You can use the Y parameter to alter the hysteresis.

* If you already have working values for M307 from using the old versions of autotune, there is no particular need to rerun this new version of auto tune (though it may improve temperature stability). Just use your existing M307 values.

* Before commencing auto tuning, the heater to be tuned **must** be at or near room temperature and its temperature reading should be stable. The exception to this is if you use the A parameter in M303.

* When tuning a hot end heater, move the nozzle close to the bed, i.e. within 1mm. The tuning will then compensate for the effect of the hot end fans (hot end cooling fan, and part cooling fan if tuning the heater as a tool).


### RepRapFirmware v1.18 to v3.1.1

The S parameter is the temperature to heat up to. The default value depends on which heater you are tuning. During auto tuning, the heater will be run at the specified power until this temperature is reached and then it will be switched off; so the temperature will overshoot the target somewhat

There is also an optional P parameter, which is the PWM value to use. Unless your heater is greatly over-powered, use the default PWM value of 1.0.

## Notes - all firmware versions

* Only one heater may be auto tuned at a time.

* Send M303 with an H parameter (and optionally P and S parameters) to start the auto tune process. A message will be generated when auto tuning is completed or abandoned. You can also run M303 with no parameters to see the current status of auto tuning.

* If auto tuning is successful, new model parameters are set (but not saved) and the PID parameters computed from them are used. You can see these parameters by running the M307 H# command, where # is the heater number. M307 will also indicate that the model is in use, meaning that the PID parameters displayed by M307 are used, not the PID parameters displayed by M301.

* Tuning a hot end heater typically takes between five and ten minutes. Tuning a bed heater may take more than half an hour, depending on the thermal capacity of the bed. Tuning can be cancelled with an emergency stop.

# Making new heater parameters persistent

After you have run auto tuning and checked that the heater control is working well, save the heater parameters. There are two ways to do this:

* If you have a M501 command at or near the end of config.g to run the config-override.g file, run M500 to save the heater parameters in config-override.g.
* Otherwise, copy the M307 command that was suggested when auto tuning completed into config.g, replacing any existing M307 command for the same heater.

Make sure there is no M301 command for the same heater after the M307 command, otherwise the model will not be used.

# Troubleshooting

## Error message

**"Error: Heater 0 fault: temperature rising much more slowly than the expected 1.7°C/sec"** (reported by RRF 3.x)

In RRF3 there is no longer a default heater model assumption used for the bed. Therefore, you must perform a PID auto tuning to give the firmware a model for bed heater performance. Failure to do so will result in an error message that the heater temperature is not rising fast enough and therefore triggering thermal protection. Running the PID auto tune for the bed and saving the results should resolve the error.

## Auto tune fails

If auto tuning fails with a message that the temperature is not rising fast enough, this indicates that either you are using too low a P value in the M303 command, or the dead time in the existing model is much too low for your heater. You can increase the dead time using the M307 command. For example, sending M307 H0 D30 will increase the dead time to 30 seconds. The actual dead time will be known after a successful auto tuning.

## Temperature faults

If the firmware detects a temperature anomaly, it will turn off the corresponding heater and put it in the Fault state. This can be seen in DuetWebControl, which will show the status of that heater as "fault" instead of "active", "standby" or "off".

You can reset a heater that is in the fault state using the command M562 P# where # is the heater number.

When a heater goes into the fault state, an error message is generated giving the reason. You can view the message in the Console page of DuetWebControl or PanelDue. The most common reasons are:

* Temperature reading was too low for several consecutive readings, suggesting that the thermistor has become disconnected
* Temperature reading was above the limit set by M143 for several consecutive readings
* A thermocouple or PT100 interface chip reported an error for several consecutive readings
* During initial heating, the temperature was not rising as fast as expected. This can occur if you have a slow or low-powered heater and you have not tuned the heater model, so that your heater has a greater dead time or a lower gain than the default model
* The heater reached the target temperature, but the temperature subsequently varied by more than +/-10C. This can occur if you are using unsuitable PID parameters, or if you turn on a very strong print cooling fan that cools the nozzle excessively.


# Setting the model parameters manually

Model parameters can be changed and reported using the [M307](/User_manual/Reference/Gcodes/M307){target=_blank} command. There have been some changes to M307 over the various versions of RepRapFirmware.

# Tabs {.tabset}

## RepRapFirmware v3.4 and later

### Examples

```
M307 H1 R2.186 K0.17:0.11 D5.67 S1.00 V24.0 ; set the process parameters for heater 1
M307 H1 ; report model parameters for heater 1, and whether the model is being used
```

### Parameters

* **H parameter**: specifies the heater.
* **R parameter**: heating rate in degC/sec at full power when the heater temperature is close to ambient.
* **K (and E, if needed) parameter**: The heater model in RepRapFirmware 3.4 and later includes non-Newtonian cooling to predict the variation of cooling rate with temperature and the maximum temperature that would be reached at continuous full power. This is represented by the E and K parameters in M307. (Replaces C parameter in RRF 3.2 and 3.3)
* **D parameter**: dead time, which is the delay between a change in PWM and an appreciable effect on the rate of temperature change.
* **S parameter**: used to limit the PWM, for example S0.8 will limit the PWM to 80%.
* **B parameter**: To use bang-bang mode instead of PID, change B0 to B1. In bang-bang mode, the S parameter is still used to limit the PWM when the heater is turned on.
* **V parameter**: VIN supply voltage at which the R parameter was calibrated.

### Measuring the heating rate (M307 R parameter)

Turn the heater on from cold, wait a few seconds for the temperature to start rising, then time how long it takes for the temperature to rise by a further amount e.g. 10C (for a slow bed heater you might wish to use a smaller amount e.g. 5C). Divide that temperature rise by the time in seconds to get the heating rate.

### Measuring the rate of cooling (M307 K and E parameter)

The K parameter is the rate of cooling in degC/sec when the heater is turned off and the temperature is falling through 100C above ambient temperature. It should be possible to measure this for a hot end. The K parameter is calculated as:
`K = ( temperature change / time in seconds ) / (( heater temperature - ambient temperature ) / 100 )^E parameter`
For example, if the ambient temperature is 20C, set the heater temperature to 140C. Then turn the heater off, and time how long it takes the heater to cool from 130C to 110C. This gives an average heater temperature of 120C, or 100C over ambient, so the second part of the formula cancels itself out. Let's say it takes 54 seconds:
`K= (20/54)/((120-20)/100)^1.35 = 0.370/(100/100)^1.35 = 0.370/1 = 0.370`

The K parameter can take a second value to allow RRF to calculate the heater cooling rate with the cooling fan on. Do as above, with the cooling fan on at 100%. The cooling time should be faster, depending on how well insulated your hot end is. Then multiply the resulting cooling value by the fan PWM, eg:
`K[fan] = ( temperature change / time in seconds ) / (( heater temperature - ambient temperature ) / 100)^E parameter * F (fan PWM in the range 0 to 1)`
The second K value is the *additional* cooling rate due to the fan running at full PWM. So, for example, if the fan cooling rate = 0.5, the actual K parameters would be `K0.370:0.130`, where 0.5-0.370 = 0.130.

For a bed or chamber that can't reach 100C above ambient, you can measure the rate of temperature drop when falling through 50C above ambient, then multiply by 2 to the power of the E parameter. 

The E parameter should not normally need to be measured, just use the default of 1.35. M303 does not currently tune it.

### Measuring the dead time (M307 D parameter)

Turn the heater on from cold and time how long it takes for an obvious change to the temperature. For a hot end this would be a change of a couple of degrees before it increases more rapidly. For a heated bed or chamber this could be a consistent 0.1C increase above the cold temperature. The D parameter will generally be a couple of seconds on a hot end, but longer (eg 10 seconds) on a bed and potentially much longer on a chamber.

## RepRapFirmware v3.2 and 3.3

### Examples

```
M307 H1 R2.186 C202.1:155.0 D5.67 S1.00 V24.0 ; set the process parameters for heater 1
M307 H1 ; report model parameters for heater 1, and whether the model is being used
```

### Parameters

* **H parameter**: specifies the heater.
* **R parameter**: heating rate in degC/sec at full power when the heater temperature is close to ambient.
* **C parameter**: time constant. This may be thought of as the time taken for the temperature rise to reach 63.2% of its ultimate value after the heater is switched on from cold at constant PWM, less the dead time.
* **D parameter**: dead time, which is the delay between a change in PWM and an appreciable effect on the rate of temperature change.
* **S parameter**: used to limit the PWM, for example S0.8 will limit the PWM to 80%.
* **B parameter**: To use bang-bang mode instead of PID, change B0 to B1. In bang-bang mode, the S parameter is still used to limit the PWM when the heater is turned on.
* **V parameter**: VIN supply voltage at which the R parameter was calibrated.

### Measuring the heating rate (M307 R parameter)

Turn the heater on from cold, wait a few seconds for the temperature to start rising, then time how long it takes for the temperature to rise by a further amount e.g. 10C (for a slow bed heater you might wish to use a smaller amount e.g. 5C). Divide that temperature rise by the time in seconds to get the heating rate.

### Measuring the cooling time constant (M307 C parameter)

With the heater hot at a steady temperature Tstart, calculate the following target temperature:

`Ttarget = Tstart * 0.37 + Tambient * 0.63`

Turn the heater off and time how many seconds it takes for the temperature to drop to Ttarget. That is the time constant.

### Measuring the dead time (M307 D parameter)

Turn the heater on from cold and time how long it takes for an obvious change to the temperature. For a hot end this would be a change of a couple of degrees before it increases more rapidly. For a heated bed or chamber this could be a consistent 0.1C increase above the cold temperature. The D parameter will generally be a couple of seconds on a hot end, but longer (eg 10 seconds) on a bed and potentially much longer on a chamber.

## RepRapFirmware v3.1 and earlier

### Examples

```
M307 H1 A350 C139 D5.5 B0 ; set model parameters for heater 1 and use PID mode
M307 H1 ; report model parameters for heater 1, and whether the model is being used
```

### Parameters

* **H parameter**: specifies the heater.
* **A parameter**: gain, which is the ultimate temperature rise divided by the PWM fraction. For example, a gain of 350 means that at a constant 50% PWM, the temperature would eventually reach ambient temperature plus 350 * 0.5 degC.
* **C parameter**: time constant. This may be thought of as the time taken for the temperature rise to reach 63.2% of its ultimate value after the heater is switched on from cold at constant PWM, less the dead time.
* **D parameter**: dead time, which is the delay between a change in PWM and an appreciable effect on the rate of temperature change.
* **S parameter**: used to limit the PWM, for example S0.8 will limit the PWM to 80%.
* **B parameter**: To use bang-bang mode instead of PID, change B0 to B1. In bang-bang mode, the S parameter is still used to limit the PWM when the heater is turned on.
* **V parameter**: VIN supply voltage at which the R parameter was calibrated.

### Measuring the gain (M307 A parameter)

* If necessary, use M307 to set some parameters that let you achieve a steady temperature. The default ones may work.
* Set the heater to a temperature you typically use and wait until the temperature is stable. It doesn't matter if it is still creeping up slowly, but it must not be oscillating (if it oscillates, increase the M307 D parameter).
* Send M573 P# (where # is the heater number) to report the average PWM.
* The gain is: (actual_heater_temperature - ambient_temperature)/average_pwm.

### Measuring the cooling time constant (M307 C parameter)

With the heater hot at a steady temperature Tstart, calculate the following target temperature:

`Ttarget = Tstart * 0.37 + Tambient * 0.63`

Turn the heater off and time how many seconds it takes for the temperature to drop to Ttarget. That is the time constant.

### Measuring the dead time (M307 D parameter)

Turn the heater on from cold and time how long it takes for an obvious change to the temperature. For a hot end this would be a change of a couple of degrees before it increases more rapidly. For a heated bed or chamber this could be a consistent 0.1C increase above the cold temperature. The D parameter will generally be a couple of seconds on a hot end, but longer (eg 10 seconds) on a bed and potentially much longer on a chamber.

# Manual adjustments to the heater model parameters

If necessary you can make manual adjustments to the M307 model parameters, as follows:

* If during initial heating the temperature undershoots the target:
  * RRF 3.3 and later: increase the R parameter by 5% or 10%
  * RRF 3.2 and earlier: reduce the A parameter by 5% or 10%
* If during initial heating the temperature overshoots the target:
  * RRF 3.3 and later: reduce the R parameter by 5% or 10%
  * RRF 3.2 and earlier: increase the A parameter by 5% or 10%
* Adjusting dead time (M307 D parameter):
  * If the response to temperature changes caused externally (e.g. the print cooling fan turning on) is too slow, reduce the D parameter. 
  * If the temperature is not stable but oscillates around the target temperature even when the print head is stationary, increase the D parameter. 
  * In both cases, try increasing/decreasing it by 30%.

# Heater feedforward

The purpose of heater feedforward is to better maintain an even nozzle temperature in high flow rate extruders, by anticipating the additional heat needed when the flow rate increases, instead of waiting for the temperature to drop before power is increased. 

[M309](/User_manual/Reference/Gcodes/M309) is used to set or report heater feedforward, and is supported in RepRapFirmware v3.4 and later.

### Parameters

* **Pn** Tool number
* **Saaa:bbb:ccc...** Feedforward coefficients. The number of coefficients provided must equal the number of heaters configured for the tool when it was created (see M563).

### Notes

* If the P parameter is not provided, the current tool is assumed. If the S parameter is not provided, the existing coefficients are reported.
* The units of S are PWM fraction (on a scale of 0 to 1) per mm/sec of filament forward movement.
* This feature is intended for high flow hot ends or pellet extruders. It's not needed on regular hot ends with a 0.4mm or similar size nozzle where the temperature drop caused by extrusion is less than 1C.

### Calibration

![tuning_heaters_feedforward_01.png](/manual/configuration/tuning_heaters_feedforward_01.png)

* Heat the nozzle and let the temperature stabilise. 
* While monitoring the temperature, extrude filament at close to the maximum rate for 20 seconds (as fast as the extruder can reasonable manage without skipping), then stop for another 20 seconds.
* Without feedforward there is a temperature drop shortly after extrusion starts, which recovers after several seconds. Then when extrusion stops there is a temperature rise, which again reverts to normal temperature after a few seconds.
* If there is an initial drop, then increase the feedforward, and retest. 
* What you are aiming for is either the temperature remaining steady, or falling by a small amount followed by an increase above target temperature of a similar amount, before returning to target temperature. When extrusion stops the reverse will happen; either the temperature will remain steady, or will rise a small amount, followed by falling below target temperature by a similar amount, and then return to target.

To calculate the **S** parameter, a sensible place to start is to increase heater PWM by 10% (0.1 in the scale 0 to 1), divided by the feedrate. If your maximum feedrate is 10mm/sec, 0.1 / 10 = 0.01. In this example, the following setting would be used:
```
M309 P0 S0.01 ; Heater feedforward for tool 0
```

In tests with 1.75mm PETG, we found 0.01 was about the right amount. 2.85mm filament will need more than 1.75mm, and high power heaters will need less than low power heaters. 

# Setting legacy PID parameters

This mode is intended as a backup, for use if model-based tuning is not working well enough. The parameters are configured using the M301 command. Example:

`M301 H1 P10 I0.2 D50 T0.3`

* The H parameter is the heater number. Usually, 0 is the bed heater, 1 is the extruder 0 heater, and so on.
* P, I and D are the standard proportional. integral and differential coefficients, scaled by 255 for compatibility with older firmware. A negative P value means use bang-bang control.
* Previous firmwares also had B, S, T and W parameters in the M301 command. These are no longer used.

Even when using legacy PID parameters, the heater model set by M307 is still used by firmware to detect whether the heater is behaving as expected and to raise a heater fault if not.