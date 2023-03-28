---
title: Calibrating a delta printer
description: Delta printers are mechanically simpler than most other types of 3D printer. They also take up very little desk space for a given build volume. However, in order to produce good prints, they need to be accurately built and calibrated.
published: true
date: 2023-03-28T13:17:27.982Z
tags: 
editor: markdown
dateCreated: 2021-12-03T16:16:48.607Z
---

# Introduction

Delta printers are mechanically simpler than most other types of 3D printer. They also take up very little desk space for a given build volume. However, in order to produce good prints, they need to be accurately built and calibrated. There are two stages to this: getting an accurate build, and calibrating the firmware to the printer geometry.

# Make sure your build is accurate

A delta printer needs to be built accurately in all of the following respects. If the geometry is inaccurate, then auto calibration won't work well because auto calibration will adjust the machine parameters  that it has control over to compensate for geometrical errors that it is unaware of and can't compensate for.

In particular, many of these errors will cause the effector to tilt by varying amounts depending on the XY position of the effector. If your Z-probe is offset from the nozzle, then this will cause the apparent trigger height of the Z probe to vary with XY position, making calibration even more difficult. It is possible to correct for varying trigger height using H parameters in the G30 commands in the bed.g file, but this requires measuring the trigger height separately at every probe point, which is tedious. For this reason, on delta printers a Z probe that detects the nozzle contacting the bed is highly recommended.

## Towers must be parallel to each other

* Preferably, use accurately-machined metal corners to connect the towers to the lower and upper frames, not printed corners.
* Make sure that the spacing between each pair of towers is the same at the top, the bottom, and half way between top and bottom.

It is not essential that the spacing between all pairs of towers is identical, because that can be allowed for by calibration.

## Bed should be at right angles to the towers in both X and Y directions

Actually, this isn't essential, because RepRapFirmware can compensate for bed tilt. Unfortunately, auto calibration can't easily distinguish between bed tilt and differences in tower separation. One approach to getting the bed level is to mount it on 3 equal-length spacers attached to the lower horizontal frame. If you are using metal corners, the horizontal frame should be at right angles to the towers. If you do need to make any adjustment, you can stack washers or shims on top of one or two of the spacers.

## All 6 rods must be the same length, measured between bearing centres

If you are building the rods yourself, make them one by one in the same jig. If you are buying them, check that the supplier guarantees a maximum difference in rod length, which should be less than 0.1mm. It is particularly important that the rods in a pair are of matched lengths, because any difference will cause the effector to tilt.

## The spacing between the bearings at the top of a pair of rods must be exactly the same as the spacing between the bearings at the bottom

If you are using Traxxas or similar joints, you can put washers or shims under the screws to make the bearing spacings equal. It is not necessary for all 3 pairs of rods to have the same spacings.

## The joints must have enough angle of movement to allow the nozzle to reach all of the printable area

If they don't, either reduce the printable radius, or use longer rods, or use better joints.

## The two bearings on each carriage must be at the same height

The bearings in each pair on the effector should be at the same height by design. The bearings on the carriage at the other end of the rods must also be at the same height, otherwise the effector will be forced to tilt when it is close to the tower with that carriage on it. Carriages are typically screwed to carriage trucks or linear rail sliders, and you can usually rotate a carriage slightly about the axis passing through its face to get the bearings at the same height before you tighten the screws.

## Carriages must not be rotated or warped about the Z axis

In other words, the two bearings on each carriage must be equal distances from the tower towards the centre of the printer. If not, then different carriages will try to twist the effector by different amounts, which can only be resolved by the effector tilting. Warped carriages can cause this problem. If you are using wheeled carriages, check that you have the same length of washers or spacers between the carriage truck and each wheel.

## There must be no play in the joints

The Bowden tube exerts a sideways force on the effector, and the magnitude and direction of this force vary with the effector position. If there is any play in the joints, this is likely to be taken up in different directions depending on the effector position, which causes effector tilt and other issues. Here are a couple of ways to mitigate effector tilt:

* Put a spring between the parallel rods in a pair, close to the joint(s) having excessive play
* Use elastic to support the Bowden tube from the top frame of the printer, to take its weight and reduce the sideways force

## Checking the accuracy of your build

To check the spacing between towers, use digital calipers. It is not necessary to use calipers large enough to measure the spacing, because we are concerned about differences in spacing rather than absolute accuracy. A pair of 150mm digital calipers can be used to check spacings up to about 390mm by measuring between the head of the calipers and the tip of the spike that comes out of the tail.

All of the issues to do with bearings and rod length cause the effector to tilt by varying amounts as it moves around the XY plane. Mount a bulls-eye spirit level on top of the effector, permanently if there is room or temporarily if not, to make this tilt more visible.

# Setting up your initial delta parameters

You can use the editor in Duet Web Control to edit these commands in config.g, or use the [RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/) to generate the whole config.g file for you.

## M665 command

[M665](/User_manual/Reference/Gcodes/M665) puts RepRapFirmware in delta mode and sets the main geometrical parameters. It has the following format:

M665 Lxxx Rxxx Hxxx Bxxx Xxxx Yxxx Zxxx

L = diagonal rod length, measure between bearing centres. Measure this as accurately as you can.

R = delta radius. This is the horizontal distance spanned by each of the rods when the effector is centred, again measured between bearing centres. You do not need an accurate value for this because calibration will determine it, so use a ruler to estimate it to within about 5mm.

H = height of the nozzle above the bed when all three carriages are just high enough to trigger the endstop switches. Measure it as best you can, to within about 5mm. Calibration will adjust it.

B = printable bed radius, in other words how far from the centre of the the bed the nozzle can reach.

X, Y, Z = tower position corrections. Unless you have an unusual geometry such as a "square" delta, omit them or set them to zero.

## M666 command

[M666](/User_manual/Reference/Gcodes/M666) sets some additional parameters. It has the following format:

M666 Xxxx Yxxx Zxxx Axxx Bxxx

X, Y, Z = endstop switch position corrections. Try to mount your endstop switches so that the heights of the three carriages are within 1mm of each other when the switches trigger.

A = bed tilt in the X direction, in percent (e.g. +1.0 means that as you move 100mm in the +X direction, the bed rises by 1mm).

B = bed tilt in the Y direction, in percent.

You can set all these parameters to zero. Calibration will establish at least the X, Y and Z parameters.

# Auto calibration

RepRapFirmware provides a fast least-squares auto calibration facility. This is how to use it.

## Choosing a Z probe for auto-calibration

Your can run auto calibration without a Z probe if you tell RepRapFirmware that you have no Z probe, by using parameter P0 in your M558 command in config.g. However, auto calibration is easier and faster if you have a Z probe. The choice of a suitable Z probe should take account of the following factors:

* It must work well with your print bed surface, to give a consistent trigger height over the whole of the bed
* If it does not use the nozzle itself as the probe, it must be small enough to be mounted **close to the nozzle**, to minimize the effect that effector tilt has on trigger height
* It must not add appreciable weight to the effector

The most popular types of Z probe for delta printers are:

* [Duet3D Smart Effector](/Duet3D_hardware/Accessories/Smart_Effector). This has a built-in strain gauge to detect contact between the nozzle and the bed.
* [Piezo based sensors](/User_manual/Connecting_hardware/Z_probe_connecting#piezoelectric-sensors)
* Three force sensitive resistors (FSRs) mounted in the bed supports. The nozzle itself is used to push the bed and the FSRs detect the force. The design of the bed supports is critical, because the bed must be constrained not to move sideways yet the weight must be taken by the FSRs; and the FSRs must be protected from the heat of the bed heater. A small piece of electronics is generally used to measure the resistance of the FSRs and provide a trigger signal to the Duet or other electronics.
* [Mini differential IR sensor by dc42](/Duet3D_hardware/Accessories/IR_Probe). It is small enough to mount close to the nozzle, below the heatsink of the popular E3Dv6 hot end. Not suitable for transparent beds with variable or highly-reflective surfaces below them, e.g. borosilicate glass plates with Kapton heaters glued directly to them. Like any Z probe that is offset from the nozzle, if you have effector tilt that varies with the XY position of the effector, this will give rise to a corresponding variation in trigger height.

## Connecting and configuring the Z probe

See [Connecting_a_Z_probe](/User_manual/Connecting_hardware/Z_probe_connecting) for general Z probe configuration. If you don't have a Z probe, use P0 in the M558 command in config.g to tell the firmware that.

## Homing the machine

The default output from the  [RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/) for Delta printers will move the axis down by 5mm from where the end stops are triggered.

<!-- Old text, not accurate
For the purposes of G30 calibration this needs to be disabled / commented out in the  homedelta.g file. G30 makes the assumption that it's starting point is where the end stops are triggered.
-->

```
;G1 Z-5 F6000               ; move down a few mm so that the nozzle can be centred
G90                        ; absolute positioning
;G1 X0 Y0 F6000             ; move X+Y to the centre
```

## Measuring the trigger height

The Z probe trigger height must not vary significantly with XY position. Measure the trigger height of your Z probe at various XY positions and make sure it is consistent. To measure the trigger height:

1. Make sure there is no filament stuck to the nozzle (you may want to do this test with the nozzle hot)
1. Cancel any existing bed compensation by sending M561
1. Send M208 S1 Z-3 to temporarily allow Z moves down to Z=-3mm. This is to ensure that you can lower the nozzle all the way to the bed in step 5.
1. Command the print head to the XY coordinates you want to probe
1. Command the print head down in small steps until the nozzle just touches the bed or just grips a sheet of paper. You may need to send M564 S0 to allow movement lower than where the firmware thinks Z=0 is. You may also wish to create a macro to lower the head by e.g. 0.02mm to get greater accuracy (G91 followed by G1 Z-0.02).
1. Send G92 Z0 to define that height as Z=0
1. Command the nozzle up 5mm (G1 Z5)
1. Send G30 S-1 to probe the bed without resetting the Z=0 position
1. Read off the Z height from the web interface. That is the trigger height.
1. Repeat the previous two steps a couple of times to make sure you are getting a consistent result
1. Repeat steps 4-10 for the other XY positions you want to check

When you have established what the trigger height is, set the Z parameter of the G31 command in config.g to that value.

On a delta printer, it can be quite hard to achieve a consistent trigger height. Z probes that are mounted on the effector are sensitive to any tilting of the effector, because it affects the relative heights of the Z probe and the nozzle. You can reduce this effect by mounting the probe as close to the nozzle as possible. Probing solutions that use the nozzle itself as the probe are usually not affected by effector tilt, but some types are sensitive to the XY position of the nozzle for other reasons.

## Check Repeatability

While it is not required to check the repeatability before moving on to the next step of setting up the bed.g file this step can save hassle later on caused by hardware or assembly issues.

* Position the nozzle about 5mm above the bed, run G30 S-1 to probe, then read off the Z height. Send G1 Z5 and repeat. Do this several times to see how reproducible the trigger height is. At a constant XY position, you are looking for it to be reproducible to within about 2 microsteps at x16 microstepping. That's normally 20 or 25 microns if you are using 1.8deg motors, or half that if you are using 0.9deg motors. I use the IR sensor on PEI and I get consistent trigger height to within 10 microns. If the trigger height is less consistent than that, try a lower probing speed (F parameter in the M558 command). If it still isn't consistent, check whether you have too much friction in your carriage movement and reduce it if necessary. Higher motor current may also help. If that's not the problem, then it's probably down to your Z probe.

* If that test gives a consistent trigger height, try a similar test but move the nozzle to a different XY position and back again before the G30 S-1 command. If the trigger height becomes inconsistent, suspect play or friction in the joints.

## Setting up the bed.g file

The bed.g file defines the points that will be probed, and the number of factors that will be calibrated. RepRapFirmware supports the following calibration schemes:

* 3-factor: Adjusts the endstop switch position corrections (M666 XYZ parameters).
* 4-factor: Similar to traditional manual delta calibration, but much faster. Adjusts the endstop switch position corrections (M666 XYZ parameters) and the delta radius (M665 R parameter).
* 6-factor: as 4-factor but also adjusts the X and Y tower position corrections.
* 7-factor: as 6-factor but also adjusts the diagonal rod length (M665 L parameter).
* 8-factor: as 6-factor but also adjusts the X and Y tilt angles (M666 A and B parameters).
* 9-factor: as 8-factor but also adjusts the diagonal rod length (M665 L parameter).

For typical delta printer builds, 6-factor is best. You should be able to measure the diagonal rod length accurately enough, and for auto calibration to be able to determine it accurately, you need probe points that are well outside the triangle formed by the towers. Likewise, without points well outside that triangle, auto calibration cannot accurately distinguish the effect of bed tilt from misplaced towers.

The number of probe points you choose must be at least as high as the number of factors you want to calibrate, and preferably higher. You can use up to 32. The exact position doesn't matter and the points do not have to be placed symmetrically. You should choose points that cover all areas of the bed. I suggest at least one point close to each tower, one to three points at the edge of the build area between each pair of towers, and one central point. Large printers should have points part way between the centre and the periphery too.

Tools that can help you generate the bed.g file include the [RepRapFirmware configuration tool](https://configtool.reprapfirmware.org/) and [the bed file wizard](http://www.escher3d.com/pages/wizards/wizardbed.php).

## Adding trigger height corrections to the bed.g file

You may wish to omit this section for first-time calibration and return to it later.

Ideally, your Z probe will have exactly the same trigger height at each probe point. In practice it may not:

* If your Z probe is displaced from the nozzle, small amounts of effector tilt will change the relative heights of the Z probe and the nozzle, which changes the effective trigger height
* If you are using sensors in the bed mounts, the force required to activate the sensors may vary depending on whether you are probing close to one of them

We recommend that you measure the trigger height at several of your probe points. If you determine that the trigger height is the same at all positions, that's excellent. If it isn't, then for accurate calibration results you should measure the trigger height at every probe point and add trigger height corrections to bed.g.

To measure the trigger height see "measuring the trigger height section above", repeat  for the other XY positions you want to check

Use the trigger height at the centre of the bed as the reference, and put that value in the Z parameter of your G31 command in config.g. For each of the G31 commands in bed.g except for the one at that centre point, add parameter Hxxx where xxx = (trigger height at that point) - (trigger height at centre point).

## Running auto calibration for the first time

Initially, we recommend that you set the Z probe dive height in your M558 command to a large value, in case of large errors in your M665 R and H parameters or in your endstop trigger heights, that could otherwise cause the nozzle to scrape the bed before probing starts. For example, add parameter H30 to your M558 command in config.g. This will make probing start 30mm above where the firmware thinks the bed ought to be, instead of the default 5mm.

To auto calibrate, simply press the Auto Delta Calibration in Duet Web Control or on PanelDue, or send the G32 command. If you go to the Console page of DWC or PanelDue, you will see a message indicating whether calibration was successful and giving the before and after mean height error and the deviation about the mean.

If you had large errors to begin with, you can further improve the calibration by running it once or twice more. You should see the deviation converge to a stable value.

## Saving the calibration results

When you are happy with the results, save the new calibration parameters. There are two ways of doing this:

* Run M665 and M666 without parameters, then they will display the current parameters as adjusted by auto calibration. Copy the new values into the M665 and M666 commands in your config.g file.
* If you are running firmware 1.17 or later, you can run M500 to save new M665 and M666 commands in the config-override.g file. Then, if you have an M501 command at the end of your config.g file, it will run config-override.g at that point, so that the new commands override the ones earlier in your config.g file.

Finally, set the dive height (H parameter in the M558 command) back to a sensible value such as 3 or 5mm so that probing will be faster.

## Auto calibrating before each print

Unless you are using a very large number of probe points, auto calibration will probably take less than half a minute, and you may wish to auto calibrate at the start of each print. To do this, simply include G32 in your slicer start gcode.

We recommend that you do not home the printer (G28) after auto calibration, because if there is any jitter in the endstop switch trigger positions, auto calibration will compensate for it until you run G28 again. You do need to have homed the printer before you auto calibrate, so you may wish to include G28 at the start of your bed.g file.

## Using mesh bed compensation to handle residual height errors

If your residual height errors after auto calibration are too large for you to get a good first layer, you can use mesh bed compensation (G29) to compensate for them. Running auto calibration clears the G29 height map; therefore if you want to use both auto calibration and mesh bed compensation, you must run auto calibration first.

See [Using mesh bed compensation](/User_manual/Connecting_hardware/Z_probe_mesh_bed) for more details. If you wish, you can include a G29 command to activate mesh bed compensation at the end of your bed.g file.

## Correcting for small X and Y scaling errors

After calibration, if you have small X or Y scaling errors you can correct them using the [M579](/User_manual/Reference/Gcodes/M579) command.

# Troubleshooting with heightmap visualization

This website has a helpful visualization of [common delta problems](http://boim.com/DeltaUtil/CalDoc/Calibration.html).