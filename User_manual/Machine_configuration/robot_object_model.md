---
title: Robot Object Model
description: description of the object model, explaining some internal workings also
published: true
date: 2022-10-30T10:28:02.439Z
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

When defining more than 10 Dn or axes, the numbers become two-digit. This will currently not work correctly.

# robotType

Setting B"robotType=..." sets a couple of parameters. The original call is saved in a string robotType. The depending parameters are: numOfAxes, mapDriveLetterDn, orientationType, Dn, connectedDns and more for specific types, so changing it directly is not advisable.

robotType is important for the firmware for special handling of some kinematics and AC / BC axes.
* CoreXY/XZ are recognized by robotType Core...
* AC, BC 5 axis types are recognized by those letters in the robotType name
* 4 axis palletized is recognized by the axisTypes p letter

The Dn parameters are set at default values by robotType to help setup, but must be changed in most cases to reflect printer properties like arm lengths.

# axisTypes, numOfAxes

Sets the axis types as described in the main document. Depending parameter: numOfAxes, counting the number of the axes. A passive parallel axis is counted. It is very important that numOfAxes is correct, so when changing axisTypes directly, numOfAxes must be changed also.

# orientationType, isAC, isBC, isQuaternion, isNoOri

This enum holds the information about how orientation information is handled: no, zaxis or full. Internal calculations are always full calculations with rotation matrices, but the decision whether a target position and orientation is met, depends on the orientationType. This is explained in detail on the firmware page.

If orientationType is zaxis, the isAC means AC is used, isBC means BC is used. IJK mode is not implemented.

If orientationType is full, the isQuat uses quaternions in G-Code, isEulerAxis uses Euler axis and angle in G-Gode.
* Quaternions are described by imaginary part as ABC and real part as D.
* Euler Axis is desribed by ABC and angle by D. Euler Axis is different from Euler Angles. Euler axis is like IJK, while Euler angles is one of 12 possible 3-step-angle rotations (ZYX, ZYZ etc).

# mapDriveLetterDn, dnDrive

Assigns drive numbers (in the sense of the M584) to the Dn number. This parameter changes dnDrive, which must be changed as well.

dnDrive is a mapping between drive number and Dn: each array element is a Dn, containing the drive number or 99 for palletized axis or -1 if no drive assigned.

# workingMode, workingModeValues

Specifies currently to be used working mode. Holds the value which is set by the M669 P"workingMode=..." parameter. The value is used for inverse kinematics as starting point, e. g. to decide in method LimitPosition whether a desired target is reachable. It is also a fallback solution where to start calculations in case something goes wrong.

workingModeValues hold the values. This values for homing values can only be set when the An parameters has been specified completely.

# dn[][], dnActiveInv

The double array dn holds the 6 Dn values each (ztr (DH: d), zrot (DH: theta), ytr, yrot, xtr (DH: a), xrot (DH: alpha)).

dnActiveInv is an array with an element for each Dn holding the information whether the Dn is active (value 1), inverted (value -1) or not used (value 0).

When changing dn directly, it must be synchronized with dnActiveInv.

# connectedDns, coreXYRatioZ

For parallel arms, those special parameters store the configuration:

* connectedDns are the connected Dn for CoreXY by storing the positions as flags. 5BarParScara has the 4 or 5 (when using cantilevered) Dns flagged.
* coreXYRatioZ stores the Z ratio for CoreXZ and CoreYZ types (default 3)

The binary flag is calculated by (variable |= (1<< Dnnumber))

# an[][], continuousAxis

an holds the Angle (or mm position for a prismatic joint) definitions in a double array, 3 values each: min, max and home angle/position.

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

* logLevel=0 means no special logging and is default
* logLevel=1 will log main data like time needed for move calculations
* logLevel=2 will contain much more information like calculation results of the chain steps to find the reason for unexpected calculation results. logLevel will greatly reduce performance.

The logLevel is independent from the debugging options of the core RRF.

# start, stop, MAXTIMERS

Timer start, stop, maximum array size to hold values for performance measuring while the code is running, using the chrono C++ library.

# cachedAngles[]

The angles (for rotational axes) or positions (for prismatic axes) of the last move are cached. They are used for the calculation of a new target. Homing sets this cache, as well as a segmented move. The calculation whether a target is reachable (LimitPosition) does not set or change cachedAngles.

# HomeRobotFileName

Name of the homing file to home all axes, default is homeRobot.g
