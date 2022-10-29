---
title: Configuring RepRapFirmware for a Robot printer
description: 
published: true
date: 2022-10-29T06:17:36.552Z
tags: robot
editor: markdown
dateCreated: 2022-03-03T13:05:06.424Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

The kinematics is developed for Duet3Ds RepRapFirmware and will be included in 3.5. The **robot firmware is currently in development**.

> I've currently removed the github repository to transfer it all to a new fork. It will be available soon again.
> 
{.is-info}

The robot is dicussed in the Duet forum at: [robot thread](https://forum.duet3d.com/topic/17421/robotic-kinematics/285) and in a few additional forum threads about robot prototypes.

Current status, last actions:
* some settings moved to B"robotType", so P"closedChain" is not necessary any more. CoreXY and CoreXZ with Z parameter
* removed Dn from robotType, because mapDriveLetterDn will be needed in most cases anyway
* new documentation page about object model
* added Z parameter for CoreXZ
* to finish the current release for a stable version, I postpone the following capabilities to the next release: 5BarParScara, handling continuous axis, complete object model, most robotType templates (draft versions are created)
* added P parameter workingMode and documentation on the robot firmware page about the meaning of work modes
* BC mode should work correct now
* robotType submodes, especially for 5 axis types
* logLevel2 statistics

# Configuring a Robot printer

When Duet starts after power on, it needs to know how to behave. Reading the file config.g and some associated files, the firmware is set to specific configurations like setup of the steppers, arm lengths, endstops and homing positions, heaters etc. M669 is at the core to define robot kinematics behaviour, accompanied by other settings, which are described in this document.

The robot kinematics supports different types. Roughly, they can be separated into
* serial robots: the joints and arms are connected in one serial chain, called open chain kinematics. Industrial robots (6 rotational axes), CNC 5 axis (3 prismatic, two rotational axes), cartesian (three prismatic axes), serial scara (one prismatic and two rotational axes) and polar (two prismatic and one rotational axes) printers are examples. Configuration allows mixing any prismatic and rotational axes, e.g. a cartesian with spheric head (3 prismatic and 3 rotational axes).
* parallel robots: the joints and arms are completely or partially connected in parallel. Often some joints are without an actuator. The kinematic is more difficult to calculate and need dedicated formulae, so only defined types are supported. Examples are delta, 5 arm parallel scara, stewart/hexapod, 4 axis palletized robot. Delta is not supported in robot kinematics, because RepRapFirmware has dedicated delta support. 4 axis palletized is supported, stewart is planned, 5 arm parallel scara has dedicated RepRapFirmware support.

# M669 configuration
M669 and its parameters are used to define the robot properties like arm lengths and type of axes.

The first M669 line must specify the K13 type. Often is also useful to specify the B"robotType":
M669 K13 B"robotType=5AxisAC"

This will set defaults for some core settings.

M669 without parameters or with K13 alone will output the current settings to the console. M409 K"move.kinematics" displays most parameters as object model.

Multiple settings of the same starting letter must be on separate lines, as the G-Code interpreter evaluates only the first one. Different letters can be combined.

Overview
* K13 set robot kinematics and must be defined first
* B"robotType=..." specify robot type
* A"a:..." minimum, maximum and home angles
* D"n:..." Denavit-Hartenberg (DH) parameters
* P"axisTypes=..." specify types of joints
* P"mapDriveLetterDn=..." assign drive number to Dn
* P"orientationType=..." set mode of orientation calculation
* P"workingMode=..." set working mode
* P"quality=..." set quality level
* P"logLevel=..." set log level
* S segments per second
* T minimum segment length in mm

Most changes in config.g don't need a reboot, but when a drive or letter assignments change, a reboot is probably necessary.

# M669 B parameter: robot type

**B"robotType=type[:parameters]"**

Currently, valid values for the type and parameters are:
* 6Axis
* 5AxisAC, 5AxisBC (=> CNC 5 axis, Pentarod, Open5x)
* CoreXYAC, CoreXYBC, CoreXZAC[:Zf], CoreXZBC[:Zf]
* 4AxisPall, 4AxisPallInv (=> IRB 460 like)
* 5BarParScara, 5BarParScaraAC, 5BarParScaraBC (experimental)
* leaving empty: use only Dn, P, A and other parameters

parameters for the AC, BC types:
* table-table for AC or BC being located at the table. In table-table mode, C is assemled on top of A/B. If not, Dn must be changed individually afterwards.
* head-table for A or B being located at the head and C at the table

parameters for CoreXZAC and CoreXZBC: (as shown above)
* Zf for the ratio parameter like described in M669 K2 kinematics

parameters for CoreXYAC or CoreXYBC: 
* ZatTable the Z axis moving the bed
* ZatHead the Z axis moving the head similar to CNC gantry machines

Not being on the list doesn't mean that a robot type is not supported. E.g. polar kinematics, serial Scara, cartesian with 3 axes spherical head and many more are all supported by specifying the D and P parameters individually. robotType is left empty in this case. Some kinematics already exist as dedicated kinematics. Robot kinematics for them is meant as an additional option, not as replacement.

The task of robotType is to set some default parameters, but it is also important to decide which arms are connected as parallel and which drives are e. g. AC. For example, together with information of mapDriveLetterDn, firmware knows to which drive and Dn A is connected, and which drives are the connected actuators for CoreXY/XZ/5BarParScara/Palletized.

The default settings of robotType can be overwritten by P"..." parameters. B"robotType" should be specified first, otherwise P parameters will be overwritten.

The robot types are described in detail on dedicated pages, please see the robot tag overview. CoreXY 5 axis and 5BarParScara are described on the CNC 5 axis page.

Example:
* B"robotType=CoreXYAC" specifies CoreXY and the rotary axes to be AC, which means A is parallel to the X axis and C to the Z axis. More details of the 5 axis types are described on a dedicated page.
* B"robotType=CoreXZAC:Z2.5" sets CoreXZ with reduction ratio for Z to 2.5. Default for CoreXZ/CoreYZ is Z3.
* B"robotType=CoreXYAC:head-table is a configuration with A axis at the head and the rotating C plate near the workpiece.

# M669 A parameter: angles

**A"axis:min:max:home"**
**A"axis:cont:home"**

**I've changed to start from 0 now. This is analogue to the drive number.**

* axis is the axis number, starting with 0
* min is the minium angle for rotary axis and minimum position in mm for prismatic axis
* max is the maximum angle or position
* cont means the axis is continuous and has no min/max angles
* home is the home position in degrees or mm. The value can be outside min and max, the endstop can be low or high type

Currently there is no continuous axis in RRF core, but when it exists, this setting will ignore angle limits.

Example:
* A"0:-180.0:180.0:0.0" means the axis 1 can rotate between -180 and +180 degrees and when while homing the endstop is triggered, the motor position is set to 0.0 degrees (or mm, if it's a prismatic axis)
* A"5:cont:0" means the axis is continuous and the homing angle is 0 degrees or 0 mm. In most cases, this is only possible if no electronics or filament is attached to the rotating element. The C rotary axis of CNC 5 axis could be cont e.g.

# M669 D parameter: Denavit-Hartenberg
Dn define DH parameters and are numbered from 0 to maximum 9.

There is a separate document about DH parameters with examples. The DWC plugin RobotViewer shall help with configuration.

The standard usage is:
* D0 is optional the definition of the base. If the first axis is vertical starting in 0,0,0, D0 can be omitted
* D1 to D6 are DH parameters with actuators assigned (or less numbers for less actuators)
* D7 for tool. Offset values of the current selected tool G10 will be added

For less (more) actuators, less (more) D-s are used. The p (parallelogram 4 axis) has its own Dn and if it has values, they are added to the parallelogram angles.

The standard can be changed with the B parameter and Dn numbers can have holes, e.g. start by 1 without 0.

Every Dn contains three translates and three rotations by Z, Y, X axis in this order. The parameters are explained in detail on the DH Parameter documentation page.

Original set of DH parameters:
**D"n:d:theta:a:alpha"**

* the original DH definition define Z axis and X axis translate and rotate each, but no Y axis change
* n are unique integer numbers starting from 0
* d displacement in Z direction
* theta rotation by Z axis, added to the variable theta angle
* a is the shortest distance between Z and former Z axis. If alpha is 0, +-90 or +-180 degrees, the distance is the arm length
* alpha is the X axis rotation
* the internally used ytr and yrot values are set to 0.0 each

Extended set with addition Y parameters:
**D"n:d:theta:ytr:yrot:a:alpha"**

* values meaning as above
* additionally, ytr and yrot define displacement and rotation/translate around the Y axis

The two versions can be mixed, e. g. using the short version if ytr, yrot is 0.0 each.

**D"!n:..."**
Same as above, but inverts the transformation. Inverts rotations and translations. This is used for workpiece mode and explained in a world mode vs. workpiece mode chapter.

Example:
* D"1:100.0:0:0:90.0" means DH 1 displacement by 100 mm in Z axis direction and a rotation of the coordinate system by +90 degrees of the X axis
* D"6" without values clears the definitions of D6 and removes D6 from the chain
* D"7:0:0:0:0" if D7 is the last defined Dn. Then it is the definition of the tool, G10 offsets will be added before calculating forward kinematics. D7 values of d, ytr or a will be added to the G10 offsets.
* D"!1:100.0:0:0:0" inverts the transformation matrix.

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
**P"mapDriveLetterDn=0X1:1Y2:2Z3:p4"**
The first number is the drive number, the second drive letter and the third the Dn number. A parallelogram axis has no actuator, so the first number is omitted and pn is used.
* if this parameter is not set, it is expected that the first drive is used at D1, second at D2 etc. and the letters are standard XYZABC (or XYZUVW) for 6 axis, XYZ for 4 axis pallet, XYZAC for CNC 5 axis AC type.
* maps drive number with drive letter with Dn, in the first example the first drive called X is mapped to D3
* the parallel axis of the 4 axis palletized robot is named pn, e.g. second example p is assigned to D4
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

**P"workingMode=home|a1:p2:a3:etc"**

Working modes are explained in https://docs.duet3d.com/en/User_manual/Machine_configuration/robot_firmware chapter "Working modes". The work mode can be changed by
* home sets the workmode to the angle combinations of the homing angles. This is the default and startup situation after changing to robot kinematics.
* list-of-angles is a colon separated list of degrees for rotary axes and mm for linear/prismatic axes. The list size must match the count of actuators, including a palletized axis (the palletized axis's angle will be ignored). If a value is not specified, the homeing value will be taken.

The work mode can be changed by e. g. G1 H2 moves and then setting to the current position by issuing the P command with the current values. Only setting the new P values without previous moving will probably fail, because the singularity cannot be crossed in most cases.

Example:
* P"workingMode=10:20:30:-20:0" for CNC 5 axis AC will set XYZ positions to 10, 20, 30 mm, the A axis to -20 degrees and the C axis to 0 degrees as starting point.
* P"workingMode=:::-20:0" same, but the prismatic positions will be set to the homing values and only A and C are specified.
* P"workingMode=home" will set the starting point to the values of the An... third parameters of each axis. The starting point will be stored when the An parameters are completely specified.

**P"quality=1|3|5"**
* n can be 1, 3 or 5. 1 is lowest quality, 5 highest, default is 5.

Slow and high quality means the algorithms takes more time to calculate exact results. Quality can be changed anytime between moves, e. g. to print specific object details with higher quality.

The following properties will be changed:
* allowed maximum number of iterations to achieve the precision goal. When it aborts, firmware takes the best result achieved (lowest position and orientation error)
* required precision for position and orientation

Testing so far has shown, that altering iterations has nearly no effect. After 5 iterations, all tests have approached the target, with the exception of singularity areas, where the iterations is interrupted, because angles go havoc. Changing precision requirement has some effect on time required to calculate.

**P"logLevel=0|1|2"**
Turning on logging for performance measuring or debugging.
* log results are output to console
* default is P"logLevel=0"
* logLevel=1 will report performance and important information
* logLevel=2 will additionally report detailed position and orientation information about every chain step and other data like parameters of the calls to forward and inverse kinematics. It will reduce performance a lot.

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

# M208: limits
M208 limits the allowable cubic area by setting X, Y, Z limits. Printing is only allowed inside this area (an execption is while homing). 

The robotic print area is not cubic in most cases, so the workspace differs from the M208 setting. Configuration can set M208 too small or too big:
* setting too small to a safe, always printable area
* setting too big around the workspace. Kinematics does two check: whether desired print is inside M208 limits and whether it is reachable by the arm lengths and allowed angles. If M208 is possible, but not according to the true workspace, an error will be reported. Whether a partial print will be done, depends on the printer type (3D printing mode will print partially, CNC mode not).

# Homing
When the Duet controller starts, it has no knowledge about the stepper positions. By a procedure called homing it gets those values. The common procedure of homing is that movements trigger endstops and the values are set for each axis when the endstop is triggered. After homing, the endstops and homing procedure is not necessary any more.

Some thoughts about homing
* removing endstops and probes after homing and mesh compensation can be considered to avoid collisions with workpiece/print object
* homing by G92: if the position is known by other means, e. g. an absolute encoder, camera, interferometer etc., the position can be set by G92 directly. G92 stores the value and sets homing status to set without an explicit trigger signal
* homing can be outside the given M208 or angle limits and can even be in a singularity (the first example on the DH page is a singularity). After homing, the axes should be moved into a valid position and orientation before starting "normal operation", because otherwise G1 moves will be classified as non-reachable
* homing angles are specified in the third M669 A parameter. The values are mm for prismatic (linear) axes and degrees for rotational ones
* the current stored stepper position can be checked with the Count values of M122. To calculate stepper degrees or mm position from it, the M92 value must be included: rotational axis degrees * steps/degrees or prismatic mm * steps/mm. The starting point is the homing value and stepper's position when it was set.

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
