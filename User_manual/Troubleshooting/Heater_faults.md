---
title: Spurious heater faults and how to avoid them
description: RepRapFirmware monitors heater temperatures to check they are behaving as expected, to detect situations that might pose a danger. Sometimes, the firmware may mistakenly think there is a heater fault in certain situations. 
published: true
date: 2021-12-17T00:29:45.590Z
tags: 
editor: markdown
dateCreated: 2021-12-05T22:56:18.764Z
---

<!-- Migrated not updated! -->

# Introduction

Starting at firmware version 1.15, RepRapFirmware monitors the heater temperatures to check that they are behaving as expected. The purpose is to detect situations that might pose a fire hazard, such as a thermistor becoming detached mechanically from the heater block or bed, or a heater cartridge falling out. The downside of having this protection is that the firmware may mistakenly think there is a heater fault in certain situations.

# Symptoms of a heater fault

* The web interface will show the heater status as "error" instead of "active".
* PanelDue will show the current heater temperature with a purple background.
* An error message will be generated describing the reason for the fault. Go to the Console page of DuetWebControl or PanelDue to see the message.
* The heater will stop heating, and cannot be commanded on again until you reset the heater.

# Faults when heating up

The message in this case is "temperature rising much more slowly than the expected x.xC/sec". This fault occurs if the rate of temperature increase is less than 75% of the value expected from the heater model defined by M307 and this condition persists for more than 5 seconds. In firmware version 1.15d and later an additional margin of 0.25C/sec is allowed. To avoid these faults:

* Run tuning on that heater if you have not already done so, and copy the resulting values into a M307 command in config.g. See [Tuning the heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning).
* If the expected temperature rise in the message is 0.0C/sec or some other very low value, upgrade to version 1.15d or later firmware.
* Check that there is not an excessive amount of noise in the temperature reading. The temperature graph shown in DuetWebControl should be smooth.

# Faults when maintaining temperature

The message in this case is "temperature excursion too large" or "temperature excursion exceeded 10.0C". This fault occurs if the temperature came to within 2.5C of the setpoint temperature, but subsequently departed from the setpoint temperature by more than 10C for more than 5 seconds. To avoid these faults:

* Check that the heater maintains a stable temperature with no large excursions. If you are using manual PID parameters, you may need to change them. Or you can let auto tuning set the PID settings for you.
* A very strong print cooling fan may cause the nozzle temperature to drop suddenly, either when it turns on at the end of the first layer, or subsequently if the print deflects the air on to the nozzle heater block. A drop of 10C is likely to cause extrusion difficulties as well as heater faults. Make sure that the print cooling fan is directed at the print, not at the heater block. Try insulating the heater block with a silicone sleeve or Kapton tape, and/or use a lower fan speed in your M226 settings.
* Check that there is not an excessive amount of noise in the temperature reading. The temperature graph shown in DuetWebControl should be smooth.
* If all else fails, in firmware versions 1.15e and later you can use the [M570](/User_manual/Reference/Gcodes/M570) command to extend the allowed temperature excursion and/or the fault trigger time.

# Faults when tuning

The message in this case is "Auto tune cancelled because temperature is not increasing". This happens when the temperature has not increased by at least 3C within 30 seconds + the configured dead time of the heater. It can occur on a bed heater with a large thermal mass and a weak heater. To avoid these faults:

* If you are using firmware older than 1.18, use a higher P factor during tuning. You can try P1.0 for the bed heater.
* You can also temporarily increase the configured dead time for that heater. For example, sending M307 H0 D60 before sending the M303 tuning command will increase the heater 0 dead time to 60 seconds.

# How to reset a heater fault without restarting the Duet

Send M562 P# where # is the heater number. In recent firmware version you can send M562 with no parameters to reset all heaters that are in the fault state.

# Firmware configured temperature limits

The [M143](/User_manual/Reference/Gcodes/M143) gcode limits temp to at preconfigured limit.  A Heater Fault error if temp is above this limit. Set it in config,g

The defaults depend on the firmware version:

300°C prior to RepRapFirmware version 1.13, and 262°C from 1.13. At RepRapFirmware 1.17 the default maximum temperatures were 262C for extruders and 125C for the bed. In more recent versions the default maximum heater temperature is 290C, to allow the hot end to be tightened at 285C as per the E3D recommendation.