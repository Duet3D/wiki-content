---
title: Configuring RepRapFirmware for a Robot printer
description: Currently for 4 to 6 axis robots
published: true
date: 2022-03-03T13:34:34.785Z
tags: 
editor: markdown
dateCreated: 2022-03-03T13:05:06.424Z
---

# Configuring a Robot printer
Following is a description how to setup a 6 axis robot for 3D printing. Removing the 4th and 3rd axis are optionally supported to allow setup of robots with less actuators. The RepRapFirmware firmware is in development, binaries for testing will be provided.
# Construction
![robot_main.jpg](/manual/configuration/robot_main.jpg)
The firmware can be configured to run a typical industrial 6 axis robot with 6 rotational axes, but other types with mixed rotational and prismatic axes as well, e. g. Stanford manipulator.
# Denavit-Hartenberg parameters
To describe the robot setup like properties of the axes, arm lenghts and other properties, the Denavit-Hartenberg parameters are used to describe most of the properties.

The parameters and examples are described in a separate document:
