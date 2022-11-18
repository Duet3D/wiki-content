---
title: Robot Object Model
description: description of the object model, explaining some internal workings also
published: true
date: 2022-11-18T12:19:34.501Z
tags: robot
editor: markdown
dateCreated: 2022-10-15T05:16:12.719Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

# Object Model

The following description allows manipulating parameters of robot kinematics directly. Care should be taken for
* parameters which are connected
* parameters which change other parameters in normal operation
* values which are cached

Those special cases will be mentioned when describing the object model.

The object model is built with the structure of a double list: main topics and sublists each.

The following is only the description of the robot kinematics specific object model elements. The general object model is described in the general Duet documentation, e.g. in https://github.com/Duet3D/RepRapFirmware/wiki/Object-Model-Documentation

The values of the object model can be called by:
m409 K"move.kinematics"

# Fixed parameters

There are values which are not part of the object model, but are constants which need recompilation of firmware when changed:

* RRFMODE=1 means the code runs in RRF, 0 if not
* WINDOWSMODE=1 means the code runs in windows, 0 if not
* MAXNUMOFAXES=6 sets the maximum number of actuators, to allow a 6 axis robot at maximum.
* MAXDN = 10 sets the maximum number of Dn definitions, meaning possible values are D0 to D9
* radiansToDegrees value as exact as possible, used to convert radians to degrees
* flt_epsilon constant with same value as FLT_EPSILON from float.h

When defining more than 10 Dn or axes by changing the variables above and recompiling, the numbers may become two-digit. This will currently not work correctly.

# robotType

Setting B"robotType=..." sets a couple of parameters. The original call is saved in a string robotType. The depending parameters are: numOfAxes, mapDriveLetterDn, orientationType, Dn, connectedDns and more for specific types, so changing it directly is not advisable.

robotType is important for the firmware for special handling of some kinematics and AC / BC axes.
* CoreXY/XZ are recognized by robotType Core...
* AC, BC 5 axis types are recognized by those letters in the robotType name
* 4 axis palletized is recognized by the axisTypes p letter

The Dn parameters are set at default values by robotType to help setup, but must be changed in most cases to reflect printer properties like arm lengths.

# axisTypes, numOfAxes

Sets the axis types as described in the main document. Depending parameter: numOfAxes, counting the number of the axes. A passive parallel axis is counted (p axis of 4 axis palletized). It is very important that numOfAxes is correct, so when changing axisTypes directly, numOfAxes must be changed also.

# isAC, isBC, isFull, isNoOri

This bool values store information about how orientation information is handled. One value is true, the others false.
* isAC means zaxis mode is used with A axis being parallel to the X axis and C parallel to Z
* isBC means zaxis mode with B parallel to Y and C to Z
* isFull means using all three coordination axes and specified with quaternions real-imag-imag-imag values as ABCD
* isNoOri means orientation is ignored

# mapDriveLetterDn

Assigns drive numbers (in the sense of the M584) to the Dn number.

The format is drivenumber-letter-Dn-colon, e.g. "0X3:1Y4" to define drive 0 as letter X and using D3 and drive 1 as Y using D4.

# workingMode, workingModeValues

The robot is in a specific work mode, which it can only escape by crossing a singularity. The current workmode is stored in two variables:
* workingMode=1 if home angles are used, set by P"workingMode=home"
* workingMode=2 if specific angles are used, set by P"workingMode=list of angles"
* workingModeValues hold the current work mode angles. They are the home angles for workingMode 1 and are explicitly set in case of workingMode 2.

To change a work mode, the robot arms must be moved outside normal operation to the new work mode, e. g. by G1 H2 commands. Then the new workingMode can be set and new movements executed.

# dn, dnActiveInv

The double array dn holds the 6 Dn values each (ztr (DH: d), zrot (DH: theta), ytr, yrot, xtr (DH: a), xrot (DH: alpha)).

dnActiveInv is a variable to held bitwise information which Dn-s are used. The bits are stored pairwise:
* bit0 for D0 (and 2 for D1, 4 for D2, ...) 1 means Dn is active, 0 means not
* bit1 for D0 (and 3 for D1, 5 for D2, ...) 0 means normal, 1 means inverted

# connectedDns, coreXYRatioZ

For parallel arms, those special parameters store the configuration:

* connectedDns are the connected Dn for CoreXY by storing the positions as flags. 5BarParScara has the 4 or 5 (when using cantilevered) Dns flagged.
* coreXYRatioZ stores the Z ratio for CoreXZ and CoreYZ types (default 3)

The binary flag is calculated by (variable |= (1<< Dnnumber))

# an, continuousAxis

an holds the Angle (or mm position for a prismatic joint) definitions, 3 values each: min, max and home angle/position.

continuousAxis is an int with binary flags for the axes which are set to continuous. If e.g. the 6th axis is continuous, the 6th bit from right is set to 1. When an axis is flagged as continuous, the first two parameters of an (min, max) are ignored.

# quality

* quality=5 is the highest level and default
* quality=3 is middle
* quality=1 is lowest quality

quality holds the string settingThe different levels change multiple values:
* precision of segemented move: precision, precisionAngle, segmentLength, segmentsPerSecond
* precision of long moves: longMovePrecision, longMovePrecisionAngle, longMoveSegmentLength
* angleDiff: angle change for calculation of jacobian matrix
* maxIterations: how many iterations max to find a solution
* lowValuesZero: used to round values near 0, 1, -1 in some orientation calculations

# logLevel

* logLevel=0 means no special logging. Default
* logLevel=1 will log performance and debug information to the console

The logLevel is independent from the debugging options of the core RRF.

# starttime

Start time for logging performance measurements.

# cachedAngles

The angles (for rotational axes) or positions (for prismatic axes) of the last move are cached. They are used for the calculation of a new target. Homing sets this cache, as well as a segmented move. The calculation whether a target is reachable (LimitPosition) does not set or change cachedAngles.

# HomeRobotFileName

Name of the homing file to home all axes, default is homeRobot.g
