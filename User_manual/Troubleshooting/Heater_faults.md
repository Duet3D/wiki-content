---
title: Heater faults and how to avoid them
description: RepRapFirmware monitors heater temperatures to check they are behaving as expected, to detect situations that might pose a danger. Sometimes, the firmware may mistakenly think there is a heater fault in certain situations. 
published: true
date: 2022-09-30T17:27:07.636Z
tags: 
editor: markdown
dateCreated: 2021-12-05T22:56:18.764Z
---

# Introduction

Starting at firmware version 1.15, RepRapFirmware monitors the heater temperatures to check that they are behaving as expected. The purpose is to detect situations that might pose a fire hazard, such as a thermistor becoming detached mechanically from the heater block or bed, or a heater cartridge falling out. The downside of having this protection is that the firmware may mistakenly think there is a heater fault in certain situations such as excessive electrical noise picked up by a temperature sensor.

# Summary

There are a range of reasons why a heater fault could occur however in all these cases the heater is shut down and a heater fault is raised:
- **Case 1**: The heater or temperature sensor becomes detached from the hotend heater block/bed/chamber heater unit.
- **Case 2**: The heater model, including its environment, is not correct or something changes so the heater model is not correct. For example, the heater model is not tuned, the print cooling fan blows on the heater block, or a chamber heater is used with the chamber doors open instead of closed.
  - in both Case 1 and Case 2 A temperature reading is present, that is not an obvious short or open circuit error, but it deviates from what the temperature should be according to the heater model. The amount and length of time that the deviation is tolerated is set by M570, the default is 15C and 5s. This should be tuned to the heater type, for example the hotend temperature should closely track the set temperature, but the chamber temperature may not be able to closely track the set temperature (for example the doors get opened).
- **Case 3**: The heater or temperature sensor wiring breaks/becomes disconnected.
  - This manifests as an out-of-spec reading, frequently an open circuit/short circuit reading. After ~2s the heater is shut down and a fault is raised. From RRF 3.5.0b1 the 2s setting will be able to be set by the user using M570 Rn. the default will be reduced to 1s.
- **Case 4**: The temperature sensor is subject to excessive noise or ESD events.
  -  Depending on the impact of the noise this could look like Case 1/3 or a Case 2 and will be handled according to those cases. The impact of noise, especially on thermocouple sensors, is one of the reasons for requiring 6 out of spec readings (~2s) in RRF 3.4.2 and earlier.
- **Case 5**: The heater exceeds a preconfigured limit. For example, setting a heater to 289C with a 290C limit configured, would be likely to trigger the 290C limit as the temperature would go slightly past 290C, depending on the heater model parameters.
  - This is configurable with M143. This limit also prevents setting the temperature of a heater to higher than it is configured.

# Symptoms of a heater fault

* The web interface will show the heater status as "fault"
* PanelDue will show the current heater temperature with a purple background.
* An error message will be generated describing the reason for the fault. Go to the Console page of DuetWebControl or PanelDue to see the message.
* The heater will stop heating, and cannot be commanded on again until you reset the heater fault.

# Event system

How heater faults are handled after the heater is shutdown changed in RRF 3.4.0 with the introduction of the [event system](/User_manual/RepRapFirmware/Events):

* RRF3.4.0 and later: a heater fault is raised through the event system so heater-fault.g is run, or if not present then the default action is taken (the print is paused and the user is alerted with a message box. 
* Prior to RRF 3.4.0: The print is paused the user is alerted with a message box. If the message box is not acknowledge after a period of the(configurable with M570) a full power off of the machine is attempted.

# Heater Fault Types

## Temperature sensor faults

A mis-wired or failing temperature sensor can present as an out-of-spec reading, frequently an open circuit/short circuit reading. For example if the temperature sensor wire is breaking in a cable chain. 

RRF allows and ignores a small number^1^ of out-of-spec readings (including apparent open and short circuit readings) from temperature sensors before it registers an error, shuts down the heater and raises a heater fault. This is managed by the heater control task on the board that the heater is connected to.

After the immediate action of raising a fault and shutting down the heater, what happens next depends of the firmware version (see event system section above).

*Notes:*

 1) RRF prior to 3.5.0b1: Out-of-spec readings raise a heater fault in approximately 2s (6 readings), irrespective of M570 settings. RRF 3.5.0b1 and later: the M570 Rn command can be used to set the number of consecutive out-of-spec readings allowed before a fault is raised. The default is 3 readings which guarantees that a fault will be raised within one second of a sensor becoming disconnected or shorted. Using R0 will result in a heater fault being raised immediately when a sensor delivers an out-of-spec reading, but will make the system more likely to report spurious failures if the sensor or its wiring is subjected to electrical interference or ESD.
 
2) Multiple, short, out-of-spec readings of less than the limit referenced in point 1 are accumulated by the heater model and will also cause a fault if frequent enough. How many short out-of-spec readings in a given period of time cause a fault depends on the specific parameters of the heater model.

3) If the error is a partial short/open circuit that indicates as a higher temperature than M143 limits but not as an open or short circuit then this protection could apply, however the heater model protection would also apply as this partial short/open circuit would still be anomalous. Fully open/short circuits over the time limit will always cause a fault regardless of M143 settings. 


## Faults when heating up

The message in this case is "temperature rising much more slowly than the expected x.xC/sec". This fault occurs if the rate of temperature increase is less than 75% of the value expected from the heater model defined by [M307](/User_manual/Reference/Gcodes/M307) and this condition persists for more than 5 seconds by default. In firmware version 1.15d and later an additional margin of 0.25C/sec is allowed. To avoid these faults:

* Run tuning on that heater if you have not already done so, and copy the resulting values into a [M307](/User_manual/Reference/Gcodes/M307) command in config.g. See [Tuning the heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning).
* If the expected temperature rise in the message is 0.0C/sec then check if the heater is heating at all.
* Check that there is not an excessive amount of noise in the temperature reading. The temperature graph shown in DuetWebControl should be smooth.
* If all else fails you can use the [M570](/User_manual/Reference/Gcodes/M570) command to extend the allowed temperature excursion and/or the fault trigger time.

## Faults when maintaining temperature

The message in this case is "temperature excursion too large" or "temperature excursion exceeded XX.XC". This fault occurs if the temperature came to within 2.5C of the setpoint temperature, but subsequently departed from the setpoint temperature by more than 15C for more than 5 seconds (defaults). To avoid these faults:

* Check that the heater maintains a stable temperature with no large excursions. If you are using manual PID parameters, you may need to change them. Or you can let auto tuning set the PID settings for you.
* A very strong print cooling fan may cause the nozzle temperature to drop suddenly, either when it turns on at the end of the first layer, or subsequently if the print deflects the air on to the nozzle heater block. A drop of 10C is likely to cause extrusion difficulties as well as heater faults. Make sure that the print cooling fan is directed at the print, not at the heater block. Try insulating the heater block with a silicone sleeve or Kapton tape, and/or use a lower fan speed in your [M106](/User_manual/Reference/Gcodes/M106) settings. When running heater tuning, if you tune the tool, rather than the heater, then the impact of the print cooling fan associated with that tool will be taken into account. See [M303 Tnnn](/User_manual/Reference/Gcodes/M303) command.
* Check that there is not an excessive amount of noise in the temperature reading. The temperature graph shown in DuetWebControl should be smooth.
* If all else fails you can use the [M570](/User_manual/Reference/Gcodes/M570) command to extend the allowed temperature excursion and/or the fault trigger time.

## Faults when tuning

[M303](/User_manual/Reference/Gcodes/M303) is used to tune heaters. You may receive the message "Auto tune cancelled because temperature is not increasing" while running M303. This happens when the temperature has not increased by at least 3C within 30 seconds + the configured dead time of the heater. It can occur on a bed heater with a large thermal mass and a weak heater. To avoid these faults:

* Ensure the heater is actually working and does start to heat up when turned on.
* You can temporarily increase the configured dead time for that heater. For example, sending `M307 H0 D60` before sending the M303 tuning command will increase the heater 0 dead time to 60 seconds.

# How to reset a heater fault without restarting the Duet

Do not simply reset a heater fault without understanding why it occured.

[M562](/User_manual/Reference/Gcodes/M562) resets temperature faults. Send `M562 P#` where # is the heater number. In recent firmware version you can send M562 with no parameters to reset all heaters that are in the fault state.

In DWC you can click on the heater with a fault and it will promt you with a warning message, to reset the fault.



# Firmware configured temperature limits

The [M143](/User_manual/Reference/Gcodes/M143) gcode limits temp to a preconfigured limit. A Heater Fault error occurs if temp is above this limit. Set it in config.g.

The default is 290C.