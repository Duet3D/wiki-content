---
title: Calibrating your Duet-based machine
description: 
published: true
date: 2022-08-12T11:26:08.795Z
tags: 
editor: markdown
dateCreated: 2022-05-30T12:13:26.620Z
---

# Introduction

> **PLEASE NOTE THIS GUIDE IS A WORK IN PROGRESS!**
> Please post any queries or comments for improvement on the [Duet3D forum](https://forum.duet3d.com/){target=_blank}. 
{.is-warning}


This guide covers the initial calibration of a 3D printer to ensure reliable print quality. It covers tuning the heaters, bed leveling, calibrating the extruder, and finding the optimal settings for the slicer.

While this guide uses the example of calibrating an Ender 3, the procedures shown here are the same for any printer during initial calibration.

# 1. PID tuning heaters

## Hot end heater

* Heater tuning allows RepRapFirmware to find the ideal parameters for the heater model, allowing for quick heat up times, and stable temperatures, with little overshoot.
* For full details, see: [Tuning the heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning){target=_blank}
* Ensure the test is started with the heaters at room temperature. Ideally this should be done with no filament loaded. For most accurate results, move the nozzle so it is within 1mm of the bed surface, and set the part cooling fan speed to match normal print fan speeds. For the Ender 3 and PLA that would be 75-100%
* Next send `M303 H1 S210` to start the heater tuning process for the hot end. Wait for the process to complete. It should only take a few minutes. Save the results by sending `M500` so that they are loaded automatically at startup.
* If you followed the configuration guide, automatic loading of the config-override file should be enabled. If you get an error message saying otherwise, add `M501` to the end of config.g
* Keep in mind that the stock Ender 3 Pro has a PTFE tube in the hot end which should not be exposed to temperatures above 240c due to the foul smell released and could eventually damage the tube.
* PID tuning should be redone whenever a major modification is done to the hot end or bed heater, such as installing or removing a silicone heater block sock, or changing the part cooling fan, or even if the ambient temperature of the room the printer is in changes from season to season.

## Bed heater

* Next tune the bed heater by sending `M303 H0 S60`. Wait for the process to complete, and once again, save the results by sending `M500`. Note that bed heater may take considerably longer due to increased heat up and cool down time.
* The temperatures chosen reflect the most common temperatures used for printing PLA on the Ender 3. If you plan to print with other filaments that require hotter temperatures, you can re-tune with those. However, the results of the tuning at these lower temperatures will still be valid over a fairly wide range of temperatures.
* The heated bed uses a magnetic surface which should not be exposed to temperatures above 80c due to the damage it can cause the magnets.
* PID tuning should be redone whenever a major modification is done to the hot end or bed heater, such as installing or removing a silicone heater block sock, or changing the part cooling fan, or even if the ambient temperature of the room the printer is in changes from season to season.

## Other heaters

* eg chamber heaters

# 2. Leveling the Bed 

## First Pass, Cold

* Every successful print starts with a good first layer. There are two factors that go into a good first layer. A clean and level print surface, and an accurate extrusion flow rate. We will address flow rate in the following tuning guide. But first we must mechanically level the bed using the 4 adjustment screws on the bed.
* This can be done with the printer turned off or on. It can be easier when powered off and you can easily move the X and Y axis by hand. Just make sure not to move them too quickly.
* Start by turning all four leveling screws so that the spring is compressed and the bed is farthest away from the nozzle.
* Now with the Z axis at the lowest point of travel move the X axis all the way to the left and the Y axis so that the bed is all the way at the front. The nozzle should be at the back-left corner.
* Turn the leveling knob at the back-left corner until the bed raises up and just touches the nozzle. You can use a piece of paper to judge this, or just your eyeball.
* Now move to all 4 corners and do the same so that the nozzle is just touching the bed at all 4 corners.
* Repeat this step a few times so that the tension on the paper under the nozzle feels about the same so it can just start to slide.
* After the heaters are tuned, we will do another leveling pass with everything up to temp to allow for warping and expansion.

## Second Pass, Hot

* With the heaters are tuned, we can go through a second leveling attempt with the hotend and bed up to printing temperature, which will allow for warping and expansion to occur as it would during a print.
* With filament unloaded, bring the hotend and the bed heater up to typical printing temperatures. 200c / 60c. Allow the heaters to reach temp and wait for 5 minutes or so for it to stabilize and saturate.
* Since we already got the bed mostly level with the first pass, this one is mainly to account for any change in shape due to being heated.
* Follow the same procedure as in step 1 until all 4 corners are gripping the paper equally.
* Later in the guide we will go through using mesh compensation to verify the flatness of your bed and mechanically account for any warp or skew.
* A later step in this guide will go through a test print to check the surface of the bed for warping and leveling, but first we must calibrate the extruder.

## Bed plane levelling

* Insert wisdom here.

* Running the [G32](/User_manual/Reference/Gcodes/G32){target=_blank} command causes the firmware to execute macro file /sys/bed.g. This macro normally uses G30 commands to define the probe points individually, including an optional Z probe trigger height adjustment for each point, and probe the bed, then:
  * on a Delta printer, perform auto calibration. See [Calibrating a Delta printer](/User_manual/Tuning/Delta_calibration){target=_blank}
  * on a Cartesian or CoreXY printer, perform bed plane levelling by moving the Z leadscrews independently. See [Bed levelling using multiple independent Z motors](/User_manual/Connecting_hardware/Z_probe_auto_levelling){target=_blank}
  * display the manual corrections needed to the bed levelling screws. See [Using the Manual Bed Levelling Assistant](/User_manual/Connecting_hardware/Z_probe_manual_levelling){target=_blank}

## Mesh bed compensation

* Mesh bed compensation allows the printer to adjust the nozzle height during printing to compensate for an uneven bed or for sag in the gantry. 
* [G29](/User_manual/Reference/Gcodes/G29){target=_blank} probing uses a grid of points. [M557](/User_manual/Reference/Gcodes/M557){target=_blank} defines the size and spacing between points. It is only used for mesh bed compensation.
  * On a Delta printer, you can if you wish use G32 to perform [auto calibration](/User_manual/Tuning/Delta_calibration){target=_blank} followed by G29 to compensate for any residual bed height errors. 
  * On a Cartesian or CoreXY printer you can use G32 bed probing for [Bed levelling using multiple independent Z motors](/User_manual/Connecting_hardware/Z_probe_auto_levelling){target=_blank}, followed by G29 probing to do bed compensation.
* For full details, see [Using mesh bed compensation](/User_manual/Connecting_hardware/Z_probe_mesh_bed){target=_blank}

# 3. Calibrating Extruder E Steps per MM

* An accurate print requires the accurate flow of plastic from the extruder. This is controlled by the number of motor steps it takes to move 1mm of filament through the extruder. The default value for the Ender 3 is 93, (or 744 if you've chosen x128 microsteps as mentioned in the config guide). This procedure will test the accuracy of this value.
* You will need a ruler or caliper, marker, filament, and calculator.
* Overview of the procedure: 
  * Mark a set length on the filament from the extruder body
  * Command a set distance of extrusion
  * Measure the resulting movement. 
  * The difference between requested and measured distance will inform the change to the steps per mm.
* Your bed should be leveled and your heaters tuned before proceeding. See the earlier steps.

## Load and mark filament

* First we need to load some filament. PLA will work fine.
* Set the hotend temp to the high end of the temperature range for your chosen filament.
* Or, if you choose to cold extrude, separate the extruder motor from the hot end, and enter M564 H0 to allow the extruder to move while cold.
* Load the filament and push it all the way down until it starts to come out the nozzle.
* Take your ruler or caliper and your marker.
* Measure 110mm from the body of the extruder where the filament enters out along the length of the filament.
* It can be tricky to hold the filament straight and line up the ruler accurately.
* Mark the filament as close to the 110mm point as possible.

## Extrude 100mm of filament

* Now that the hotend is up to temp and the filament is marked at 110mm we can command an extrusion.
* Go to the Gcode console and send the following command to extrude 100mm of filament at a slow speed.
* `G1 E100 F60`
* Wait for the extruder to slowly push the filament through. This will take about 100 seconds.

## Measure the Distance to the Mark

* When the extruder motor has stopped moving, measure the distance from the extruder body to the mark on the filament.
* On our first pass we ended up measuring 14.28mm.

## Calculate the New E Step Value

* Use the following formula to derive the new E steps per mm value.
* Old_E_Steps * (100 / (110 - Distance_To_Mark)) = New_E_Steps
* The old e steps value was 93 and the distance to the mark was 14.28mm
* 93*(100/(110-14.28)) = 97.2.
* Our new E step value is 97.2.

## Set the New E Step Value

* We can use `M92 E97.2` in the gcode console to temporarily set the new e steps value.
* You may need to repeat this procedure a few times to get it dialed in. Due to slight variations with each pass the end value may be slightly different, but it should start to coalesce around a stable value.
* After 5 attempts we get: • Starting value: 93 • 1st pass: 97.2 • 2nd pass: 96.3 • 3rd pass: 96.2 • 4th pass: 96.5 • 5th pass: 96.8
* Since the value seems to be fairly consistent we will use the average value of 96.5.
* To make the change permanent, edit config.g (Go to Settings > System Editor > Right click on Config.g > Edit) and change the M92 E parameter to 96.5, eg `M92 E96.5`
* The E step value will depend on a few factors such as the diameter of the filament used to measure, the tension of the spring pinching the filament on the extruder gear, etc.
* Since each filament will be slightly different, it makes more sense to set the e steps once and use the slicer extrusion factor separately to fine tune it for each filament.
* The procedure for tuning the slicer extrusion factor will be shown later in the guide.

# 4. Find Maximum Extrusion Rate

* Now that we have the correct amount of filament coming through the nozzle, it will be very useful to know the maximum extrusion rate that your extruder and hot end combo can provide. This will define one of the limits on how fast you can reliably print.
* You can find the max flow rate for your hot end using a simple test and a formula. Then you can use those results in another formula to determine viable combinations of layer height, extrusion width, and print speed.
* Max Flow Rate = Max Input Feed rate * pi * (Filament Diameter/2)^2^
* To find the Max Input Feed rate, bring your hot end to the temp you'd normally wish to use for that material and start extruding some plastic. Start at 1mm/s and extrude 50mm, then increase it by 1mm/s and extrude 50mm again.
* Repeat this until you can see or hear the extruder start to skip steps. Back off the speed by 0.5mm/s until it stops clicking and try to extrude 100mm of filament at that speed. If it can keep up, you've found your max flow rate.
* On the Ender 3 Pro, using the PLA that came with the printer and heating up to 220c, I was able to extruder 50mm @ 6mm/s ok, but 50mm @ 7mm/s skipped. 100mm @ 6mm/s also skipped. 200mm @ 5mm/s worked fine, so 5mm/s will be the max feed rate.
* Max Flow Rate = 5mm/s * 3.14 * (1.67mm/2)^2^ = 10.9 mm^3^/s
* So, for the sake of simplicity and to add a bit of safety factor, we will say that the stock Ender 3 Pro extruder and hot end combo can reliably extrude 10 cubic mm per second. At least for this PLA at this temperature. Every material will be slightly different, but this is a good starting point and should be more or less applicable.

## Video Example of Extruder Skipping

* The extruder skips when trying to extruder PLA at 210c at 6mm/s
* Reducing the speed to 5mm/s allows it to extrude 50mm of filament successfully.
* Raising the temperature can also be used to increase max extruder throughput, but at the expense or overhang and bridging performance.

## Volumetric Limit in Relation to Speed

* Now that we have established the volumetric limit for the hotend, we can ensure that our combination of speed, extrusion width, and layer height, do not exceed this limit.
* First we should determine what layer height and extrusion width we will use. The Ender 3 comes with a 0.4mm nozzle, so the optimal combination would be 0.4mm extrusion width and 0.2 layer height, just to keep it simple for the purpose of this guide.
* Next we can determine the maximum print speed we can use while still staying within the volumetric limit of 10mm^3^/s using the formula Max Print Speed = Volumetric Limit / ( Layer Height * Extrusion Width)
* Max Print Speed = 10/(0.2*0.4) = 125mm/s So now we have a speed limit for that combination.
* Note that this doesn’t take into consideration the ideal print speed for the printer mechanics or the ability to cool the extruded plastic fast enough.
* Note that if we increase the extrusion width or layer height, the speed limit decreases. For example with 0.3 layer height and 0.5 extrusion width: Max Print Speed = 10/(0.3*0.5) = 66mm/s
* Keep these limits in mind when choosing slicer settings.

## Fine Tuning Slicer Extrusion Factor

* We initially tuned the extruder flow by setting the rough E steps per mm value. Now we can fine tune the extrusion rate in the slicer for each filament type we wish to use.
* Each slicer calls it something slightly different. Slic3r calls it extrusion multiplier in the filament properties tab. Cura calls it Flow rate %.
* For best results, you will need to make slight adjustments to it for each type of plastic and even sometimes between different rolls of the same brand and type. If your E steps are correct and was measured with filament similar in diameter, a value of 100% or 1.0 may be correct.
* The first step is to provide the slicer with an accurate dimension for your filament. The nominal diameter for typical filament is 1.75mm, but in actual practice, it can vary quite a bit. To accommodate this, the slicer allows you to specify a more accurate measurement to use in its calculations.
* To do this, use a caliper to measure your filament diameter in several places over a few meters worth and in at least 2 orientations rotated by 90 degrees (in case it's oval and they all are). Use the average value as your filament diameter in your slicer. 1.68 to 1.72 has been the most common diameter in my experience.
* Usually measuring the start of a new roll is enough, but sometimes it can vary throughout the roll, so it may be beneficial to measure before each print.
* Next, we will use an actual test print to help us dial in the extrusion multiplier.

## Extrusion Factor Test Print (Method 1)

* Download this [calibration cube](https://www.thingiverse.com/thing:3670991){target=_blank} created specifically for this test from Thingiverse.
* Using the slicer of your choice, slice the cube with the following settings: 0.2 layer height, 2 perimeters, 0% infill, 0 top layers, 0 bottom layers, 30mm/s inner and outer perimeter speed, 100% flow rate/extrusion factor, extrusion width = nozzle width. Use a wide brim as well to ensure adhesion.
* Let the print progress for several layers to allow any first layer inaccuracies dissipate.
* When the print reaches 10mm z height carefully check for the separation of the 2 walls. Ideally, the walls should be fused together with no gap between them. Under extrusion will show as a gap between the walls, and over extrusion will show as messy walls or an uneven, rough surface.
* Use the extrusion factor slider in the DWC to lower the extrusion factor by 10% and observe the print for a few more layers. Keep reducing it until you can definitely make out a gap between the walls. Then increase it again until you notice the gap disappear.
* Keep adjusting the extrusion factor up and down until you find a value that looks best. You can now modify the slicer extrusion factor to use this value. So if 98% looked best, use 98% (or 0.98) in the slicer. If 105% looked best, use 105% (or 1.05).
* The benefit of this method is that you can get the extrusion factor dialed in with a single print rather than printing it multiple times with slight changes each time.
* You can even use both methods to verify that you're on the right track.

## Extrusion Factor Test Print (Method 2)

* Another method of adjusting the extrusion factor is as follows. You will need a caliper. This method will require printing the part multiple times.
* Using the same STL as the last step, print with the following settings: Vase mode/Spiralize outer contour, 0 bottom layer, 30mm/s wall speed, wide brim, 0.2 layer height, extrusion width = nozzle width.
* Let the print reach 10mm in Z height and cancel the print.
* Use a caliper to measure the center of all 4 walls and take the average.
* Use the following formula to calculate your modified extrusion factor.
* New Extrusion Multiplier = Old Extrusion Multiplier * (Expected Thickness/Measured Thickness)
* For example: New Extrusion Multiplier = 1*(0.4/0.41)=0.975 OR 97.5%
* You can repeat this test a few times to narrow down the multiplier. It may need to be redone for different plastic types, ex: PLA, PETG, ABS, Etc.

# 5. Bed level verification print

* Now that the bed has been manually leveled, and your extrusion factor has been calibrated, it's time to test how flat and level the bed really is.
* This time print the [Leveling Test STL from Thingiverse](https://www.thingiverse.com/thing:3670991){target=_blank}
* Use 0.4 extrusion width and 0.2 first layer height. This will print a series of concentric square lines covering the entire bed. You'll be able to see where the nozzle is too close to the bed or too far from the bed.
* In my case, the bed has a dip in the middle. You can see the gaps between the lines in the center squares, and the joined lines towards the edges of the bed. Adjusting the leveling screws will not be able to fix this issue.
* Some skew can be corrected by adjusting the leveling screws, but a concave or convex shape can only be corrected with Mesh Bed Compensation, and will require a Z probe.
* If a Z probe is not an option, you can work around this by using baby stepping during the first layer, or by increasing the first layer flow rate to extrude a thicker line to fill the gap in the dipped area, and reduce it again for the outer area.
* Another solution is to replace the bed with a flat sheet of glass or aluminum.

# 6. Tuning motion

* A set of macros to help tuning motion can be found on [this forum post](https://forum.duet3d.com/post/56174){target=_blank}
* All of [Teaching Tech's Calibration Guide](https://teachingtechyt.github.io/calibration.html){target=_blank} seems pretty good, too.

## Speeds, jerk and acceleration

Insert wisdom here. (Text below added by Ian from [forum post](https://forum.duet3d.com/post/276309){target=_blank})

* **M203** is maximum speed. See gcode dictionary [M203](/User_manual/Reference/Gcodes/M203). Even if you send a GCode command with a feed rate faster than this, the firmware will limit the speed to this setting.
* **M566** is 'instantaneous speed change', also known as 'jerk'. See gcode dictionary [M566](/User_manual/Reference/Gcodes/M566). If the axis is not moving, then you command movement, it will try to start moving at this speed. All machines will skip steps if you set this too high. Because you try to move the axis at high speed immediately. Usual setting for M566 is between 300 and 900. if you set it too high, it can limit acceleration, and will cause a lot of shake in your machine from violent starts. If your axis is heavy, it will have to be lower. Generally, it is better to set M566 low (eg 300) and set acceleration (M201) higher. This should reduce violent movement of the machine, but still give good speed. **Add note on jerk policy, P parameter.**
* **M201** is acceleration. See gcode dictionary [M201](/User_manual/Reference/Gcodes/M201). Increase this for the axis to achieve high speed quicker. 2000 is quite low. Some machines can reach 20000.

For tuning these three, it is dependent on the machine what can be set, and you will need to experiment to find the best values, for each axis (X, Y and Z). The usual procedure to tune these settings is to:
* Find a value for each that works, eg M566 X300, M201 X2000, M203 X5000
* Change one value, find where there is step loss, eg M201 X10000
* Halve the difference between the values, eg M201 X6000, then test
  * If it works, halve the difference with the higher number, and test again, eg M201 X8000
  * If it doesn't work, halve the difference with the lower number and test, eg M201 X4000
* Repeat using the highest value that works, and lowest value that does not work.
* When you find the highest value that works, eg M201 X5000, set to 80% of this value, eg M201 X4000. This gives you some flexibility.

This technique is called a 'binary search'. You can use it to tune all the settings, for each axis.

## Retraction

* Insert wisdom here.
* [Teaching Tech retraction tuning guide](https://teachingtechyt.github.io/calibration.html#retraction){target=_blank}
* Hardware retraction: [G10](/User_manual/Reference/Gcodes/G10){target=_blank}, [G11](/User_manual/Reference/Gcodes/G11){target=_blank}, [M207](/User_manual/Reference/Gcodes/M207){target=_blank}

## Pressure advance

* Insert wisdom here.
* [M572](/User_manual/Reference/Gcodes/M572){target=_blank}
* See [Tuning pressure advance](/User_manual/Tuning/Pressure_advance){target=_blank}

## Input shaping

* Insert wisdom here.
* [M593](/User_manual/Reference/Gcodes/M593){target=_blank}
* [Connect an accelerometer](/User_manual/Connecting_hardware/Sensors_Accelerometer){target=_blank}
* [Input shaping](/User_manual/Tuning/Input_shaping){target=_blank}
* Keep in mind that you have to retune Pressure Advance after you have configured Input Shaping. The Pressure Advance will differ from shaper to shaper and from frequency to frequency.

# 7. Optimal slicer settings

* Default slicer profiles are available for the Ender 3 Pro and Duet 3 Mini 5+:
* Cura:
* PrusaSlicer:
