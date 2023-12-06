---
title: Configuring RepRapFirmware for a Robot printer
description: 
published: true
date: 2023-12-06T08:56:55.482Z
tags: robot
editor: markdown
dateCreated: 2022-03-03T13:05:06.424Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# Overview of implemented configurations

The following configurations are implemented and tested:

|-|-|-|
|type|tested by|status|
|CoreXY AC, Zbed, ACbed|@JoergS5|fw since 2023...., dirty test|

meaning of abbreviations:
- Zbed: Z axis is connected to the bed
- Zendp: Z axis connected to endpoint
- ACbed: AC both connected to the bed, A nearest to workpiece


# current status of firmware
This are the current capabilities of the firmware which is published on github:

Development concentrates on CoreXY 5 axis AC and BC mode:
* release of Aug 9th with Mini 5, 6HC, 6XD and Duet 2 binaries
* A axis (1,0,0) with optional y and z offsets in AC mode
* B axis (0,1,0) with optional x and z offsets in BC mode, no prototype test
* C axis (0,0,1) with 0 x, y offsets (i. e. bed 0,0 above C axis)
* D paramter support to hinder unhoming
* Z attached to bed. Z attached to hotend (like Voron 2.4) may work
* no angle, speed limits watched (will be changed soon)

I'm currently working on a homing procedure, to activate the limits and to use arbitrary A and C axes.


# Overview

How to setup:
|---|---|
|configuration (this starting page)|[Configuring](/User_manual/Machine_configuration/Configuring_RepRapFirmware_for_a_Robot_printer)|
|6 axis industrial robot|[6 axis industrial](/User_manual/Machine_configuration/robot_industrial_6_axis)|
|CNC, CoreXY, Delta, Prusa, 5-Bar-Par.Scara 5 axis|[5 axis](/User_manual/Machine_configuration/robot_5_axis_CNC)|
|4 axis palletized (parallelogram)|[4 axis palletized](/User_manual/Machine_configuration/robot_4_axis_palletized)|

Using:
|---|---|
|RobotViewer DWC plugin|[RobotViewer DWC plugin](/User_manual/Machine_configuration/RobotViewer_DWC_plugin)|
|Object Model|[object model](/User_manual/Machine_configuration/robot_object_model)|

Theories:
|---|---|
|firmware explained, orientations|[Firmware](/User_manual/Machine_configuration/robot_firmware)|
|screw theory (product of exponentials, PoE)|[screw theory](/User_manual/Machine_configuration/robot_screw_theory)|
|conformal geometric algebra (CGA)|[geometric algebra](/User_manual/Machine_configuration/robot_geometric_algebra)|
|Denavit-Hartenberg (DH)|[DH parameters](/User_manual/Machine_configuration/Configuring_Robot_DH_parameters)|


The kinematics is developed for Duet3Ds RepRapFirmware. The **robot firmware is currently in development** on the base of RRF 3.5.0beta3.

The source is in github https://github.com/JoergS5/RepRapFirmware/tree/3.5-dev/src/Movement/Kinematics
RobotKinematics.cpp is code which is used by RRF directly. RobotKinematics1 to 2.cpp is code which is independent of RRF and can run and be tested outside RRF.


The robot is dicussed in the Duet forum at: [robot thread](https://forum.duet3d.com/topic/17421/robotic-kinematics/285) and in a few additional forum threads about robot prototypes. Current discussion about CoreXY 5 axis is at [CoreXY5axis thread](https://forum.duet3d.com/topic/33060/joergs5-s-corexy-5-axis-robot-kinematics/3?_=1690185230293)




Current status, last actions:
* implementation for CoreXY 5 axis AC
* P"abSign" implemented and processing
* RTCP improvement
* performance measured: 35 microseconds for each segment of inverse kin. on Mini5 (can be measured by movements, then call M669 R)
* CoreXY5BC implemented, but not tested with prototype
* abSign=2 added
* binaries 6XD added, so there are now Mini 5, 6HC, 6XD and Duet 2. For other destinations please compile yourself following the readmes on github
* changed to geometric algebra code, which is faster (15 microseconds) and removes some of the singularities at (0,0,0) and A0

# Configuring a Robot

When Duet starts after power on, it needs to know how to behave. Reading the file config.g and some associated files, the firmware is set to specific configurations like setup of the steppers, arm lengths, endstops and homing positions, heaters etc. M669 is at the core to define robot kinematics behaviour, accompanied by other settings, which are described in this document.

The robot kinematics supports different types. Roughly, they can be separated into
* serial robots (also called open chain): the joints and arms are connected in one serial chain. Industrial robots (6 rotational axes), CNC 5 axis (3 prismatic, two rotational axes), cartesian (three prismatic axes), serial scara (one prismatic and two rotational axes) and polar (two prismatic and one rotational axes) printers are examples. Configuration allows mixing any prismatic and rotational axes, e.g. a cartesian with spheric head (3 prismatic and 3 rotational axes).
* parallel/closed chain robots: the joints and arms are completely or partially connected in parallel. Often some joints are without an actuator. Examples are (3 arm) delta, 5 bar parallel scara, stewart/hexapod, 4 axis palletized robot.

Serial robots are easier to calculate, but parallel robots have higher precision and in case of 4 axis palletized robots higher payloads.

# M669 configuration
M669 and its parameters are used to define the robot properties like arm lengths and type of axes.

- the first line should specify K type, B and P"axisTypes=...". This will set some important parameters which are needed for the following parameter settings. Example M669 K13 B"CoreXY5AC" P"axisTypes=RRPPP"
- after K13 is choosen, M669 without parameters will report the current settings to the console. M409 K"move.kinematics" displays most parameters as object model. The report is explained in the next chapter
- multiple settings of the same starting letter must be on separate lines, as the G-Code interpreter evaluates only the first one. Different letters can be combined.

Overview
* K13 set robot kinematics and must be defined first
* B"name" or B"axisorder"
* A"letter=..." minimum, maximum and home angles
* C"para=..." screw parameters
* D don't unhome
* P"para=..." special parameters
* R reporting
* S segments per second
* T minimum segment length in mm

Most changes in config.g don't need a reboot, but when drive or letter assignments with M584 change, a reboot is often necessary.

# M669 without parameter: report configuration

After M669 K13 is defined and robot kinematics set, calling M669 without parameters will report the current configuration to the console.

- The first line reports general axis information:
Example: numOfAxes 5 axisTypes RRPPP chain CAZ.. specialChain XY
means 5 moving/rotating axes are defined with types rotary/rotary/linear/linear/linear. The chain begins at the print object/workpiece and ends at the hotend/drill. .. means, the letters are connected in a specific way with special handling, in this case the connected steppers for XY.

- the next lines reports information about each axis, including orientation, a point on the axis, min and max angles, homing angles

- the next lines report information relevant for screw calculations: reference angles/positions and endpoint position and orientation and currently set tool length, offset and orientation.

- the next lines report settings for special subkinematics like A (B) positive/negative preference for 5 axis or workmode for 5 bar scara.

- the next lines will report how much of the chain cache is used and how much is available. The chain cache reuses memory for different subkinematics, default are 200 floats, wich is currently enough to store 6 axis robot information.

- the final line will tell whether from firmware's view the parameters are completely specified to give reasonable results

# M669 B parameter: robot type

**B"name"**
or
**B"axis chain parameters"**

The name is only a placeholder for convenience. The important part is the parameter. Using the name sets the parameters from the table.

The parameter describes the chain of the axes in the order from bed/table to the endpoint/head/hotend/spindle/tool. Example: the rotating table C comes first in the chain, so the order is CA.

|-|-|-|-|
|name|kinematics|parameter|axisTypes|comment|
|Industrial6|6 axis industrial robot|XYZABC|RRRRRR|Puma, IRB120 like|
|CNC5AC *) |CNC 5 axis|CAYXZ|RRPPP||
|CoreXY5AC|CoreXY 5 axis|CAZ_corexy(XY)|RRPPP|CA_corexy(XY)Z if Z at head. corexy(YX) can also be used|
|LinearDelta5AC|Linear Delta 5 axis|CA_lindelta(XYZ)|RRPPP||
|RotaryDelta5AC|Rotary Delta 5 axis|CA_rotdelta(XYZ)|RRRRR||
|Prusa5BC|Prusa style, Open5x|CAYZX|RRPPP||
|5bar5AC|5 bar par. Scara|CAZ_5bar(XY)|RRPRR|optional cantilevered mode|
|Palletized4|4 axis palletized|X_pall(YZ)|RRR|optional actuator at hotend (parameter X_pall(YZ)A, axisTypes RRRR), IRB 460 like, optional inverse mode|

*) All AC names change to BC for a BC orientation instead of AC.
- AC is used if the A axis is parallel to the X axis
- BC is used if the B axis is parallel to the Y axis
- the C axis is parallel to the Z axis

The underscore _ can be omitted. The length for name and parameter is  currently limited to 20 characters each.

Special kinematics are marked with syntax similar to a function call. Available kinematics are those listed in the table. After the special function call can be additional axes (e. g. to rotate the hotend). For some kinematics like Delta and 5-bar-parallel-Scara, additional parameters need to be set. This will be done with Parameter P, please see below.

axisTypes is in the order of the chain. Only axes with actuators are added. The types of the passive hinges are defined by the kinematics. Example: 5 bar parallel three passive rotational hinges. Palletized has one rotational hinge behind XYZ.

Hangprinter 5 axis, Polar 5 axis, Tripteron and Stewart-Gough will be added later if it makes sense.

Not being on the list doesn't mean that a robot type is not supported. E.g. polar kinematics, serial Scara, cartesian with 3 axes spherical head and more are all supported by specifying axis types individually.

Example 1: M669 B"CoreXY5AC" defines the core parameters for the CoreXY with two rotary axes where A is parallel to the X axis, C to the Z axis and both are assembled in the order CA viewed from the print object's perspective. The axis orientations are the same as the main axes, i. e. A points to the right and C to the top (this defines the rotation positive angle directions, CCW looking at the arrow).

Example 2: M669 B"XYZAB" with axisTypes="RRRRR" will define a 5 axis industrial robot with rotary axes.

# M669 A parameter: angles

M208 to set home positions is not applicable to robots, because M208 XYZ values are cartesian coordinates, but to set rotary angle positions, the angle values are necessary. So all angles (homing, min, max) are set explicitly with a separate M669 A parameter.

**A"letter=min:max:home"**
**A"letter=home"**

* letter is the axis letter
* min is the minium angle for rotary axis and minimum position in mm for prismatic axis
* max is the maximum angle or position
* if no min and max are set, the axis is continuous. This doesn't mean continuous movement, but only that it can rotate any degree *)
* home is the home position in degrees or mm. The value can be outside min and max, the endstop can be low or high type

*) this is the firmware setting. It must be physically possible also, without wires, electronics or filament hindering it.

If An is not defined for an axis, then the M208 values are used for homing: depending in low or high end the S1 or S0 value and taking the values as limits. A prismatic X, Y, Z axis or A, B, C rotary axes are handled this way. Rotary X, Y, Z axes must be defined with An, because M208 X, Y, Z values are cartesian values, and rotary axis values are angles.

Example:
* A"X=-180.0:180.0:0.0" means the axis X can rotate between -180 and +180 degrees and when while homing the endstop is triggered, the motor position is set to 0.0 degrees (or mm, if it's a prismatic axis)
* A"C=0" means the C axis (e. g. the rotating table of AC) is continuous and the homing angle is 0 degrees or 0 mm. In most cases, this is only possible if no electronics or filament is attached to the rotating element.

# Coordinates

Before explaining the following parameters, a word about coordinate system is necessary.

Coordinates like the next omega1...3 are x, y, z coordinates. They have the property
- they sum to 1, i. e. they are normalized, i. e. x²+y²+z²=1
- they are right handed
- if the input is not normalized, the configuration will normalize and store the normalized value

Performance of the kinematics is best when using values with one +-1 and two 0 values, i. e. exact orientation into the axis directions.

The normalizing is for convenience of the user, allowing to input a value like (0, 0.01, 1) and the input procedure will normalize it to something like (0, 0.008, 0.99) by dividing the source data through the length.

The normalizing may lead to a solution which is not orthonormal any more (i. e. multiple axes are not perpendicular on each other), so this should only be used for small corrections (i.e. the length of the input values should be near 1).

This normalizing is applied to:
- Cletter omega1...3
- C"Mnoap" r11...r33
- P special settings if they contain axis coordinates

Example: change main linear axis direction a bit for correcting small deviations from 90 degree in construction.

# M669 C parameter: screw properties

C defines the axis properties, the endpoint for reference angles/positions, and default tool properties.

**C"letter=omega1:omega2:omega3:q1:q2:q3"**
**C"Mnoap=r11:r21:r31:r12:r22:r32:r13:r23:r33:p1:p2:p3"**
**C"Mreference=a0:a1:a2:..."**

* letter is the letter which is used by B
* omega1:omega2:omega3 is the axis direction
* q1:q2:q3 is a point on this axis in cartesian world coordinates. High precision is currently only important for rotary axes.
* Mnoap and it's 12 values is a transformation matrix from begin to end of the chain. r11,r21 and r31 are xyz directions of the x-Axis. r..2 of the y-axis and r...3 of the z-axis. p1...p3 is the position.
* Mreference are the actuator's angles in degrees which were used to calculate the M values
* the **Mnoap endpoint is including the tool** length, i. e. the true endpoint of the hotend or drill. I am no expert for laser, but probably it is the focus of the laser beam which is the reference.

Example:
* C"X=1:0:0:70:0:352" means the X axis points to the X-axis direction and a point on the axis is (70,0,352). Looking from the arrow side to the axis, counterclockwise (CCW) means positive angle change.
* C"Mnoap=0:0:1:0:-1:0:1:0:0:615:0:712" is the setting of the DH example of the 6 axis robot
* C"Mreference=0:0:0:0:0:0" means Mnoap is the endpoint orientation and orientation if all angles are 0 degrees. The values are degrees for rotational and mm for linear/prismatic axes.

# M669 D parameter: don't unhome

Most M669 parameters will unhome, i. e. it is necessary to home after issuing the command. The D parameter allows to set some parameters without homing.

The change may produce a high velocity movement if coordinates change too much, so handle with care if used. A possibility is to reduce speed for the next move.

# M669 P parameter: axisTypes, special

**P"axisTypes=[R]|[P]*"**

Defines the type of the axes. It is important that it matches the number of actuators.

* R means rotational/revolute, units are degrees, speeds e.g. degrees/min.
* P means prismatic/linear, units are mm and mm/min.
* when specifying a known B type, axisTypes will be set there, but can be overwritten

The parameter must be set correct, otherwise kinematics will not calculate correctly.

Examples: see the B parameter table column axis types.

**P"abSign=0|1|2"**

* relevant for 5 axis kinematics with AC or BC rotary axes
* setting whether inverse kinematics shall use the positve or negative A (B) axis solution.
* 0 means stay positive or 0, 1 stay negative or 0, 2 means don't change calculation result. Default is 0

For AC/BC, there are two solutions (with exception 0,0), so in situations where the move crosses A0, the other position is choosen with the result of C rotating very fast by 180 degrees. This will probably be visible in the print. To avoid this, it is best to stay at positive or negative (>= 0 or <= 0). The abSign stores the preference, so the firmware knows the preference for sure. In general, the moves have no memory of past moves, so this is necessary.

The two solutions are rotating C by 180 degrees and to negate A, eg AC being (30, 80) second solution is (-30, -100). The XYZAC coordinates on the print object stay the same, but the axis positions of XYZ are very different.

**special settings for rotary Delta**


**special settings for linear Delta**

**special settings for CoreXY and CoreXZ**
* XZ translation ratio

**special settings for 5 bar parallel Scara**
* forward workmode
* cantilevered modes left/right

# R reporting

R reports performance statistics, currently the average time to process one segment.

# M669 S, T parameters: segmentation

**Sn** Segments per second, default is 100

**Tn** Minimum segment length (mm). Default is 0.2 mm

G1, G2, and G3 moves are separated into segments, which are executed as straight lines. The length of the segments is controlled by the S and T parameters. More segments give better results, but at the cost of processing time to calculate them.

# M584 drive assignment

For CNC 5 axis, it is common to use XYZAC (or AB, BC) as drive letters, AC being the rotary axes. RRF's default is to use XYZUVWABCD in this order, so to use AC instead, an explicit drive letter assignment is needed, see documentation for M584.
Example:
M584 X0 Y1 Z2
M584 A3
M584 C4
To be sure that the drives are created in the correct order (the order of motorPos), it is best to create the drives which are out of default order on separate lines. Default order is XYZUVWABCD.

# M584 R0, R1: axis type

In RRF, XYZUVW are linear axes by default and ABCD rotational axes. This corresponds to CNC conventions. If usage differs from this defaults, the defined axes for robot kinematics should be clarified as prismatic or rotational with the M584 settings, R0 meaning prismatic/linear and R1 meaning revolute/rotational. The reason is, RRF uses this information for some calculations like the distance calculation and uses different algorithms for prismatic and rotational axes.

# tool changing

If the G10 offset values are changed, e. g. when the tool is being changed, the screw endpoint Mnoap needs to be set to the new value.

# M208 limits
M208 limits the allowable printable area to a cube by setting X, Y, Z limits. Using it with G1 H1, the values only make sense for linear axes. If using X, Y, Z rotary axes, the M208 values should be changed to angle values while defining the homing position with G1 H1 and then set back to the cartesian limits after homing. A, B, C values can be used for angle/position (rotary/prismatic axis).

The M208 limits are controlled when calculating whether the object is printable, together with the reachability by arm lengths and angle restrictions. 3D printers and CNC are handled slightly different:
* 3D printers move partially until reaching the edge of M208
* CNC doesn't execute the move and reports an error

# speed limits: M350, M92, M203, maxVelocity

The following values are important to check speed limits and the reporting of speed violations. Movement speeds will be lowered or hindered if the motor speeds are too high.

* M92 are steps/mm for prismatic and steps/degrees for rotary axes, where the step number of a stepper, the M350 microsteps, gear ratio and pulley teeth are considered. Example 200-step stepper, 16 microsteps,1:3 ratio gear and 20 teeth of pulley are 200 * 16 * 3 / 20. The M92 values must be correct for calculations.
* M203 is the mm/min for prismatic and degress/min for rotary axes. An industrial robot like Kuka has typically 180 degress for one second, i. e. M203 would be 180 * 60 = 10800. The "big axis" from 1 to 3 are slower, the axes 4 to 6 are often faster, sometimes doubled speed. This makes sense, as axes 1 to 3 have to rotate more mass than axes 4 to 6. Accelerations will differ accordingly.
* a typical prismatic M203 could be 200 mm/s * 60 s = 12000

If a movement has a velocity too high for one of it's axes, it is currently reported on the console, but the movement is currently not hindered. This will be changed in productive use. A too high velocity is a signal of approaching or reaching a singularity or if the extrusion speed is set too high compared to the normal axis speeds. In most cases, however, extrusion speed is not the limiting factor.

# Homing
When the Duet controller starts, it has no knowledge about the stepper positions. By a procedure called homing it gets and stores those values.

In robot kinematics, three different methods are supported:
- specifying the homing angles with A parameter and when an endstop triggers, homing is set to this value
- when no A is set for this drive, the M208 value is used when triggering while G1 H1 is used. The usual high/low of endstop configuration is used. M208 can be changed for homing and set back to printing limits afterwards (see M208 chapter).
- setting the homing values with G92. Example usage is setting homing positions with absolute encoders or optical based direct value setting (camera, interferometer, calibration result etc).

For rotary axes, the values are in degrees, for linear/prismatic axes it is in mm. The G92 and M122 values are degrees or mm * microsteps * ratio.

Example: rotary axis homing position 10 degrees, 16 microsteps, gear 1:5 between stepper and axis, then value is 10 * 16 * 5 = 800.
Example: prismatic axis homing position 10 mm, 16 microsteps, gear 1:5, 20 teeth GT2: one rotation is 40 mm,  ... tbd

Homing values can be checked by the M122 Count value.

The default homing file is called homeRobot.g

# Mesh compensation
Mesh compensation is a feature to handle uneven print beds und allow printing with good adhesion by printing the first layers in sync to the unevenness of the bed. It is used a probe to record the unevenness data, which has in most cases an XYZ offset from the hotend. The offset may not change while measuring, because the firmware calculates the hotend position from the probe offset and stores the hotend positions in the mesh file.

After the mesh is measured and stored, the probe is not needed anymore. To avoid collision with the print object later when the hotend tilts (and with it the probe), a mechanical removal of the probe or a save distance in Z direction should be considered when nonplanar printing or drilling is used.

# Configuration first testing
When configuration is stored and Duet rebooted, the following procedure shall avoid damages:
* when Duet reboots or is powered off, the motors lose current and the arms may fall down (for protection, consider brakes, weight balance, gear friction, detent torque, springs, counterweights)
* G91 G1 H2 X1 to check whether first axis rotates into the expected direction and it is the correct axis. Use low values in case the move is wrong. Repeat with the other axes. Check letter assignments, positive and negative angles interpreted as expected, and whether rotation degrees are correct by rotating e. g. 90 degrees.
* G91 G1 H2 Xn with bigger values to assure that the M92 settings are correct
* home the individual axes and assure that the endstops are triggered. M114 Count values can be used to check the stored motor position value for the homing position.
* with normal G1 moves, check that the coordinates are interpreted correctly, X positive being to the right, Y positiv to behind and Z positive means greater distance between hotend and bed. This step is especially important for setups with the print object moving (workpiece mode).
* there are kinematics like CoreXY where G1 H2 and normal G1 moves behave differently for some axes.
