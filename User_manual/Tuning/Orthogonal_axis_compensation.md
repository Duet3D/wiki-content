---
title: Orthogonal axis compensation with M556
description: 
published: true
date: 2023-01-13T23:28:35.986Z
tags: 
editor: markdown
dateCreated: 2022-05-06T14:44:11.234Z
---

# Introduction

RepRapFirmware allows you to compensate for the fact that the X, Y, and Z axes of your machine may not have been assembled at perfect right angles, using Gcode [M556](/User_manual/Reference/Gcodes/M556) for axis skew compensation. Ideally, your machine will be have been built perfectly square. This page tells you how to implement orthogonal axis compensation if you were not able to.

# Original RRF method

The first part of these instructions for calibrating [M556](/User_manual/Reference/Gcodes/M556) is based on the instructions from [RepRap Ltd](https://reprapltd.com/reprappro/documentation/ormerod-2/axis-compensation/index.html#Orthogonal_Axis_Compensation), who kindly allowed us to reproduce the source material here.

## Print the calibration parts

![axis-comp-01.jpg](/manual/axis_compensation/axis-comp-01.jpg =600x){target=_blank}

Download and print the orthogonal axis compensation test pieces, as in the picture above. STL files are available here: [60mm calibration piece](https://github.com/reprappro/RepRapFirmware/blob/master/STL/calibration_60mm.stl){target=_blank} or [90mm calibration piece](https://github.com/reprappro/RepRapFirmware/blob/master/STL/calibration_90mm.stl){target=_blank}. The original 3D model (OpenSCAD) for the test pieces are [here](https://github.com/reprappro/RepRapFirmware/tree/master/STL/Calibration){target=_blank}.

Before you remove the pieces from the bed, use a felt-tipped pen to label the axes of the three-legged piece. In the picture above, X runs left-right, Y runs front-back, and Z runs down-up.

## Setting the compensation from the printed test parts

### Assemble the gauge

Clean any extraneous wisps of filament or small lumps on the surface of the printed parts away.

![axis-comp-02.jpg](/manual/axis_compensation/axis-comp-02.jpg =600x){target=_blank}

**Parts:** 
* 1 x M3 screw, at least 20mm long, threaded all the way up the screw.
* 2 x M3 nuts
* 1 x M3 washers

Optionally:
* 1 x ballpoint pen spring
* 2 x M3 washers

An M3x35mm hex head screw and spring is shown in the pictures for the axis compensation wheel. You don’t need a special screw; use any screw you have available. An M3x20mm cap head screw, or a crosshead screws, should be long enough. It should be threaded all the way up the screw, so you can tighten the thumbwheel in place. The spring is a ball-point pen spring, but isn’t necessary.

Use an M3 screw to draw an M3 nut into the hexagonal cavity in the angled part of the gauge. Put an M3 washer under the head of the screw. Take care as you tighten the screw that the flats on the nut are aligned with the hexagon of the cavity. You will feel the tightening force increase as the nut reaches the bottom of the cavity.

Then assemble the measuring screw. An M3 hex-headed screw fits in the cavity in the top of the thumbwheel, if you have one available. Secure it there with a nut with a washer under it on the other side of the wheel. Then, optionally, put a washer, a spring, and another washer on the screw and screw it into the nut embedded in the cavity of the angled piece as shown above.

The order goes: M3 screw, thumbwheel, washer, nut, (washer, spring, washer, if available) angled piece, nut embedded in the angled piece.

The thumbwheel in the test print has 10 radial indentations to allow you to count turns of it. The thread pitch of an M3 screw is 0.5mm, so each gradation is 0.05mm. Use a felt-tipped pen to mark one indentation so you can identify it as the wheel turns.

### Set the guage zero point

![axis-comp-03.jpg](/manual/axis_compensation/axis-comp-03.jpg =600x){target=_blank}

Now use a set square to set the screw at just the right position for a right-angle, as shown above. Push the set square against the small projection near the angle. But don’t force things or push too hard. You don’t want to distort the pieces.

Note the position of the mark you made on the thumbwheel.

Measure the distance between the tip of the projection near the angled corner and the centre of the end of the screw.  Let’s call the distance d mm.

## Measure the offset

Now take the three-legged test piece that you printed. Hold it in the same place as the set square, and see how much (if at all) you have to turn the thumbwheel to just touch it. Note down the turns, and whether they were clockwise/inward/acute-angle/negative or anti-clockwise/outward/obtuse-angle/positive.

![axis-comp-04.jpg](/manual/axis_compensation/axis-comp-04.jpg =600x){target=_blank}

Suppose you need 1.3 clockwise turns. The pitch of an M3 thread is 0.5mm, so this means that the axis pair you have measured is -0.65mm away from a true right angle.

You can take several readings and average them – always more accurate. If you do, re-zero with the set square before each reading.

Measure all three pairs of axes: XY, YZ and XZ and write down the measurement for each.

## Configuring M556

The format for the M556 command is
`M556 S[d] X[XY] Y[YZ] Z[XZ]`
where [d] is the distance in mm you measured on the guage between the projection and the end of the screw. Suppose d = 75, and the XY, YZ and XZ measurements are XY = -0.65, YZ = 0.9, and XZ = 0.2. Then send:
```
M556 S75 X-0.65 Y0.9 Z0.2
```
You can check the skew compensation used by sending M556, e.g.:
```
M556
Axis compensations - XY: -0.00867, YZ: 0.01200, ZX: 0.00267
```
Note that the skew factor is the measured distance / d, e.g. XY / d = -0.65 / 75 = -0.00867

Your machine will now correct for the angles between the axes when it prints.

## Finishing up

You can put the M556 command in your config.g file. Or you can put it in a short file called, say, setaxes.g and upload that to your Duet's SD card using the web interface. Then, when you run that file, it will set your axis compensation.

To check that the axis compensation has worked, apply it, print the test pieces again, and check them with the set-square and gauge. This time you should find that all three legs of the largest test piece are at right angles to each other.

# Diagonal measurement method

This is a newer method to determine the skew factor. It also requires a number of printed calibration parts, but no extra parts. The following text borrows heavily from the notes in the [Marlin configuration.h](https://github.com/MarlinFirmware/Marlin/blob/7503ac3c67b1521f8d2c822f51b46c97a884a21e/Marlin/Configuration.h#L2161) file.

## Print the calibration parts

Print a test square, e.g. [this calibration square on Thingiverse](https://www.thingiverse.com/thing:2563185). This has squares for both horizontal (XY) and vertical (XZ, YZ) axes.
You can print a cube, but make sure that the corners don't bulge if you are measuring across them, to get an accurate measurement.

## Measure the diagonals

For your chosen axes:
* measure the diagonal A to C
* measure the diagonal B to D
* measure the edge A to D

Use these diagrams for reference:
<br>
<pre class="cblock">
    Y                     Z                     Z
    ^     B-------C       ^     B-------C       ^     B-------C
    |    /       /        |    /       /        |    /       /
    |   /       /         |   /       /         |   /       /
    |  A-------D          |  A-------D          |  A-------D
    +-------------->X     +-------------->X     +-------------->Y
    XY SKEW               XZ SKEW               YZ SKEW
</pre>

## Calculate the skew factor

Skew factors can be calculated and set manually:

`AB = SQRT ( 2 * AC * AC + 2 * BD * BD - 4 * AD * AD ) / 2`
`Skew factor = TAN ( PI / 2 - ACOS ( ( AC * AC - AB * AB - AD * AD ) / ( 2 * AB * AD )))`

Or you can use the following Gcode in RRF v3.x to calculate the skew factor. Copy it into a macro and upload it to your Duet. Edit the AC, BD and AD values to your measured values:
```
var AC = 331.229
var BD = 241.842
var AD = 200
var Skew_mm = 0

; Compute skew
var AB = sqrt((2 * (var.AC * var.AC)) + (2 * (var.BD * var.BD)) - (4 * (var.AD * var.AD)))/2
var Skew = tan(pi/2-acos((var.AC * var.AC - var.AB * var.AB - var.AD * var.AD)/(2 * var.AB * var.AD)))
if (var.AC-var.BD>=0)
	set var.Skew_mm = sqrt(var.AB * var.AB - var.AD * var.AD)
else
	set var.Skew_mm = -sqrt(var.AB * var.AB - var.AD * var.AD)

echo "Skew factor:", var.Skew
echo "Skew distance @", var.AD, "mm:", var.Skew_mm, "mm"
```
Example output on running the above macro:
```
M98 P"0:/macros/Skew calculator.g"
Skew factor: 0.3201567
Skew distance @ 200 mm: 64.03204 mm
```
Note: the skew value in the above example is very large! Most skew factors will be much smaller. A skew distance of 2mm at 200mm gives a skew factor of 2 / 200 = 0.01

Calculate values for all axes (XY, XZ and/or YZ) that require them.

## Configuring M556

You can configure M556 either directly with the calculated Skew factor or with Skew distance and AD length. Axes can be set individually, or all at once.
Suppose the AD and XY measurements are AD = 200 and XY = 64.03204. Then send:
```
M556 S200 X64.03204 ; set XY Skew distance @  200 mm:  64.03204 mm
; or send:
M556 S1 X0.3201567
```
For multiple axes, you may send something like:
```
M556 S200 X64.03204 Y-3.1 Z2.25
; or send:
M556 S1 X0.32016 Y-0.01550 Z0.01125
```
You can check the skew compensation used by sending M556, e.g.:
```
M556
Axis compensations - XY: 0.32016, YZ: -0.01550, ZX: 0.01125
```
Your machine will now correct for the angles between the axes when it prints.

## Finishing up

Usually the best place for the M556 command in your config.g file. You can put it in a macro file called, say, setaxes.g and upload that to your Duet's SD card using the web interface. Then, when you run that file, it will set your axis compensation.

To check that the axis compensation has worked, apply it, print the test pieces again, and check the diagonal measurements again. This time you should find the diagonals are equal.