---
title: Slicing recommendations
description: Suggestions for improving compatibility of slicers with RRF.
published: true
date: 2022-03-16T17:54:14.946Z
tags: 
editor: markdown
dateCreated: 2021-12-03T16:03:24.047Z
---

- For best accuracy we recommend slicing with relative extruder coordinates. This avoids the accumulation of rounding errors that firmwares suffer from when using absolute extruder coordinates. All popular slicers support relative extruder coordinates. The current version of Cura supports relative extrusion under the "Special Modes" section. 
- In your slicer start gcode, include command M82 if you are using absolute extrusion, or M83 if you are using relative extrusion. Some slicers insert these commands automatically, others do not.
- RepRapFirmware supports multiple tools (i.e. print heads). You must always define which tool you want to use at the start of a print. If you are slicing for a single-tool machine, include T0 followed by M116 in your slicer start gcode.
- RepRapFirmware supports thumbnail image display on Duet Web Control and on PanelDue with firmware 3.4.1-pre and later. To use this facility, the slicer must include thumbnail images in the GCode file that it generates. For PanelDue a thumbnail size of 160x160 pixesl is recommended and the image format must be QOI. For Duet Web Control the image format may be QOI, PNG or JPEG.

***This section is still under development***