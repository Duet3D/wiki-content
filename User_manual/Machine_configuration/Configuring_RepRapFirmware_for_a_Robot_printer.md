---
title: Configuring RepRapFirmware for a Robot printer
description: 
published: true
date: 2022-08-29T04:59:57.122Z
tags: robot
editor: markdown
dateCreated: 2022-03-03T13:05:06.424Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

The kinematics is developed for Duet3Ds RepRapFirmware and will be included in 3.5. The **robot firmware is currently in development**.

The robot is dicussed in the Duet forum at: [robot thread](https://forum.duet3d.com/topic/17421/robotic-kinematics/285) and in a few additional forum threads about robot prototypes.

# Configuring a Robot printer

When Duet starts after power on, it needs to know how to behave. Reading the file config.g and some associated files, the firmware is set to specific configurations like setup of the steppers, arm lengths, endstops and homing positions, heaters etc. M669 is at the core to define robot kinematics behaviour, accompanied by other settings, which are described in this document.

# M669 configuration

M669 and its parameters are used to define the robot properties like arm lengths and type of axes.

M669 K13 AT"type|R|P" should be the first line to set robot kinematics and define roughly the configuration.

M669 without parameters will output the current settings to the console.

Most parameters are described in separate sections below.

Overview
* K13 defines robot kinematics and must be defined first
* A defines robot type, axes types, axes and arm properties by using Denavit-Hartenberg parameters
* B allows setting specific parameters directly
* P behaviour of axes, specific for a robot type
* Q quality of calculation to allow balance between precision and time needed for calculation
* R reporting mode to get information about current configuration or recommendations about good parameter settings
* S segments per second
* T minimum segment length in mm


**Qn** quality of calculation

Q1 is fast but lowest, Q5 is slow but highest quality of calculation. The time needed to calculate depends on the processor speed. Slow and high quality means the algorithms takes more time to calculate exact results. Quality can be changed anytime between moves, e. g. to print specific object details with higher quality. Default is Q3

**R** Reporting modes (this parameter will change often)
* R0 no reporting, default.
* R1 prints out time information and calculation precision to the console about running core methods of robot kinematics. Helps to find the best Q value.

**Sn** Segments per second

**Tn** Minimum segment length (mm). Default is 0.1 mm

G1, G2, and G3 moves are separated into segments, which are executed as straight lines. The length of the segments is controlled by the S and T parameters. More segments give better results, but at the cost of processing time to calculate them.

# M669 A parameter
**A** is used to define the properties of the robot.

* AT:string defines the actuator type, rotary or prismatic and how they are assembled
* A0...n:parameters define DH parameters with optional Y

AT:"name|[R]|[P]|[Rp]|[htabc]*" defines the overall configuration and number of axes. R mean revolute (rotational), P is prismatic (translational, linear) joint, Rp means revolute parallelogram closed chain without actuator, htabc are used for CNC 5 axis flavours.
* AT:"RRRRRR" means 6 axis robot with rotational axes
* AT:"RRP" means serial scara with Z axis being prismatic (prismatic means linear movement)
* AT:"PPP" means 3 axis cartesian printer.
* AT:"PPPRRR" means cartesian printer with additional spheric 3 axis head
* AT:"PPPRtaRtc" means CNC 5 axis with A as main rotary axis on table and C as dependent (installed on A) on table also
* AT:"PPPRhbRtc" means CNC 5 axis with B axis on head and C on table
* AT:"RRRRp" means 4 axis palletized
* AT:"RRRRpR" means 4 axis palletized with 4th actuator

There is a separate document, explaining the robot types and their specifics.

**DH: Ajoint:d:theta:a:alpha:home:minangle:maxangle**

* DH (Denavit-Hartenberg) parameters are defined by:
* joint 1 is describing the transformations, so that the resulting coordinate system at axis 2
* d offset in Z direction
* theta rotation by Z axis, added to the variable theta angle (so the position of 0 degrees can be altered)
* a is the distance between Z and former Z axis. If alpha is 0, 90 or -90, it is the arm length
* alpha, which is the X axis rotation and is as high as the Z and former Z angle difference. Often 0, 180, 90 or -90 degrees
* home, min, max angles of theta if rotatioal axis. Home, min and max position in mm for a prismatic axis.

Instead of DH parameters, all 6 translations and rotations can be defined by:
**Aaxisnr:d:theta:ytr:yrot:a:alpha:home:minangle:maxangle**

ytr is transformation in direction of Y axis, yrot is a rotation for a rotational axis and translation for a prismatic axis.

When 8 values are defined, DH parameters are expected. When 10 values are defined, it is interpreted as general setting of all transformations.

Adding the possibility to define Y axes' parameters rotation and translation to give full flexibility to define the coordinates. The Z axis is rotated and translated first, then Y, then X, according to the roll-pitch-yaw (RPY) order.

8- and 10-parameter settings can be mixed, using Y translation and rotation only where needed.

A1 to A6 are the equivalent to DH parameters. A0 allows a displacement of the first axis and axis rotations.


# M669 B parameter
B allows setting some parameters directly. tbd a list

# M669 P parameter

The P parameter changes the behaviour of the axes. The behaviour is different, depending on the robot type.

When a behaviour cannot be met, the kinematics throws an unreachable error and outputs an explanation to the console.

The P can be changed and is effective at any time between moves.

**6 axis robot with 6 rotary axes**

P0 axes have no preference. Only when an axis touches a limit, kinematics tries to find an alternative solution. Default
P1 endpoint is always vertical, but orientation around the Z axis is not controlled. Only when angle limit are reached, kinematics tries to find an alternative solution.
P2 endpoint is always vertical and XY endpoint axes are parallel to XY cartesian coordinates. This setting is valuable for probing for mesh compensation, because the probe offset stays at the same values
P3 endpoint is vertical and axes 4 and 6 are rotated as little as possible.

# Drive configuration
**For a 6 axis robot the following naming will be used:**

The 6 axes are named XYZUVW. Additional axes for 7 and more axis robots will be named ABC... M92 values are steps per degree for rotational axes and steps per mm for prismatic axes.

**For a 5 axis CNC and 3D printers like Open5x the following naming will be used:**

For rotational axes around the X axis A will be used, around Y B and around Z C. 5 axis CNC uses three linear axes XYZ and two rotational ones AB, AC or BC. The rotational axes can be installed at the spindle, called head/head, at the workpiece, called table/table, or mixed head/table. Open5x uses UV for rotational axes in G-Code, it is converted to the AT parameters. UV must be defined to rotational by M584 R1 in this case.

**For a 4 axis palletized robot (closed chain) this naming is used:**
(like ABB IRB 460, Kuka KR 700, Fanuc M-410)

The letters X, Y, Z when using 3 actuators, and the next defined letter if a 4th actuator is used.

# M584 R0, R1

In RRF, XYZUVW are linear axes by default and ABC rotational axes. This corresponds to CNC conventions. The defined axes for robot kinematics should be clarified as prismatic or rotational with the M584 settings, R0 meaning prismatic/linear and R1 meaning revolute/rotational. The reason is, RRF uses this information for some calculations like the distance calculation and uses different algorithms for prismatic and rotational axes. This clarification is only needed if the used letters differ from the default assignment.

# G10 tool
At the end of the last axis, a tool is attached. The robot's kinematics is calculation with the G10 offsets of the currently selected tool:
* X, Y, Z are the tool's offsets in mm. Default is 0, 0, 0.

If the tool has rotational elements, which may be necessary when e. g. using tool changers, there is no parameter to set them with G10. As solution is to define it at the corresponding DH parameter with the B parameter. An example will be provided in the DH document.

Offsets are included in the calculation of the XYZ position. The signs of the offsets are important and depend on tool's coordinate system (explained in the DH document).

# M208 configuration
M208 limits the allowable cubic area by setting X, Y, Z limits. Printing is only allowed inside this area (an execption is while homing). M208 setting can follow two strategies:
* defining a secure area where printing is always possible
* defining an area surrounding and using all "donut" shaped area. This will result in a possibility to address coordinates which are inside M208, but not reachable. The Kinematics will mention it and throw an error, because it checks M208, whether the position is reachable by arm lengths and restrictions (e. g. allowed angles), and whether it is near a singularity.
# Homing
The homing angles are specified in the M669 A parameter and can be impemented e. g. by endstops between joint's axes or by reading absolute encoder positions. G1 H2 addressing specific axes (joints). Setting the position with G1 H1 is not possible, because M208 X, Y, Z values do not match any stepper positions. When an endstop is triggered, the homing position is set by firmware code to the A value. If necessary, the value can be changed later by G92, the 6 axes being XYZUVW. For a rotational axis, the value the stepper angle * microsteps * gear ratio must be taken. The current stepper position is the nth count value when running M122.

If the homing position is in a singularity or near it, after homing the robot arms should be rotated away from it (this can be done with G1 H2 moves), before starting normal operation. The arm positions shall remain in this work mode for all following operation if possible.
# Mesh compensation
Mesh compensation is a feature to handle uneven print beds und allow printing with good adhesion by printing the first layers in sync to the unevenness of the bed. It is used a probe to record the unevenness data, which has in most cases an XYZ offset from the hotend. The offset may not change while measuring, because the firmware calculates the hotend position from the probe offset and stores the hotend positions in the mesh file.

Some robot setups can assure the constant XYZ offset between probe and hotend, some not:

* 6 axis robot can use M669 P2 mode to change the hotend orientation to stay parallel to XY axes
* a mechanical solution of a horizontal parallelogram or other means can fix orientation
* the probe can be installed at XY 0,0 position, e. g. under the hotend
* a toolchanger can load a probe instead of a tool and measure at 0,0

After the mesh is measured and stored, the probe is not needed anymore. To avoid collision with the print object later when the hotend tilts (and with it the probe), a mechanical removal of the probe or a save distance in Z direction should be considered.
