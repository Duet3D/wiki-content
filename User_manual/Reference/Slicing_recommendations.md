---
title: Slicing recommendations
description: Suggestions for improving compatibility of slicers with RRF.
published: true
date: 2021-12-03T16:03:26.704Z
tags: 
editor: markdown
dateCreated: 2021-12-03T16:03:24.047Z
---

The current version of Cura supports relative extrusion under the "Special Modes" section.

For best accuracy we recommend slicing with relative extruder coordinates. This avoids the accumulation of rounding errors that firmwares suffer from when using absolute extruder coordinates. All popular slicers support relative extruder coordinates.

In your slicer start gcode, include command M82 if you are using absolute extrusion, or M83 if you are using relative extrusion. Some slicers insert these commands automatically, others do not.

RepRapFirmware supports multiple tools (i.e. print heads). You must always define which tool you want to use at the start of a print. If you are slicing for a single-tool machine, include T0 followed by M116 in your slicer start gcode.

***This section is still under development***