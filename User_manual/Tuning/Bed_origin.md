---
title: Centering the bed or setting the bed origin
description: 
published: true
date: 2021-12-03T15:50:26.433Z
tags: 
editor: markdown
dateCreated: 2021-12-03T15:50:23.740Z
---

# Introduction

With most printer geometries you can choose where on the bed the origin (the X=0 Y=0 point) will be. There are two common choices:

1. X=0 Y=0 is at the centre of the printable area. This has a number of benefits. You will configure your slicer to centre prints on X0 Y0 regardless of the size of the bed; so you don't need to change the slicer configuration depending on the bed size, and you may be able to print the same sliced files on more than one printer.  If you have an IDEX printer then you don't need to change the print centering in the slicer depending on whether you print the part in ditto mode or in normal mode; also IDEX mirror mode printing is easier to set up.
1. X=0 Y=0 is one corner of the printable area, typically the front left corner. Some users of Cartesian and CoreXY printers prefer this scheme. You will need to configure your slicer so that it know where the centre of the bed is, in order to centre prints on that position.

# Setting the origin on a Cartesian or CoreXY/CoreXZ/CoreXYU printer

The [M208 S0](/User_manual/Reference/Gcodes/M208) command defines the upper position limits of each axis, and the M208 S1 command defines the lower position limits. When an axis is homed to a low end (minimum) homing switch, the axis is assumed to be at the lower position limit. When it is homed to a high end (maximum) homing switch, it is assumed to be at the upper position limit. Therefore, changing the position limits in the M208 command will move the origin.

Example: a Cartesian printer uses a low end X homing switch, which triggers when the head reference point (i.e. the nozzle in a single tool printer) is 8mm off the low-X edge of the bed. It uses a high-end Y homing switch, which triggers when the head reference point is 2mm off the high-Y edge of the bed. The bed is 200mm square.

## Setting origin in centre

Suppose we want to make X0 Y0 the centre of the bed. Then the Xmin edge of the bed is at X=-100 and the homing switch triggers at X=(-100-8) = -108mm. Similarly, the Ymax edge of the bed is at Y=100 and the Y homing switch triggers at Y=(100+2) = 102mm. So we can use the following commands in config.g:
```
M208 S1 X-108 Y-100  ; set axis lower limits
M208 S0 X100 Y102    ; set axis upper limits
```
or alternatively when using firmware 2.02 or later:
```
M208 X-108:100 Y-100:102  ; set axis limits
```

## Setting origin in corner

If instead we want to make X0 Y0 the corner of the bed then we would use:

```
M208 S1 X-8 Y0     ; set axis lower limits
M208 S0 X200 Y202  ; set axis upper limits
```

or if using firmware 2.02 or later:

```
M208 X-8:200 Y0:202     ; set axis limits
```

Notes:

* You would normally have Z parameters in the M208 commands too.
* The M208 limits set the position immediately after the corresponding G1 S1 command completes by triggering the homing switch. However, if your homing file has a G92 command after the G1 S1 command (as is typically the case when homing to a Z-min switch), then the G92 command will override that position.

# Setting the origin on a Linear Delta or Rotary Delta printer

The origin is always the centre of the bed and there is no facility to change it. If you really want an offset, use either a tool offset (G10) or workplace coordinates (G10 in conjunction with G54..G59.3).

# Setting the origin on a Scara printer

In the M669 command, use the  X and Y parameters to specify the offset where you want X=0 Y=0 to be relative to the centre of the proximal joint.

# Setting the origin on a Polar printer

To be completed.