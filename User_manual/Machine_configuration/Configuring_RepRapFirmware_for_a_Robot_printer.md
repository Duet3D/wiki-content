---
title: Configuring RepRapFirmware for a Robot printer
description: 
published: true
date: 2022-09-05T07:16:21.369Z
tags: robot
editor: markdown
dateCreated: 2022-03-03T13:05:06.424Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

The kinematics is developed for Duet3Ds RepRapFirmware and will be included in 3.5. The **robot firmware is currently in development**.

The robot is dicussed in the Duet forum at: [robot thread](https://forum.duet3d.com/topic/17421/robotic-kinematics/285) and in a few additional forum threads about robot prototypes.

# Configuring a Robot printer

When Duet starts after power on, it needs to know how to behave. Reading the file config.g and some associated files, the firmware is set to specific configurations like setup of the steppers, arm lengths, endstops and homing positions, heaters etc. M669 is at the core to define robot kinematics behaviour, accompanied by other settings, which are described in this document.

For specific robot types, example configurations and explanation of specific settings, please see the document about robot types.

# M669 configuration
M669 and its parameters are used to define the robot properties like arm lengths and type of axes.

M669 K13 B"axisTypes=R|P|p" must be the first M669 line.

M669 without parameters will output the current settings to the console.

> tbd check: maybe D1 D2 ... must be in separate lines.
{.is-warning}

Overview
* K13 set robot kinematics and must be defined first
* D Denavit-Hartenberg (DH) parameters
* A minimum, maximum and home angles
* B axis types, specific settings
* P preferences
* Q quality
* S segments per second
* T minimum segment length in mm

Most of the parameters can be changed by accessing the object model also. Most changes in config.g don't need a reboot, but when a drive or letter assignments change, a reboot is probably necessary.

# M669 D parameter: Denavit-Hartenberg
Dn define DH parameters and are numbered from 0 to maximum 9. In most cases, D0 is the base, D1 to Dx are DH parameters for the x axes and D(x+1) are the tool DH properties. The Transformations are processed in the order starting from 0 to highest number, the numbers may not have gaps.

Every Dn contains three translates and three rotations by Z, Y, X axis in this order. The joint parameters are pairwise descriptions of translate in mm and rotate in degrees for the Z[Y]X coordinate axis.

Original set of DH parameters:
**Dn:d:theta:a:alpha**

* the original DH definition define Z axis and X axis translate and rotate each, but no Y axis change
* n are unique integer numbers starting from 0
* d displacement in Z direction
* theta rotation by Z axis, added to the variable theta angle
* a is the shortest distance between Z and former Z axis. If alpha is 0, +-90 or +-180 degrees, the distance is the arm length
* alpha is the X axis rotation

Extended set with addition Y parameters:
**Dn:d:theta:ytr:yrot:a:alpha**

* values meaning as above
* additionally, ytr and yrot define displacement and rotation/translate around the Y axis

The two versions can be mixed, e. g. using the short version if ytr, yrot is 0.0 each.

Example:
* D1:100.0:0:0:90.0 means DH 1 displacement by 100 mm in Z axis direction and a rotation of the coordinate system by +90 degrees of the X axis

# M669 A parameter: angles

**Ajoint:min:max:home**

Defining minimun and maximum angles of the joint. Home is the angle which is set when the endstop is triggered. The home angle can be outside min and max.

Example:
* A1:-180.0:180.0:0.0 means the axis 1 can rotate between -180 and +180 degrees and when while homing the endstop is triggered, the motor position is set to 0.0 degrees (or mm, if it's a prismatic axis)

# M669 B parameter: axisTypes, special
**B"axisTypes=[R]|[P]|[p]*"**
defines the type of the axes

* R means rotational/revolute, units are degrees, speeds e.g. degrees/min
* P means prismatic/linear, units are mm
* p (lower case p) means passive joint without actuator with parallelogram (see 4 axis palletized robot type)
* the number of letters define the number of actuator or parallelogram axes

Examples:

* B"axisTypes=RRRRRR" means 6 axis robot with rotational axes
* B"axisTypes=RRP" means serial scara with third axis being prismatic
* B"axisTypes=PPP" means 3 axis cartesian printer with three prismatic axes
* B"axisTypes=PPPRRR" means cartesian printer with additional spheric 3 axis head
* B"axisTypes=PPPRR" means CNC 5 axis with three linear and two rotary axes
* B"axisTypes=RRRp" means 4 axis palletized
* B"axisTypes=RRRpR" means 4 axis palletized with 4th actuator, so 5 axes in total

CNC 5 axis allows many variants. The following dynamic mapping allows to configure them by defining how the forward kinematics is calculated. Inverting transformation matrices or reverting axes is necessary sometimes, as well as changing letter assignments.

**B"mapDriveLetterDn=0X3:1Y4:2Z5:3A1:4C2"**
B"mapDriveLetterDn=0X1:1Y2:2Z3:p4"
* maps drive number with drive letter with Dn, in the first example the first drive called X is mapped to D3
* letters IJK have a special meaning as tool vector
* the parallel axis of the 4 axis palletized robot is named pn, e.g. second example p is assigned to D4
* default is 0X1:1Y2:2Z3:3A4:4B5:5C6 or abbreviated for less defined axes
* every Dn number may be used only once
* different drive numbers may not point to the same Dn
* the order of the elements is not important

**B"dnInvert=0:1:2"**
* the specified Dn transformations will be inverted. It is needed when changing from world to workpiece mode. This will be explained in detail on the page about CNC 5 axis.

**B"revertCoordinates=X:Y:Z"**
* revert axis movement, for cases where the robot moved the object instead of the hotend. This will be explained in detail on the page about 4 axis palletized robots.

# M669 P parameter: preferences
currently not implemented

# M669 Q parameter: quality

Q1 is fast but lowest, Q5 is slow but highest quality of calculation. The time needed to calculate depends on the processor speed. Slow and high quality means the algorithms takes more time to calculate exact results. Quality can be changed anytime between moves, e. g. to print specific object details with higher quality. Default is Q3

# M669 S, T parameters: segmentation

**Sn** Segments per second

**Tn** Minimum segment length (mm). Default is 0.1 mm

G1, G2, and G3 moves are separated into segments, which are executed as straight lines. The length of the segments is controlled by the S and T parameters. More segments give better results, but at the cost of processing time to calculate them.

# M584 R0, R1: axis type

In RRF, XYZUVW are linear axes by default and ABC rotational axes. This corresponds to CNC conventions. The defined axes for robot kinematics should be clarified as prismatic or rotational with the M584 settings, R0 meaning prismatic/linear and R1 meaning revolute/rotational. The reason is, RRF uses this information for some calculations like the distance calculation and uses different algorithms for prismatic and rotational axes. This clarification is only needed if the used letters differ from the default assignment.

# G10: tool offset
At the end of the last axis, a tool is attached. The robot's kinematics is calculation with the G10 offsets of the currently selected tool:
* X, Y, Z are the tool's offsets in mm. Default is 0, 0, 0.

Offsets are included in the calculation of the XYZ position. The signs of the offsets are important and depend on tool's coordinate system (explained in the DH document).

Tool tilt or rotated tool can be defined through the DH parameters. The last DH definition is the tool definition, the tool tip is called endpoint. G10 XYZ offsets are added to the DH values.

# M208: limits
M208 limits the allowable cubic area by setting X, Y, Z limits. Printing is only allowed inside this area (an execption is while homing). 

The robotic print area is not cubic in most cases, so the workspace differs from the M208 setting. Configuration can set M208 too small or too big:
* setting too small to a safe, always printable area
* setting too big around the workspace. Kinematics does two check: whether desired print is inside M208 limits and whether it is reachable by the arm lengths and allowed angles. If M208 is possible, but not according to the true workspace, an error will be reported. Whether a partial print will be done, depends on the printer type (3D printing mode will print partially, CNC mode not).

# Homing
The homing angles are specified in the M669 A parameter and can be impemented e. g. by endstops between joint's axes or by reading absolute encoder positions. G1 H1 will set to the homing angle which is defined with the A parameter when the endstop is triggered. If necessary, the value can be changed later by G92. For a rotational axis, the value the stepper angle * microsteps * gear ratio must be taken. The current stepper position can be checked by calling M122, it is the nth count value.

If the homing position is in a singularity or near it, after homing the robot arms should be rotated away from it (this can be done with G1 H2 moves), before starting normal operation.
# Mesh compensation
Mesh compensation is a feature to handle uneven print beds und allow printing with good adhesion by printing the first layers in sync to the unevenness of the bed. It is used a probe to record the unevenness data, which has in most cases an XYZ offset from the hotend. The offset may not change while measuring, because the firmware calculates the hotend position from the probe offset and stores the hotend positions in the mesh file.

Some robot setups can assure the constant XYZ offset between probe and hotend, some not:

* 6 axis robot can use M669 P2 mode to change the hotend orientation to stay parallel to XY axes
* a mechanical solution of a horizontal parallelogram or other means can fix orientation
* the probe can be installed at XY 0,0 position, e. g. under the hotend
* a toolchanger can load a probe instead of a tool and measure at 0,0

After the mesh is measured and stored, the probe is not needed anymore. To avoid collision with the print object later when the hotend tilts (and with it the probe), a mechanical removal of the probe or a save distance in Z direction should be considered.

# Configuration first testing
When configuration is stored and Duet rebooted, the following procedure shall avoid damages:
* when Duet reboots, the motors lose current and the arms may fall down
* G91 G1 H2 X1 to check whether first axis rotates into the expected direction and it is the correct axis. Use low values in case the M92 setting is wrong. Repeat with the other axes. Check letter assignments, positive and negative angles interpreted as expected, and whether rotation degrees are correct.
* G91 G1 H2 Xn with bigger values to assure that the M92 settings are correct
* home the individual axes and assure that the endstops are triggered. M114 Count values can be used to check the stored motor position value for the homing position.
* with normal G1 moves, check that the coordinates are interpreted correctly, X positive being to the right, Y positiv to behind and Z positive means greater distance between hotend and bed. This step is especially important for setups with the print object moving.
