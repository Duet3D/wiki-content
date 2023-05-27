---
title: Configuring RepRapFirmware for a Robot printer
description: 
published: true
date: 2023-05-27T08:00:37.376Z
tags: robot
editor: markdown
dateCreated: 2022-03-03T13:05:06.424Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

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


The kinematics is developed for Duet3Ds RepRapFirmware. The **robot firmware is currently in development** on the base of RRF 3.5 beta.

The source is in github https://github.com/JoergS5/RepRapFirmware/tree/3.5-dev/src/Movement/Kinematics
RobotKinematics.cpp is code which is used by RRF directly. RobotKinematics1 to 4.cpp is code which is independent of RRF and can run and be tested outside RRF.

> The code is based on screw theory and geometric algebra. Denavit-Hartenberg-based code is moved to the RobotViewer DWC plugin.
{.is-info}


The robot is dicussed in the Duet forum at: [robot thread](https://forum.duet3d.com/topic/17421/robotic-kinematics/285) and in a few additional forum threads about robot prototypes.

Current status, last actions:
* started to include screw theory (also called PoE Product of Exponentials), based on skew matrices. This will probably speed up calculations. The extended DH parameter configuration with 6 parameters will remain, but Plücker coordinates will probably be added as option to define parameters.
* following Pardos-Godor's book to develop closed calculations for inverse kinematics

# Configuring a Robot

When Duet starts after power on, it needs to know how to behave. Reading the file config.g and some associated files, the firmware is set to specific configurations like setup of the steppers, arm lengths, endstops and homing positions, heaters etc. M669 is at the core to define robot kinematics behaviour, accompanied by other settings, which are described in this document.

The robot kinematics supports different types. Roughly, they can be separated into
* serial robots (also called open chain): the joints and arms are connected in one serial chain. Industrial robots (6 rotational axes), CNC 5 axis (3 prismatic, two rotational axes), cartesian (three prismatic axes), serial scara (one prismatic and two rotational axes) and polar (two prismatic and one rotational axes) printers are examples. Configuration allows mixing any prismatic and rotational axes, e.g. a cartesian with spheric head (3 prismatic and 3 rotational axes).
* parallel/closed chain robots: the joints and arms are completely or partially connected in parallel. Often some joints are without an actuator. The kinematic is more difficult to calculate and need dedicated formulae, so only defined types are supported. Examples are (3 arm) delta, 5 arm parallel scara, stewart/hexapod, 4 axis palletized robot. Delta is not supported in robot kinematics, because RepRapFirmware has dedicated delta support. 4 axis palletized is supported, stewart is planned, 5 arm parallel scara is planned.

Serial robots are easier to calculate, but parallel robots have higher precision and in case of 4 axis palletized robots higher payloads.

# M669 configuration
M669 and its parameters are used to define the robot properties like arm lengths and type of axes.

The first M669 line must specify the K13 type.

This will set defaults for some core settings.

M669 without parameters or with K13 alone will output the current settings to the console. M409 K"move.kinematics" displays most parameters as object model.

Multiple settings of the same starting letter must be on separate lines, as the G-Code interpreter evaluates only the first one. Different letters can be combined.

Overview
* K13 set robot kinematics and must be defined first
* B"axisorder" specify axis order and robot type
* A"a:..." minimum, maximum and home angles
* C"para=..." screw parameters
* P"para=..." special parameters
* S segments per second
* T minimum segment length in mm

P"axisTypes=..." is the most important setting, as it defines how many axes are used.

Most changes in config.g don't need a reboot, but when drive or letter assignments with M584 change, a reboot is often necessary.

# M669 B parameter: robot type

**B"name"**
or
**B"axis chain parameters"**

This parameter describes the chain of the axes from bed/table to the endpoint/head/hotend/spindle/tool. Example: the rotating table C comes first in the chain, so the order is CA.

|-|-|-|
|name|kinematics|parameter|comment|
|Industrial6|6 axis industrial robot|XYZABC|Puma, IRB120 like|
|CNC5AC *) |CNC 5 axis|CAZYX|other orders possible or CB...|
|CoreXY5AC|CoreXY 5 axis|CAZ_corexy(XY)|CA_corexy(XY)Z if Z at head|
|LinearDelta5AC|Linear Delta 5 axis|CA_lindelta(XYZ)||
|RotaryDelta5AC|Rotary Delta 5 axis|CA_rotdelta(XYZ)||
|Open5x|Open5x original (Prusa)|CBYZX|UV letters can be remapped|
|5bar5AC|5 bar par. Scara|CAZ_5bar(XYc1)|c1, c2 for cantilevered mode|
|Palletized4|4 axis palletized|X_pall(YZ)|optional actuator at hotend, IRB 460 like|
|Palletized4Inv|4 axis palletized inverse|X_pall(YZinv)|optional actuator at hotend|

*) All AC names change to BC for a BC orientation instead of AC.
- AC is used if the A axis is parallel to the X axis
- BC is used if the B axis is parallel to the Y axis
- the C axis is parallel to the Z axis

Not being on the list doesn't mean that a robot type is not supported. E.g. polar kinematics, serial Scara, cartesian with 3 axes spherical head and more are all supported by specifying axis types individually.

Example 1: M669 B"CoreXY5AC" defines the core parameters for the CoreXY with two rotary axes where A is parallel to the X axis, C to the Z axis and both are assembled in the order CA viewed from the print object's perspective. The axis orientations are the same as the main axes, i. e. A points to the right and C to the top (this defines the rotation positive angle directions, CCW looking at the arrow).

Example 2: M669 B"XYZAB" with axisTypes="RRRRR" will define a 5 axis industrial robot with rotary axes.

# M669 A parameter: angles

M208 to set home positions is not applicable to robots, because M208 XYZ values are cartesian coordinates, but to set rotary angle positions, the angle values are necessary. So all angles (homing, min, max) are set explicitly with a separate M669 A parameter.

**A"axis:min:max:home"**
**A"axis:cont:home"**

* axis is the axis number, starting with 0
* min is the minium angle for rotary axis and minimum position in mm for prismatic axis
* max is the maximum angle or position
* cont means the axis is continuous and has no min/max angles. This doesn't mean continuous movement, but only that it can rotate any degree
* home is the home position in degrees or mm. The value can be outside min and max, the endstop can be low or high type

If An is not defined for an axis, then the M208 values are used for homing: depending in low or high end the S1 or S0 value and taking the values as limits. A prismatic X, Y, Z axis or A, B, C rotary axes are handled this way. Rotary X, Y, Z axes must be defined with An, because M208 X, Y, Z values are cartesian values, and rotary axis values are angles.

Currently there is no continuous axis in RRF core, but when it exists, this setting will ignore angle limits.

Example:
* A"0:-180.0:180.0:0.0" means the axis 1 can rotate between -180 and +180 degrees and when while homing the endstop is triggered, the motor position is set to 0.0 degrees (or mm, if it's a prismatic axis)
* A"5:cont:0" means the axis is continuous and the homing angle is 0 degrees or 0 mm. In most cases, this is only possible if no electronics or filament is attached to the rotating element. The C rotary axis of CNC 5 axis could be cont e.g.

# M669 C parameter: screw properties
Instead of configuration by D parameters of Denavit-Hartenberg, properties based on screw theory can be used.

**C"axis=drivenr:s1:s2:s3:q1:q2:q3"**
**C"M=r11:r12:r13:r21:r22:r23:r31:r32:r33:p1:p2:p3"**
**C"reference=a0:a1:a2:..."**
**C"defaultToolLength=z"**

* axis is the axis number, starting by 1
* drivenr is the assigned drive number as defined by M584 and used by mapDriveLetterDn first digit
* s1:s2:s3 is the axis orientation as normalized XYZ directions. The direction reference are the world coordinates.
* q1:q2:q3 is a point on this axis in cartesian world coordinates
* M and it's 12 values is a transformation matrix from begin to end of the chain. r11 to r33 are the values of the rotation matrix, p1 to p3 are the XYZ positions, with respect to the reference (0,0,0).
* reference are the actuator's angles in degrees which are used to calculate the M values. It defines the workmode, i. e. which of the up to 16 possible solutions for a given position is choosen. Default is to expect 0 angles/positions. This parameter isn't needed for calculations, but is informative only.
* defaultToolLength is the Z length of the default tool. It is added to the endpoint calculation as placeholder when no tool is defined yet. The value is positive, although the direction is in the negative Z direction (i. e. lowers the distance between hotend and printbed).

The choice of the angles and M influence the performance of calculations: if they are near the desired target, less iterations are needed.

When using D parameter with DH values, the C values are calculated from them and the workmode angles are used for M and Mangles. When only R is used and not D, D is not calculated.

Example:
* C"1=1:0:1:0:70:0:352" means axis 2 is oriented horizontal with arrow to the back (i.e. Y=1 and the others 0) and the position is X 70, Y 0 and Z 352. This is a value of the DH example robot
* C"M=0:0:1:0:-1:0:1:0:0:615:0:712" is the setting of the DH example of the 6 axis robot
* C"reference=0:0:0:0:0:0" means M is calculated with all angles being 0 degrees
* defaultToolLength=100 is the tool length for initial calculation

# M669 P parameter: axisTypes, special

P commands will overwrite the default settings of B"robotType", so it makes sense to set P after the B parameter.

**P"axisTypes=[R]|[P]|[p]*"**

Defines the type of the axes. It is important that it matches the number of actuators plus optional parallelogram axis.

* R means rotational/revolute, units are degrees, speeds e.g. degrees/min
* P means prismatic/linear, units are mm
* p (lower case p) means passive joint without actuator with parallelogram (see 4 axis palletized robot type)

The parameter must be set correct, otherwise kinematics will not calculate correctly.

Examples:

* P"axisTypes=RRRRRR" means 6 axis robot with rotational axes
* P"axisTypes=RRP" means serial scara with third axis being prismatic, i. e. two rotary arms and one linear Z axis
* P"axisTypes=PPP" means 3 axis cartesian printer with three prismatic axes
* P"axisTypes=PPPRRR" means cartesian printer with additional spheric 3 axis head
* P"axisTypes=PPPRR" means CNC 5 axis with three linear and two rotary axes. With a rotary axis at the head, the order will be different like RPPPR
* P"axisTypes=RRRp" means 4 axis palletized
* P"axisTypes=RRRpR" means 4 axis palletized with 4th actuator, so 5 axes in total

CNC 5 axis allows many variants. The following dynamic mapping allows to configure them by defining how the forward kinematics is calculated. Inverting transformation matrices or reverting axes is necessary sometimes, as well as changing letter assignments.

**P"mapDriveLetterDn=0X3:1Y4:2Z5:3A1:4C0"**
**P"mapDriveLetterDn=0X1:1Y2:2Z3: p4"**
The first number is the drive number, the second drive letter and the third the Dn number. A parallelogram axis has no actuator, so the first number is omitted and space used instead.
* if this parameter is not set, it is expected that the first drive is used at D1, second at D2 etc. and the letters are standard XYZABC (or XYZUVW) for 6 axis, XYZ for 4 axis pallet, XYZAC for CNC 5 axis AC type.
* maps drive number with drive letter with Dn, in the first example the first drive called X is mapped to D3
* the parallel axis of the 4 axis palletized robot is named spacepn, e.g. " p4"
* default is 0X1:1Y2:2Z3:3A4:4B5:5C6 or abbreviated for less defined axes
* every Dn number may be used only once
* different drive numbers may not point to the same Dn
* the order of the elements is not important

Example:
* 4C0 means, drive number 4 from config.g with letter C is assigned to D!0 . An example for CNC 5 axis AC table/table configuration, where the chain starts with C axis inverted in workpiece mode.

**P"orientationType=AC|BC|full|no"**
The parameter defines how the robot shall behave in respect to orientation information. It can not change the physical properties of the robot, i. e. the setting will fail if the printer doesn't support the required mode. E. g. a cartesian printer cannot change orientation, so setting to full makes no sense.
* AC, BC means, only the orientation of the Z axis is important. That's the case with most 3D printers and CNC machines, including 5 axis CNC. The tool may get tilted, but the orientation with respect of X and Y axis is not controlled. AC is set if two rotational axes are used which are parallel to X and Z axis. BC is used if they are parallel to Y and Z axis.
* no means, there is no control about orientation. Orientation will change by the mechanical properties and can be changed by actuator changes, but it is not managed by firmware. Forward and inverse kinematics ignore orientation values with the exception of angle violations.
* full gives full control over the orientation by using the full three orientation vectors. There is no G-Code standard, quaternions are used using ABCD: A is the real number, BCD are the imaginary numbers.

Examples:
* 4 axis palletized RRRp is no, because orientation is not controllable
* CNC 5 axis e. g. PPPRR is AC or BC, because the 2 rotary axes control the Z axis orientation. XY constantly changes because the drill rotates.
* 3 axis cartesian PPP is no, because the endpoint is always vertical and cannot be changed
* robot 6 axis RRRRRR can be set to different modes: AC, BC if the endpoint has no XY axis information like a hotend or drill. full if orientation of all three axes is important.

# R reporting

R is for future use for reporting functions like to tell about singularity regions.

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

In RRF, XYZUVW are linear axes by default and ABC rotational axes. This corresponds to CNC conventions. The defined axes for robot kinematics should be clarified as prismatic or rotational with the M584 settings, R0 meaning prismatic/linear and R1 meaning revolute/rotational. The reason is, RRF uses this information for some calculations like the distance calculation and uses different algorithms for prismatic and rotational axes. This clarification is only needed if the used letters differ from the default assignment.

# G10: tool offset
At the end of the last axis, a tool is attached. The tool is defined as last Dn setting. The Dn values are taken and G10 offsets of the currently selected tool is added to it:
* X, Y, Z are the tool's offsets in mm. Default is 0, 0, 0.

The signs of the offsets are important and depend on tool's coordinate system (explained in the DH document).
Example: Z is positive and Z axis of the coordinate system of last Dn points to (0 0 -1) downwards with a common head-table configuration, then Z will lower the distance between head and table.

# M208 limits
M208 limits the allowable cubic area by setting X, Y, Z limits. Printing is only allowed inside this area (an execption is while homing). With 5 axis robots (AC or BC), the letters A, B, C can also be specified. In this case, the limit is not a cartesian coordinate, but the A, B, C angles in degrees. This is redundant to The first and second An parameter.

The robotic print area is not cubic in most cases, so the workspace differs from the M208 setting. Configuration can set M208 too small or too big:
* setting too small to a safe, always printable area
* setting too big around the workspace. Kinematics does two check: whether desired print is inside M208 limits and whether it is reachable by the arm lengths and allowed angles. If M208 is possible, but not according to the true workspace, an error will be reported. Whether a partial print will be done, depends on the printer type (3D printing mode will print partially, CNC mode not).

# angle limits: M208, A"..."

Additionally the M208 limits, the A angle min and max settings limit the allowed movements.

# speed limits: M350, M92, M203, maxVelocity

The following values are important to check speed limits and the reporting of speed violations. Movement speeds will be lowered or hindered if the motor speeds are too high.

* M92 are steps/mm for prismatic and steps/degrees for rotary axes, where the step number of a stepper, the M350 microsteps, gear ratio and pulley teeth are considered. Example 200-step stepper, 16 microsteps,1:3 ratio gear and 20 teeth of pulley are 200 * 16 * 3 / 20. The M92 values must be correct for calculations.
* M203 is the mm/min for prismatic and degress/min for rotary axes. An industrial robot like Kuka has typically 180 degress for one second, i. e. M203 would be 180 * 60 = 10800. The "big axis" from 1 to 3 are slower, the axes 4 to 6 are often faster, sometimes doubled speed. This makes sense, as axes 1 to 3 have to rotate more mass than axes 4 to 6. Accelerations will differ accordingly.
* a typical prismatic M203 could be 200 mm/s * 60 s = 12000

If a movement has a velocity too high for one of it's axes, it is currently reported on the console, but the movement is currently not hindered. This will be changed in productive use. A too high velocity is a signal of approaching or reaching a singularity or if the extrusion speed is set too high compared to the normal axis speeds. In most cases, however, extrusion speed is not the limiting factor.

# Homing
When the Duet controller starts, it has no knowledge about the stepper positions. By a procedure called homing it gets those values. The common procedure of homing is that movements trigger endstops and the values are set for each axis when the endstop is triggered. After homing, the endstops and homing procedure is not necessary any more.

If there are An homing and limit defintions for a drive, those values are used. If they are not defined, M208 values are used. If both are not defined, the status will remain at unhomed for this axes and normal movements are not possible.

Some thoughts about homing
* removing endstops and probes after homing and mesh compensation can be considered to avoid collisions with workpiece/print object
* homing by G92: if the position is known by other means, e. g. an absolute encoder, camera, interferometer etc., the position can be set by G92 directly. G92 stores the value and sets homing status to set without an explicit trigger signal
* homing can be outside the given M208 or angle limits and can even be in a singularity (the first example on the DH page is a singularity). After homing, the axes should be moved into a valid position and orientation before starting "normal operation", because otherwise G1 moves will be classified as non-reachable
* homing angles are specified in the third M669 A parameter. The values are mm for prismatic (linear) axes and degrees for rotational ones
* the current stored stepper position can be checked with the Count values of M122. To calculate stepper degrees or mm position from it, the M92 value must be included: rotational axis degrees * steps/degrees or prismatic mm * steps/mm. The starting value is the homing value and stepper's position when it was set.
* homing procedures by using G1 H1 setting to M208 limits will not work in most cases, because M208 limits are cartesian coordinates, while the axes are not behaving linearly, but often are rotational axes. That's the main reason to clearly specify home angles inside An
* the default homing file is called homeRobot.g

# Mesh compensation
Mesh compensation is a feature to handle uneven print beds und allow printing with good adhesion by printing the first layers in sync to the unevenness of the bed. It is used a probe to record the unevenness data, which has in most cases an XYZ offset from the hotend. The offset may not change while measuring, because the firmware calculates the hotend position from the probe offset and stores the hotend positions in the mesh file.

After the mesh is measured and stored, the probe is not needed anymore. To avoid collision with the print object later when the hotend tilts (and with it the probe), a mechanical removal of the probe or a save distance in Z direction should be considered when nonplanar printing or drilling is used.

# Configuration first testing
When configuration is stored and Duet rebooted, the following procedure shall avoid damages:
* when Duet reboots or is powered off, the motors lose current and the arms may fall down (for protection, consider brakes, weight balance, gear friction, detent torque, springs, counterweights)
* G91 G1 H2 X1 to check whether first axis rotates into the expected direction and it is the correct axis. Use low values in case the M92 setting is wrong. Repeat with the other axes. Check letter assignments, positive and negative angles interpreted as expected, and whether rotation degrees are correct.
* G91 G1 H2 Xn with bigger values to assure that the M92 settings are correct
* home the individual axes and assure that the endstops are triggered. M114 Count values can be used to check the stored motor position value for the homing position.
* with normal G1 moves, check that the coordinates are interpreted correctly, X positive being to the right, Y positiv to behind and Z positive means greater distance between hotend and bed. This step is especially important for setups with the print object moving (workpiece mode).

# Glossary

I'll use some abbreviations in the documents and explain it here.

|-|-|
|GA|geometric algebra|
|CGA|conformal geometric algebra, G4,1 geometry|
|PK1 to PK...|Paden-Kahan subproblems (for rotary axes), extended by newer research|
|PG1 to PG...|Pardos-Gotor subproblems (mainly for linear axes)|
|Pos/Ori|position and full orientation with 3-value vector for position and 3 axes x, y, z with 3 values each|
|GSt(0)|defined end position for given angles/positions|
|noap|nick-...-approach-position of endposition|
|DH|Denavit-Hartenberg system to describe configuration|
