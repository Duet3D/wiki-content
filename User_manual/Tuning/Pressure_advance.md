---
title: Pressure advance
description: Pressure advance aims to compensate for the elasticity of the filament and the extruder system.
published: true
date: 2025-04-01T16:41:35.745Z
tags: 
editor: markdown
dateCreated: 2021-12-03T16:23:22.545Z
---

# What is pressure advance?

Pressure advance aims to compensate for the elasticity of the filament and the extruder system. There are at least three sources of elasticity:

* The filament in the Bowden tube behaves as if it is compressible, because its diameter is typically 0.25mm smaller than the inside diameter of the tube. When it is under tension, it will take the most direct path that the tube allows. When it is under compression, it will snake from side to side, so the length of filament in the tube will be greater.
* The filament itself is slightly compressible.
* In order to produce torque, the angle of the rotor of a stepper motor must lag the angle commanded by the current in the coils. The more the filament resists being fed, the greater the lag angle. So the motor itself is slightly "springy".

These factors cause under-extrusion whenever the extrusion rate is increasing, for example at the start of a straight line when the nozzle has to accelerate from zero or near-zero speed. This is because some of the filament fed at the start of the move is used to counter the elasticity and build up the pressure. Similarly, you get over-extrusion when the rate of extrusion decreases, for example when the nozzle slows down at the end of a straight line. This is because the pressure in the Bowden tube continues to push filament through the nozzle even after the extruder drive slows down or stops.

Pressure advance compensates for elasticity by feeding additional filament through the extruder drive when the extrusion speed is increasing, and feeding less filament through the extruder when the extrusion speed is decreasing, This may result in filament actually being retracted during the last part of the deceleration phase of a move.

Mathematically, it works like this:

`actual_extrusion_speed = requested_extrusion_speed + (K * current_extruder_acceleration)`

The constant K is the amount of pressure advance you configure.

# How to enable and configure pressure advance

Pressure advance is configured on a per-extruder-drive basis. Different extruder drives can have different amounts of pressure advance. To enable pressure advance on an extruder drive, use the following form of command:

```
M572 D0 S0.1
```

The D parameter is the extruder drive number, and the S parameter is the amount of pressure advance you want for that extruder drive. To set the same S parameter for multiple extruders, list each extruders D value, separated by colons:

```
M572 D0:1 S0.1
```

To assign different values to different extruders, you need to use separate M572 commands.

# How much pressure advance to use?

The amount of pressure advance required will vary based on numerous factors that affect the elasticity of the extrusion system such as the distance between hotend and extruder gears, the type of material, the print speed and temperature, jerk and acceleration values. The predominant factor being the length of bowden tube between the extruder and hotend. A very long bowden tube (1m) may require S values approaching 1.0 or even 2.0 in some cases. Whereas a direct drive extruder like a Titan Aero has good results with a value of ~0.05.

A good starting point for tuning for PLA is as follows:

* Direct Drive: S0.025 and up
* Short Bowden (200mm): S0.1 and up
* Medium Bowden (400mm): S0.3 and up
* Long Bowden (600mm): S0.5 and up
* Very long bowden (800mm+): S0.7 and up

These are only starting points, your particular setup may require more or less. The only way to know for sure is an actual print test. Different materials may behave very differently.

# Methods of finding the right amount of pressure advance

> **Note**: When enabling and configuring pressure advance, the extruder acceleration (M201 E parameter) has to be limited to the allowable instantaneous speed change of the extruder (aka jerk, M566 E parameter) divided by pressure advance (M572 S parameter).
For example, if a machine used jerk 300 at a PA of 0.02 to 0.03, maximum extruder acceleration would be 300 / 0.02 = 10,000 to 300 / 0.03 = 15,000
{.is-info}


See the set of macros developed by Duet3D stalwart [Phaedrux](https://forum.duet3d.com/user/phaedrux) in [this forum thread](https://forum.duet3d.com/topic/6181/)

Another option for finding your ideal PA value is to use a customizable python script developed by a forum user that generates a gcode file with varying amounts of PA, which is described in [this forum thread](https://forum.duet3d.com/topic/6698/)

Another option is to use the Marlin K factor script and modifying it as described in [this forum thread](https://forum.duet3d.com/topic/15131/)

Another option still is this detailed method described in [this forum thread](https://forum.duet3d.com/topic/14525/)

## Simplest Method Possible

Alternatively, the following method is proven to work, but requires you use post processing or manually edit the gcode file.

The most simple method of tuning pressure advance is to slice a 100mm cube, 1 bottom layer, 1 or 2 perimeters, and minimal or no infill. Ensure cooling is adequate for printing an object with very short layer times. Ensure the Z seam is aligned to a single point on the middle of a face. Use a print speed close to your normal print speed. Ensure your extruder steps per mm has been calibrated. Ensure any wipe and coasting settings are disabled in the slicer.

Now start the print with pressure advance disabled. After a few layers enable pressure advance by sending the M572 command with your desired starting amount in the gcode console. You may notice a brief pause in movement while the value changes. Continue the print for a few more layers. Try various amounts above or below the starting value and pay attention to the Z seam. Too little PA may result in a bulge at the end of the perimeter before the layer change. Too much PA may result in a gap at the seam.

If you use PrusaSlicer, you can use the before layer change gcode found in the Printer Tab > Custom Gcode, to automate the pressure advance value change.

```
; 0.2mm layer, 8 bands 5mm each, total height 40mm
{if layer_num== 1}M572 D0 S0.0
{elsif layer_num== 25}M572 D0 S0.05
{elsif layer_num== 50}M572 D0 S0.10
{elsif layer_num== 75}M572 D0 S0.15
{elsif layer_num== 100}M572 D0 S0.20
{elsif layer_num== 125}M572 D0 S0.25
{elsif layer_num== 150}M572 D0 S0.30
{elsif layer_num== 175}M572 D0 S0.35
{endif}
```

For Simplify3D, you can put the code below into the post processing commands found at Edit process settings > Scripts, then scroll down to find "Additional commands for post processing"

```
; 0.2mm layer, 8 bands 5mm each, total height 40mm
{REPLACE "; layer 25" "M572 D0 S0.0\n; layer 25"}
{REPLACE "; layer 50" "M572 D0 S0.05\n; layer 50"}
{REPLACE "; layer 75" "M572 D0 S0.10\n; layer 75"}
{REPLACE "; layer 100" "M572 D0 S0.15\n; layer 100"}
{REPLACE "; layer 125" "M572 D0 S0.20\n; layer 125"}
{REPLACE "; layer 150" "M572 D0 S0.25\n; layer 150"}
{REPLACE "; layer 175" "M572 D0 S0.30\n; layer 175"}
{REPLACE "; layer 200" "M572 D0 S0.35\n; layer 200"}
```

This script will insert a new M572 value every 25 layers. You can customize it by changing the S value at the end.

## Interpreting the Results

![pressure_advance_01.jpg](/manual/configuration/pressure_advance_01.jpg =400x) ![pressure_advance_02.jpg](/manual/configuration/pressure_advance_02.jpg =400x)

These images show the results of the various PA values. Choose the value where the seam is most even and the corners do not bulge. If there is a gap at the seam or corners, the PA value is too high.

Once you choose a range that looks best, you can repeat the test and expand the values within the range. In this example case, the best value was 0.15. A repeat test could test all the values between 0.1 and 0.2, and so on. If you have a very long bowden tube, you may need to test a fairly wide range of values and may need to run multiple tests.

Once you have a good value, do some other test prints of more detailed models, like a Benchy just to verify that there are no other odd issues.

# Side effects of enabling pressure advance

If you use a large amount of pressure advance then the extruder may retract filament at the end of a move. If that move is followed by a travel move with retraction of the filament, then the total amount of retraction may be too great for your hot end. Therefore you should consider reducing retraction when you enable pressure advance.

When the extruder acceleration changes, the extruder velocity has to change instantaneously. If the configured extruder jerk is too low to permit this, the extruder acceleration will be restricted so as to honour the maximum configured extruder jerk, and this may in turn limit the printing acceleration. So if enabling pressure advance slows down printing, this may be a sign that the allowed extruder jerk configured in the M566 command is too low. This may prove problematic for remote direct drive extruders like the Zesty Nimble which use a high gear ratio and low jerk values.

You may notice that when using pressure advance there may be a gap between the perimeters and the solid infill lines. To combat this you may need to adjust the infill overlap value in the slicer.

If you configure [Input Shaping](/User_manual/Tuning/Input_shaping), you will need to retune your Pressure Advance. It is recommend to first tune Input Shaping, then Pressure Advance, then Retraction.