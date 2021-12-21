---
title: GDF1
description: test page 1
published: true
date: 2021-12-21T02:20:48.211Z
tags: 
editor: markdown
dateCreated: 2021-12-21T02:20:48.210Z
---

# Test only
This version is not in date and is used for performance testing only

# Introduction

GCodes are a widely used machine control language. They are human readable and editable. This page describes the RepRapFirmware supported GCodes. RepRapFirmware follows the philosophy of "GCode everywhere", in essence the users or external program's interaction with the firmware should be through GCodes. There are GCodes for all supported control and configuration inputs along with status and debugging information.

RepRapFirmware GCodes were originally based on the information from the [RepRap wiki GCode page](http://reprap.org/wiki/G-code). There are some GCodes listed on that page that are not implemented in RepRapFirmware. More details can be found on the [GCodes not implemented](/User_manual/Reference/Gcodes_not_implemented) page.

# GCode and RepRapFirmware

A typical piece of GCode sent to a machine running RepRapFirmware might look like this (The meaning of these codes (and more) is explained below on this page.)

```
G10 P0 S195 R175
T0
G1 X100 Y100 Z0.3 F3000
G1 X100.4 Y99.3 E0.23 F600
...many 1000 more lines...
```

## GCode Everywhere

A design philosophy of RepRapFirmware is "GCode everywhere" what this means is explained in this sub section

The GCode can originate from a number of sources:

* Sent to over USB (for example from [Pronterface](http://www.pronterface.com/))
* Sent by the [Duet Web Control (DWC)](/User_manual/Reference/Duet_Web_Control_Manual) Interface
* Sent by an external controller such as the [PanelDue](/Duet3D_hardware/Accessories/PanelDue)

In all cases the GCode could

* be entered by user one line at time, for example during configuration or testing
* be sent by the User Interface (Pronterface, Web Interface or PanelDue) in response to the user pressing buttons
* originate from [Macros](/User_manual/Tuning/Macros) that are triggered on startup, on certain events (such as error conditions), or called by the user or UI.
* be from a GCode file which are normally stored on the on-board or external SD card.

A key difference from other 3d printer firmwares is not employing a separate command set (other than GCodes) to configure the printer. To that end RepRapFirmware has a large collection of configuration GCodes that allow the behaviour of the machine to be controlled. For some examples of when these GCodes are employed have a look at these wiki pages:

* [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian)
* [Configuring RepRapFirmware for a Linear Delta printer](/User_manual/Machine_configuration/Configuration_linear_delta)
* [Configuring RepRapFirmware for a CoreXY printer](/User_manual/Machine_configuration/Configuration_coreXY)
* [Configuring RepRapFirmware for an IDEX printer](/User_manual/Machine_configuration/Configuration_IDEX)
* [Tuning the heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning)
* [Setting up automatic probing of the print bed](/User_manual/Connecting_hardware/Z_probe_auto_probing)
* [Connecting hobby servos and DC motors](/User_manual/Connecting_hardware/Motors_servos)
* [Controlling unused IO pins](/User_manual/Connecting_hardware/IO_GPIO)
<!--* [Configuring RepRapFirmware for a SCARA printer **UPDATE LINK**]()
* [Configuring RepRapFirmware for a Polar printer **UPDATE LINK**]()
* [Configuring RepRapFirmware for a Hangprinter printer **UPDATE LINK**]()-->

The advantage of "GCode everywhere" is the same commands can be send from any of the GCode sources, and originate from the user, a UI, macro or file and it will generate the same response from the firmware. This greatly improves the ease and power of firmware configuration and operation.

# GCode Structure

This section explains the elements that make up a GCode command.

## Command Order

The general rule on command order in config.g is: don't try to change the parameters of anything that you haven't already created and doesn't exist by default.

For example:

* Don't refer to any axis letter or extruder number that you haven't created using M584 (except that XYZ already exist by default)
* Don't refer to any temperature sensor in M950 or M106 before you create it using M308
* Don't refer to a heater in M307, M140, M141, M143 or m563 before you create it using M950
* Don't refer to a fan in M106 before you create it using M950
* Don't refer to a tool in G10/M568 before you create it using M563

## Comments

GCode comments begin at a semicolon, and end at the end of the line:

```
T0 ; This is a comment
G92 E0
;So is this
G28
```

Alternatively, comments can be enclosed in brackets, but they must start and end on the same line:

```
G28 (here come the axes to be homed) X Y
```

Comments and white space will be ignored by RepRapFirmware when executing the GCode

## Fields

A RepRap GCode is a list of fields that are separated by white spaces or line breaks. A field can be interpreted as a command, parameter, or for any other special purpose. It consists of one letter directly followed by a number, or can be only a stand-alone letter (Flag). The letter gives information about the meaning of the field (see the list below in this section). Numbers can be *integers* (128) or *fractional* numbers (12.42), depending on context. For example, an X coordinate can take integers (**X175**) or fractionals (**X17.62**), but selecting extruder number 2.76 would make no sense. In this description, the numbers in the fields are represented by **nnn** as a placeholder.

In RepRapFirmware 3.01 and later, instead of a number you may use an expression enclosed in braces, for example {2+2}. See [GCode Meta Commands](/User_manual/Reference/Gcode_meta_commands) for details of the supported expression types.

In RepRapFirmware, some parameters can be followed by more than one number, with colon used to separate them. Typically this is used to specify extruder parameters, with one value provided per extruder. If only one value is provided where a value is needed for each extruder, then that value is applied to all extruders.


| Letter | Meaning |
|:---|:---|
| Gnnn | Standard GCode command, such as move to a point |
| Mnnn | RepRap-defined command, such as turn on a cooling fan |
| Tnnn | Select tool nnn. In RepRap, a tool is typically associated with a nozzle, which may be fed by one or more extruders. |
| Snnn | Command parameter, such as time in seconds; temperatures; voltage to send to a motor |
| Pnnn | Command parameter, such as time in milliseconds; proportional (Kp) in PID Tuning |
| Xnnn | A X coordinate, usually to move to. This can be an Integer or Fractional number. |
| Ynnn | A Y coordinate, usually to move to. This can be an Integer or Fractional number. |
| Znnn | A Z coordinate, usually to move to. This can be an Integer or Fractional number. |
| U,V,W | Additional axis coordinates |
| Innn | Parameter - X-offset in arc move (*Not yet implemented in RepRapFirmware*); integral (Ki) in PID Tuning; signal inversion |
| Jnnn | Parameter - Y-offset in arc move (*Not yet implemented in RepRapFirmware*) |
| Dnnn | Parameter - used for diameter; derivative (Kd) in PID Tuning; drive number |
| Hnnn | Parameter - used for heater number in PID Tuning |
| Fnnn | Feedrate in mm per minute. (Speed of print head movement) |
| Rnnn | Parameter - used for temperatures |
| Qnnn | Parameter - not currently used |
| Ennn | Length of filament to move through the extruder. This is exactly like X, Y and Z, but for the length of filament to consume. Where a tool has more than one extruder drive then Ennn:nnn:nnn etc is supported to allow for the individual morement of each to be controlled directly |
| Nnnn | Line number. Used to request repeat transmission in the case of communications errors. Optional |
| *nnn | Checksum. Used to check for communications errors. Optional |

**Case sensitivity**

The original NIST GCode standard requires GCode interpreters to be case-insensitive, except for characters in comments. However, not all 3D printer firmwares conform to this and some recognise uppercase command letters and parameters only.

RepRapFirmware version 1.19 and later is case-insensitive, except for characters within quoted strings. RepRapFirmware version 1.18 and earlier accept only uppercase letters for command and parameter letters.

**Quoted strings**

In RepRapFirmware, quoted strings are permitted anywhere a string parameter is expected. This allows file names, WiFi passwords etc. to contain spaces, semicolons and other characters that would otherwise not be permitted. Double-quote characters are used to delimit the string, and any double-quote character within the string must be repeated.

Unfortunately, many GCode sender programs convert all characters to uppercase and don't provide any means to disable this feature. Therefore, within a quoted-string, the single-quote character is used as a flag to force the following character to lowercase. If you want to include a single quote character in the string, use two single quote characters to represent one single quote character.

Example: to add SSID MYROUTER with password ABCxyz;" 123 to the WiFi network list, use command:

```
M587 S"MYROUTER" P"ABCxyz;"" 123"
```

or if you can't send lowercase characters:

```
M587 S"MYROUTER" P"ABC'X'Y'Z;"" 123"
```

**Checking**

This is an optional feature that is seldom used as GCode files are normally printed form the on-board SD card.

#### N: Line number

Example: N123

If present, the line number should be the first field in a line. For GCode stored in files on SD cards the line number is usually omitted.

If checking is supported, the firmware expects line numbers to increase by 1 each line, and if that doesn't happen it is flagged as an error. But you can reset the count using M110 (see below).

#### *: Checksum

Example: *71

If present, the checksum should be the last field in a line, but before a comment. For GCode stored in files on SD cards the checksum is usually omitted.

If checking is supported, the RepRap firmware checks the checksum against a locally-computed value and, if they differ, requests a repeat transmission of the line of the given number.

#### Method

Example: N123 [...G Code in here...] *71

The firmware checks the line number and the checksum.

You can leave both of these out - RepRapFirmware will still work, but it won't do checking. You have to have both or neither though. If only one appears, it produces an error. See [this forum thread](https://forum.duet3d.com/topic/15134/) for an example of usage, in this case sending GCode to the PanelDue port without disabling cheksums.

The checksum "cs" for a GCode string "cmd" (including its line number) is computed by exor-ing the bytes in the string up to and not including the * character as follows:

```
int cs = 0;
for(i = 0; cmd[i] != '*' && cmd[i] != NULL; i++)
 cs = cs ^ cmd[i];
cs &= 0xff; // Defensive programming...
```

and the value is appended as a decimal integer to the command after the * character.

## Conditional execution, loops, and other command words

In RepRapFirmware 3.01 and later, if the line begins with a recognised keyword (optionally preceded by N and a line number, and/or space or tab characters) then that whole line of GCode is interpreted as a meta-command. Recognised keywords are:

**abort elif else if set var while**

See [GCode Meta Commands](/User_manual/Reference/Gcode_meta_commands) for details of these commands.

A line that does not start with one of these keywords must start with command letter G, M or T or be empty apart from white space and comments. Exception: when in CNC or Laser mode, if a line does not start with a G, M or T command but nevertheless has other fields, and the previous line that included a command was a G0, G1, G2 or G3 command, then the previous command will be repeated with values from the new fields. This is to support GCode generated for CNC machines.

## Multiple commands on a single line

RepRapFirmware allows multiple G- and M-commands to be included in a single line. Each occurrence of G or M on the line that is preceded by a space or tab character and is not inside a quoted string or a meta command starts a new command. In RRF 3.2 and later, the space or tab character is not required.

**Important**: a command that invokes a macro file must be the last command in that line of GCode, because any following commands on the same line will not be executed.

## Buffering

RepRapFirmware stores some commands in a ring buffer internally for execution. This means that there is no (appreciable) delay while a command is acknowledged and the next transmitted. In turn, this means that sequences of line segments can be plotted without a dwell between one and the next. As soon as one of these buffered commands is received it is acknowledged and stored locally. If the local buffer is full, then the acknowledgement is delayed until space for storage in the buffer is available. PC host programs rely on this for flow control when the controller electronics does not support device level flow control.

Only the G0 to G3 movement commands are buffered by RepRapFirmware. All other G, M or T commands are not buffered. When M555 P6 is used to select nanoDLP compatibility mode, no commands are buffered.

When an unbuffered command is received it is stored, but it is not acknowledged to the host until the buffer is exhausted and then the command has been executed.

## Filenames and Paths

System macro GCode files are expected in '0:/sys/'. See M505 for how to switch between multiple configurations

User macro GCode files are expected in '0:/macros/'

Job GCode files are expected in '0:/gcodes/' or a sub directory of that.

'0:/' is root of the on board SD card in stand alone mode; the equivalent folder in SBC mode is '/opt/dsf/sd/'.

Long file names (e.g. longer than 8.3 format) are supported, file names with spaces are supported.

Full paths, including all directories and subdirectories are limited to 120 characters.

e.g.:

```
0:/gcodes/0123456789/012.gcode
```

counts as 30 characters

# G-commands

<!-- Example entry
## Gx: Short description

Sentence description if referring to another GCode (example: G0).
See 'Gx: Short description' for usage
Supported/deprecated from firmware version X.XX

**Usage**

Gx Pnnn Xnnn Ynnn Znnn etc

**Parameters**

* **P** First parameter description
* **X** Second parameter description
* **Y** Third parameter description

**Order dependency**

Blah

**Examples**

```
Gx P1 X2 Y3 Z4 ; description of effect
```

**Descripton**

Blah blah

**Notes**

* Blah
* Blah

-->


## G0: Rapid move

Same as G1 except when in Laser and CNC mode, where moves are executed at the maximum feedrate available.
See 'G1: Controlled linear' move for usage.

## G1: Controlled linear move

**Usage**

* RRF2.02 and later, RRF3
  * G0 Xnnn Ynnn Znnn Ennn Fnnn Snnn Hnnn
  * G1 Xnnn Ynnn Znnn Ennn Fnnn Snnn Hnnn

* RRF2.01 and earlier
  * G0 Xnnn Ynnn Znnn Ennn Fnnn Snnn
  * G1 Xnnn Ynnn Znnn Ennn Fnnn Snnn

**Parameters**

* *Not all parameters need to be used, but at least one of XYZEF must be used*
* **Xnnn** The position to move to on the X axis
* **Ynnn** The position to move to on the Y axis
* **Znnn** The position to move to on the Z axis
* **Ennn** The amount to extrude between the starting point and ending point ^1^
* **Fnnn** The feed rate per minute of the move between the starting point and ending point (if supplied)
* **Hnnn** Move type (RRF2.02 and later, RRF3)
* **Snnn** In RRF3, this parameter is used to set laser power, when switched into Laser mode (see [M452](/User_manual/Reference/Gcodes/M452)); its use for defining move type is deprecated, use 'H' parameter instead. In RRF2.02 and later, when switched into Laser mode (see [M452](/User_manual/Reference/Gcodes/M452)), this parameter sets the laser power. When not switched into Laser mode, and always in firmware 2.01 and earlier, it defines the move type (see the description of the H parameter).
* **Rn** Return to the coordinates stored in restore point #n (see [G60](/User_manual/Reference/Gcodes/G60)). Any X, Y, Z and other axis parameters in the command are used as offsets from the stored position. Axes not mentioned are not moved, so use offset 0 for axes you want to restore to the stored value. For example, G1 R0 X0 Y0 Z2 will move to 2mm above the position stored in restore point 0.
* **Pnnnn** (supported only in some builds of RepRapFirmware) IOBITS parameter. Defines the states of output pins while this command is executed. See the M670 command.

^1^Where a tool has more than one extruder drive then Ennn:nnn:nnn etc is supported to allow for the individual movement of each to be controlled directly. This overrides the extruder mix ratio set with M567

**Very important!** If you use M452 to put your machine into Laser mode, when upgrading firmware from 2.01 or earlier to 2.02 or later you must replace all S parameters in G0/G1 commands in homing files etc. by H parameters. This is because S is now used to control laser power, for compatibility with programs that generate GCode files for laser cutters.

#### G0/G1 H and S parameter

The meaning of the H parameter is as follows:

* **H0** no special action (default)
* **H1** terminate the move when the endstop switch is triggered and set the axis position to the axis limit defined by M208. On delta printers, H1 also selects individual motor mode as for H2. Normally used with relative motor coordinates (see G91).
* **H2** Individual motor mode. X refers to the X motor, Y refers to the Y motor, and so on. Normally used with relative motor coordinates (see G91).
* **H3** terminate the move when the endstop switch is triggered and set the axis limit to the current position, overriding the value that was set by M208.
* **H4** terminate the move when the endstop switch is triggered and update the current position (supported in RRF 3.2-b4 or newer)

The meaning of the S parameter has changed over successive versions of RepRapFirmware. It currently sets the laser power when M452 Laser mode is set, but was also used for homing behaviour. See below.

### {.tabset}

#### RRF 3

In **RRF 3**, H parameter controls movement type, S parameter sets laser power with range of 0 to 254 when M452 Laser mode set, otherwise ignored.

| RRF 3, G0/G1 H parameter BEFORE and AFTER M452 Laser Mode. ||
|:------------|----------|
| **Parameter** | **Meaning** |
| G1 Xnnn Ynnn Znnn H0 | Ignore endstops while moving. |
| G1 Xnnn Ynnn Znnn H1 | Sense endstops while moving (ignoring the axis limits). On Delta (only), axis letters refer to individual towers. |
| G1 Xnnn Ynnn Znnn H2 | Ignore endstops while moving. Also ignore if axis has not been homed. On Delta and Core XY, axis letters refer to individual towers. |
| G1 Xnnn Ynnn Znnn H3 | Sense endstops while measuring axis length, setting the appropriate M208 limit to the measured position at which the endstop switch triggers. |
| G1 Xnnn Ynnn Znnn H4 | Sense endstops while moving, update the current position at which the endstop switch triggers (supported in RRF 3.2-b4 or newer). |


| RRF 3, G0/G1 S parameter BEFORE M452 Laser Mode. ||
|:------------|----------|
| S parameter is ignored ||


| RRF 3, G0/G1 S parameter AFTER M452 Laser Mode. ||
|:------------|----------|
| S parameter sets laser power with range of 0 to 254. ||

#### RRF 2.02 to 2.05.1

In **RRF 2.02 to 2.05.1**, H parameter controls movement type. S parameter controls movement type BEFORE M452 Laser Mode is set. S parameter sets laser power with range of 0 to 254 AFTER M452 Laser mode set.

| RRF 2.02 to 2.05.1, G0/G1 H parameter BEFORE and AFTER M452 Laser Mode. ||
|:------------|----------|
| **Parameter** | **Meaning** |
| G1 Xnnn Ynnn Znnn H0 | Ignore endstops while moving. |
| G1 Xnnn Ynnn Znnn H1 | Sense endstops while moving (ignoring the axis limits). On Delta (only), axis letters refer to individual towers. |
| G1 Xnnn Ynnn Znnn H2 | Ignore endstops while moving. Also ignore if axis has not been homed. On Delta and Core XY, axis letters refer to individual towers. |
| G1 Xnnn Ynnn Znnn H3 | Sense endstops while measuring axis length, setting the appropriate M208 limit to the measured position at which the endstop switch triggers. |


| RRF 2.02 to 2.05.1, G0/G1 S parameter BEFORE M452 Laser Mode. ||
|:------------|----------|
| **Parameter** | **Meaning** |
| G1 Xnnn Ynnn Znnn S0 | Ignore endstops while moving. |
| G1 Xnnn Ynnn Znnn S1 | Sense endstops while moving. On Delta (only), axis letters refer to individual towers. |
| G1 Xnnn Ynnn Znnn S2 | Ignore endstops while moving. Also ignore if axis has not been homed. On Delta and CoreXY, axis letters refer to individual towers. |
| G1 Xnnn Ynnn Znnn S3 | Sense endstops while measuring axis length, and set the appropriate M208 limit to the measured position at which the endstop switch triggers. |


| RRF 2.02 to 2.05.1, G0/G1 S parameter AFTER M452 Laser Mode. ||
|:------------|----------|
| S parameter sets laser power with range of 0 to 254. ||

#### RRF 2.01 and earlier

In **RRF 2.01 and earlier**, S parameter controls the movement type. There is no H parameter or M452 Laser Mode.

| RRF_2.01 and earlier, G0/G1 S parameter ||
|:------------|----------|
| **Parameter** | **Meaning** |
| G1 Xnnn Ynnn Znnn S0 | Ignore endstops while moving. |
| G1 Xnnn Ynnn Znnn S1 | Sense endstops while moving. On Delta (only), axis letters refer to individual towers. |
| G1 Xnnn Ynnn Znnn S2 | Ignore endstops while moving. Also ignore if axis has not been homed. On Delta and CoreXY, axis letters refer to individual towers. |
| G1 Xnnn Ynnn Znnn S3 | Sense endstops while measuring axis length, and set the appropriate M208 limit to the measured position at which the endstop switch triggers. |

**Examples**

```
G0 X12               ; (move to 12mm on the X axis)
G0 F1500             ; (Set the feedrate to 1500mm/minute)
G1 X90.6 Y13.8 E22.4 ; (Move to 90.6mm on the X axis and 13.8mm on the Y axis while extruding 22.4mm of material)
G1 E10:10:5:0:0 F300 ; with a tool that has 5 extruder drives, extrude 10mm on drive 0, 10mm on drive 1, 5mm on drive 2 and 0mm on drive 3 and 4.
```

RepRapFirmware treats G0 and G1 in the same way **except** as follows:

* On SCARA and similar architectures that normally require linear motion to be approximated by short segments, a single continuous non-segmented movement will be used if this can be done without the print head dropping below the current Z height.
* In Laser and CNC mode, G0 moves are executed at the maximum feed rate available, to comply with the NIST GCode standard, This feed rate is set by the M203 command.

**Feedrate**

```
G1 F1500
G1 X50 Y25.3 E22.4
```

In the above example, we set the feedrate to 1500mm/minute on line 1, then move to 50mm on the X axis and 25.3mm on the Y axis while extruding 22.4mm of filament between the two points.

```
G1 F1500
G1 X50 Y25.3 E22.4 F3000
```

However, in the above example, we set a feedrate of 1500mm/minute on line 1, then do the move described above accelerating to a feedrate of 3000 mm/minute as it does so. The extrusion will accelerate along with the X and Y movement, so everything stays synchronized.

Feedrate is treated as simply another variable (like X, Y, Z, and E) to be linearly interpolated. This gives complete control over the acceleration and deceleration of the printer head in such a way that ensures that everything moves smoothly together, and the right volume of material is extruded at all points. The feedrate specified may not be reached due to a lower feedrate limit being configured, or the move being too short for the axis to accelerate and decelerate in time.

**For CNC users especially: RRF has a default minimum movement speed of 0.5mm/sec.** In firmware 2.03 and later this can be changed using the I ('i') parameter of the M203 command.

**Maximum Length of Moves**

The firmware keeps track of the exact number of microsteps sent to each movement axis using a 32-bit signed integer microstep counter, this limits the maximum absolute move and axis length to (2^31 - 1) microsteps. This does not apply to extruder drives. The firmware multiplies the requested axis position (after adding any offsets) by the steps/mm to get the required microstep position and the microstep counter accumulates across multiple moves, as the axis position increases it increments, as it decreases it decrements. The maximum size of the microstep counter is only an issue for situations where an axis needs to accommodate one or more moves or an overall axis length that would overflow the counter (i.e. an extremely long axis, or an extremely high resolution axis). In typical uses this is not a concern, for example on a standard linear axis using 160 microsteps/mm an axis of over 13km is supported. In cases where the microstep counter is not sufficient (e.g. a very high resolution rotary axis moving for a very long time) G92 can be used to set the origin to a new point on the axis and thus reset the counter.

In a similar manner, if the requested axis position gets very large then accuracy will suffer, because it is held and calculated as a 32-bit float.

## G2: Controlled Arc Move

Clockwise arc move. Supported by RRF_1.18 and later.
See 'G3: Controlled Arc Move' for usage.

## G3: Controlled Arc Move

Counter-clockwise arc move. Supported by RRF_1.18 and later.

**Usage**

* G2 Xnnn Ynnn Znnn Innn Jnnn Ennn Fnnn *(Clockwise Arc)*
* G3 Xnnn Ynnn Znnn Innn Jnnn Ennn Fnnn *(Counter-Clockwise Arc)*

**Parameters**

* **Xnnn** The position to move to on the X axis.
* **Ynnn** The position to move to on the Y axis.
* **Znnn** The position to move to on the Z axis.
* **Innn** The X coordinate of the arc centre **relative to the current X coordinate** (optional, ignored if R parameter is present).
* **Jnnn** The Y coordinate of the arc centre **relative to the current Y coordinate** (optional, ignored if R parameter is present).
* **Ennn** The amount to extrude between the starting point and ending point.^1^
* **Fnnn** The feedrate per minute of the move between the starting point and ending point (optional, defaults to the current feed rate).
* **Rnnn** The radius of the arc (optional, RRF2.03 and later)

Either the R parameter must be provided, or at least one of I and J must be provided. To draw a complete circle, define the position of the centre using I and/or J and make X and Y the same as the current X and Y coordinates.

^1^Where a tool has more than one extruder drive then Ennn:nnn:nnn etc is supported to allow for the individual movement of each to be controlled directly. This overrides the extruder mix ratio set with M567

**Examples**

```
G2 X90.6 Y13.8 I5 J10 E22.4 ; (Move in a Clockwise arc from the current point to point (X=90.6,Y=13.8), with a center point at (X=current_X+5, Y=current_Y+10), extruding 22.4mm of material between starting and stopping)
G3 X90.6 Y13.8 I5 J10 E22.4 ; (Move in a Counter-Clockwise arc from the current point to point (X=90.6,Y=13.8), with a center point at (X=current_X+5, Y=current_Y+10), extruding 22.4mm of material between starting and stopping)
G2 X100 Y50 R200            ; (draw a clockwise arc with radius 200 from the current position to X=100 Y=50)
```

## G4: Dwell

Pause the machine for a period of time.

**Parameters**

* **Pnnn** Time to wait, in milliseconds
* **Snnn** Time to wait, in seconds

**Examples**

```
G4 P200 ; wait for 200 milliseconds
```

In this case, sit still doing nothing for 200 milliseconds. The state of the machine, for example the bed and extruder temperatures, will still be preserved and controlled during the delay.

## G10: Tool Temperature Setting

Note that this use of G10 may be deprecated in a future version of RRF, although it will remain available for a substantial time. It will be replaced with [M568](/User_manual/Reference/Gcodes/M568) which can already be used for temperature setting in firmware 3.3beta2 and later.

This form of the G10 command is recognised by having a P combined with at least an R or S parameter.

**Usage**

* G10 Pnnn Rnnn Snnn

**Parameters**

* **Pnnn** Tool number
* **Rnnn** Standby temperature(s)
* **Snnn** Active temperature(s)

**Order dependency**

The tool must be created with M563 before it is referenced with this command.

**Examples**

```
G10 P1 R140 S205 ; set standby and active temperatures for tool 1
```

Remember that any parameter that you don't specify will automatically be set to the last value for that parameter.

The R value is the standby temperature in ^o^C that will be used for the tool, and the S value is its operating temperature. If you don't want the tool to be at a different temperature when not in use, set both values the same.

Temperatures set with G10 do not wait for the heaters to reach temp before proceeding. In order to wait for the temp use a M116 command after the G10 to wait for temps to be reached.

See the [T code (select tool)](/User_manual/Reference/Gcodes/T) below. In tools with multiple heaters the temperatures for them all are specified thus: R100.0:90.0:20.0 S185.0:200.0:150.0 .

## G10: Set workplace coordinate offset or tool offset

This form of the G10 command is recognised by having either or both of the L and P parameters. Supported on the Duet 2 series and later Duets.

**Parameters**

* **Ln** (Default L1) Mode (see below)
* **Pnnn** Tool number (default: current tool) if L=1, coordinate system number (default: current coordinate system) if L=2 or L=20
* **Xnnn** X offset
* **Ynnn** Y offset
* **U,V,Wnnn** U, V and W axis offsets^1^
* **Znnn** Z offset^2^

**Modes**

* L=1: this sets the tool offset, as if the L parameter was not present
* L=2: this sets the origin of the coordinate system number specified by the P parameter (1 to 9) to the specified X, Y, X... values
* L=20: this is similar to L=2 except that the origin is specified relative to the current position of the tool.

**Notes**

^1^Tool offsets are applied after any X axis mapping has been performed. Therefore if for example you map X to U in your M563 command to create the tool, you should specify a U offset not an X offset. If you map X to both X and U, you can specify both offsets.

^2^It's usually a bad idea to put a non-zero Z value in as well unless the tools are loaded and unloaded by some sort of tool changer or are on independent carriages. When all the tools are in the machine at once they should all be positioned at the same Z height to avoid a lower tool colliding with the object while a higher tool is printing.

Tool offsets are given as the offset of the nozzle relative to the print head reference point, so the signs are opposite to what you might expect because tool offsets are subtracted from the required printing locations during printing.

Any parameter that you don't specify will automatically be set to the last value for that parameter. That usually means that you want explicitly to set Z0.0. RepRapFirmware will report the tool parameters if only the tool number is specified.

See also [M585](/User_manual/Reference/Gcodes/M585).

**Tool Offset Examples**

```
G10 P2 X17.8 Y-19.3 Z0.0    ; sets the offset for tool 2 to the X, Y, and Z values specified
G10 L1 P2 X17.8 Y-19.3 Z0.0 ; sets the offset for tool 2 to the X, Y, and Z values specified
```

**Coordinate Offset Example**

Suppose the current machine coordinates are

* X=110
* Y=110
* Z=20

and you want to make this the origin (i.e. X=0, Y=0, Z=0) of the second coordinate system (accessible via G55) then there are two options:

1. G10 L2 P2 X110 Y110 Z20
1. G10 L20 P2 X0 Y0 Z0

The first example will set offsets to be subtracted from the current machine coordinates.

The second example will set the coordinates of the current position in the specified coordinate system directly.

**Order dependency**

If this command refers to any axes other than X, Y and Z then it must appear later in config.g than the M584 command that creates those additional axes.

## G10: Retract

This form of the G10 command is recognised by having no parameters.

**Parameters**

* (no parameters in the RepRapFirmware implementation)

**Examples**

```
G10
```

Retracts filament then performs any zlift/hop according to settings of M207.

RepRapFirmware recognizes G10 as a command to set tool offsets and/or temperatures if the P parameter is present, and as a retraction command if it is absent.

## G11: Unretract

**Parameters**

* no parameters in the RepRapFirmware implementation)

**Examples**

```
G11
```

Unretracts/recovers filament after undoing any zlift/hop according to settings of M207.

## G17: Select XY plane for arc moves

Supported from RepRapFirmware 3.3beta1.

**Parameters:** none

The active plane determines how the tool path of an arc (G2 or G3) is interpreted. Each input channel (e.g. SD card, USB, DWC) remembers the current plane independently of the other channels.

G17 is supported in RRF 2.03 to RRF 3.2.2, however as no other plane selection command (G18, G19) is supported, it accepts this command, but takes no action on receiving it.

## G18: Select XZ plane for arc moves

Supported from RepRapFirmware 3.3beta1.

**Parameters:** none

The active plane determines how the tool path of an arc (G2 or G3) is interpreted. Each input channel (e.g. SD card, USB, DWC) remembers the current plane independently of the other channels.

## G19: Select YZ plane for arc moves

Supported from RepRapFirmware 3.3beta1.

**Parameters:** none

The active plane determines how the tool path of an arc (G2 or G3) is interpreted. Each input channel (e.g. SD card, USB, DWC) remembers the current plane independently of the other channels.

## G20: Set Units to Inches

**Examples**

```
G20 ; set units to inches
```

Units from this command onwards are in inches. Note that this is only intended to affect G0, G1 and other commands commonly found in GCode files that represent objects to print. Specifically G20 only affects: G0 to G3, G10/M568 and G92.

So you should use metric values in config.g when configuring the printer and then change to inches with G20 at the end of it if the GCodes you want to send to move the machine are expressed in inches by default.

In RRF 2.03 and later, each GCode input channel has a separate inches/mm setting.

## G21: Set Units to Millimeters

**Examples**

```
G21 ; set units to millimeters
```

Units from this command onwards are in millimeters. (This is the default.).

## G28: Home

**Parameters**

*This command can be used without any additional parameters.*

* **X** Flag to home the X axis
* **Y** Flag to home the Y axis
* **Z** Flag to home the Z axis
* **U, V, W, A, B, C, D** Flags to home additional axes that may have been created

**Restrictions**

This command may not be used within a homing file.

**Examples**

```
G28    ; Home all axes
G28 XZ ; Home the X and Z axes
```

The **X** and **Z** parameters in this example act only as flags. Any coordinates given are ignored. For example, G28 Z10 results in the same behavior as G28 Z. Delta printers cannot home individual axes, but must always home all three towers, so the **X Y Z** parameters are simply ignored if the Firmware is in Delta mode.

The purpose of homing is to move the specified axes in such a way as to establish a known position for them, for example by moving an axis motor until an endstop switch is triggered. Homing an axis normally leaves it in a fixed position, however this position needs not be the zero position.

The way in which each axis is homed is completely configurable using the homing macro files, which specify what actions are taken. Execution of the G28 command is as follows:

* If there are no G28 parameters that correspond to axes that exist, or if the printer is a delta, then all axes are to be homed. Otherwise, the axes to be homed are determined by enumerating parameters of the G28 command.
* Each axis or delta tower to be homed is flagged as "position not known".
* If all axes or towers are to be homed, the file **homeall.g** is processed, except that on a delta printer **homedelta.g** is processed. If this process results in some but not all axes become flagged as "position known", an attempt will be made to home the remaining axes as if the G28 command had listed those axes.
* For each remaining axis flagged as "to be homed" the appropriate homing file is executed (**homex.g**, **homey.g**, **homez.g** etc.).

For Cartesian printers that use a Z probe to home Z instead of an endstop it is sensible to setup the **homeall.g** with the XY axes to home first, then move the carriage to a safe position –usually the middle of the bed– where it can safely probe downward to home Z. For an example see [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian).

Neither homeall.g nor any other homing file may itself contain a G28 command, however it may use a M98 command to invoke another homing file.

Because the behaviour of **G28** can be complex, it is recommended to consider the printer actions carefully before including **G28** in your ending GCode. On a Cartesian or CoreXY it could result in colliding with the printed object. An alternative to move the carriage at the completion of a print is to use **G0** or **G1**.

## G29: Mesh bed probe

This command uses a probe to measure the bed height at 4 or more points to determine its tilt and overall flatness. It then enables mesh compensation so that the nozzle will remain parallel to the bed. The printer must be homed with **G28** before using this command.

**Usage**

* G29
* G29 S0
* G29 S1 [P"filename"]
* G29 S2
* G29 S3 P"filename"

**Parameters**

* **S0** Probe the bed, save the height map in a file on the SD card, and activate bed compensation. The height map is stored in file is */sys/heightmap.csv*.
* **S1** Load the height map from file and activate bed compensation. The default filename is as for S0 but a different filename can be specified using the P parameter.
* **S2** Clear height map
* **S3** Save height map (supported in RepRapFirmware 2.04 and later)
* **P"file.csv"** Optional file name for bed height map file to save with **S3** or load with **S1**.
* **Kn** (supported in RRF 3.01-RC5 and later only, default 0) Z probe number

If G29 is commanded with no S parameter, then file **sys/mesh.g** is run if it exists, and in RRF 3.3 and later any parameters present are passed to mesh.g. If sys/mesh.g is not present then the command behaves like G29 S0.

**Examples**

```
G29 S0              ; Probe the bed, save height map to *heightmap.csv* and enable compensation
G29 S3 P"usual.csv" ; Save the current height map to file *usual.csv*
G29 S2              ; clear bed height map (disables bed compensation)
G29 S1 P"usual.csv" ; Load height map file *usual.csv* and enable compensation
```

To define the probe grid, see [M557](/User_manual/Reference/Gcodes/M557).
You can define a height to taper off the compensation using [M376](/User_manual/Reference/Gcodes/M376)
You can find more detailed information about setting up [Mesh Bed Compensation here](/User_manual/Connecting_hardware/Z_probe_mesh_bed).

## G30: Single Z-Probe

**Usage**

* G30 [Pnnn] [Xnnn] [Ynnn] [Znnn] [Hnnn] [Snnn]

**Parameters**

* **Pnnn** Probe point number
* **Xnnn** X coordinate
* **Ynnn** Y coordinate
* **Znnn** Z coordinate
* **Hnnn** Height correction
* **Snnn** Set parameter
* **Kn** (supported in RRF 3.01-RC5 and later only, default 0) Z probe number

**Examples**

```
G30     ; Probe the bed at the current XY position. When the probe is triggered, set the Z coordinate to the probe trigger height.
G30 S-1 ; Probe the bed at the current XY position. When the probe is triggered, do not adjust the Z coordinate, just report the machine height at which the probe was triggered.
G30 S-2 ; Probe the bed at the current XY position. When the probe is triggered, adjust the Z offset of the current tool to make the current position Z=0.
G30 S-3 ; Probe the bed and set the Z probe trigger height to the height it stopped at (supported in RRF 2.03 and later)
G30 P0 X20 Y50 Z-99999       ; Probe the bed at X20 Y50 and save the XY coordinates and the height error as point 0
G30 P3 X180 Y180 Z-99999 S4  ; Probe the bed at X180 Y180, save the XY coordinates and the height error as point 3 and calculate 4-point compensation or calibration
G30 P3 X180 Y180 Z-99999 S-1 ; As previous example but just report the height errors
```

**Caution:** the XY coordinates are permitted to be outside the normal printable bed area! This is intentional, because some printers (e.g. delta printers) benefit from probing areas not used for printing.

**G30 without a P parameter**

This probes the bed starting at the current height. Depending on the value of the S parameter it can be used to home the Z axis, to measure the Z probe trigger height, or to adjust the Z offset of the current tool.

**G30 with a P parameter**

This is used for operations that are performed after the printer has been homed and that require the height error at more than one probe point to be measured. These operations are typically performed in the bed.g file. With a Z parameter of -9999 or less, the head moves to the specified XY coordinates and the dive height (set using the H parameter in the M558 command), and probes the bed.

On the last G30 command in the sequence, the S parameter indicates that a complete set of points has been probed and instructs the firmware what sort of calibration to perform. If the value is -1 then the Z offsets of all the points probed are printed, but no calibration is done. If the value is zero or not present, then this specifies that the number of factors to be calibrated is the same as the number of points probed. Otherwise, the value indicates the number of factors to be calibrated, which must be no greater than the number of points probed. From version 1.09, the number of factors may be 3, 4 or 5 when doing old-style auto bed compensation on a Cartesian or CoreXY printer, and 3, 4, 6, 7, 8 or 9 when doing auto calibration of a Delta printer.

See [Calibrating a Delta Printer, setting up the bed.g file](/User_manual/Tuning/Delta_calibration#setting-up-the-bedg-file) for a more detailed explanation.

If a "normal" Z parameter is given instead of -9999 or lower, then the bed is not probed, but instead that value is used as if the Z probe had triggered at that height.

The optional H parameter is a height correction for that probe point. It allows for the Z probe having a trigger height that varies with XY position. The nominal trigger height of the Z probe (e.g. at bed centre) is declared in the Z parameter of the G31 command in the config.g file. When you probe using G30 and the probe triggers, the firmware will assume that the nozzle is at the nominal trigger height plus the value you have in the H parameter.

## G31: Set or Report Current Probe status

**Usage**

* G31 Pnnn Xnnn Ynnn Znnn Cnnn Snnn

**Parameters**

* **Pnnn** Trigger value
* **Xnnn** Probe X offset^1^
* **Ynnn** Probe Y offset^1^
* **U,V,W,A,B,C...nnn** Probe Offsets for all axes except Z^1^ (RRF >=3.3beta2)
* **Znnn** Trigger Z height
* **Cnnn** Temperature coefficient^2^ (RRF <= 3.3beta1)
* **Tnnn** Temperature coefficient^2^ (RRF >= 3.3beta2)
* **Snnn** Calibration temperature^2^
* **Tnnn** (RRF 1.17 and later, removed in RRF3) Z probe type to which these parameters apply, defaults to the current Z probe type as defined by M558 P parameter.
* **Knnn** (RRF3) Selects the Z probe number. If there is no K parameter then the current Z probe number is used. The current Z probe number is 0 at startup.
* **Hnnn** (RRF3) Selects the sensor number to use for temperature compensation when the C and S parameters are used.

^1^X and Y offsets of the Z probe relative to the print head (i.e. the position when the empty tool is selected) can be specified. This allows you to calculate your probe coordinates based on the geometry of the bed, without having to correct them for Z probe X and Y offset.

^2^Optional parameters 'S' (temperature in °C at which the specified Z parameter is correct, default is current temperature) and 'C' (temperature coefficient of Z parameter in mm/°C, default zero) ('T' in RRF >=3.3beta2) can be set. This is useful for probes that are affected by temperature. In RRF2 the bed temperature reading is used. In RRF3 you must specify which temperature sensor to use in the H parameter.

**Examples**

```
G31 P500 Z2.6
G31 X16.0 Y1.5
```

**Order dependency**

A G31 command to set Z probe parameters must come after the M558 command that defines the Z probe.
It must also come after M584 if it references any axes beyond X and Y (RRF >=3.3beta2).

**Notes**

When used on its own this reports whether the Z probe is triggered, or gives the Z probe value in some units if the probe generates height values. If combined with a Z and P field (example: G31 P312 Z0.7) this will set the Z height to 0.7mm when the Z-probe value reaches 312 when a G28 Z0 (zero Z axis) command is sent. The machine will then move a further -0.7mm in Z to place itself at Z = 0. This allows non-contact measuring probes to approach but not touch the bed, and for the gap left to be allowed for. If the probe is a touch probe and generates a simple 0/1 off/on signal, then G31 Z0.7 will tell the RepRap machine that it is at a height of 0.7mm (as configured by Z0.7 in this example) when the probe is triggered.

If you are using the nozzle as a probe (for example with a peizo or switch that the hotend had a travel distance to trigger then remember the Z offset needs to be negative (ie the probe triggers under Z0

Separate G31 parameters may be defined for different probe types (i.e. 0+4 for switches, 1+2 for IR probes and 3 for alternative sensors). To specify which probe you are setting parameters for, send a [M558](/User_manual/Reference/Gcodes/M558) command to select the probe type before sending the G31 command, or use the T parameter.

## G32: Run bed.g macro

**Parameters**

In RRF 3.3 and later any parameters will be passed to macro file bed.g.

**Examples**

```
G32 ; execute macro bed.g
```

The firmware executes macro file **bed.g**. This macro normally uses G30 commands to probe the bed and then perform auto calibration of a delta printer (see [Calibrating a delta printer](/User_manual/Tuning/Delta_calibration)), or perform bed levelling by moving the Z leadscrews independently, or display the manual corrections needed to the bed levelling screws.

For more detail on using G32 for automatic Delta calibration see: [Calibrating a delta printer](/User_manual/Tuning/Delta_calibration)

For more detail on using G32 for automatic leveling of a cartesian or CoreXY see: [Bed levelling using multiple independent Z motors](/User_manual/Connecting_hardware/Z_probe_auto_levelling)

For more detail on using G32 for manual bed leveling assistant see: [Using the manual bed levelling assistant](/User_manual/Connecting_hardware/Z_probe_manual_levelling)

## G38.2: Straight Probe

Probe toward workpiece, stop on contact, signal error if failure. Supported in RepRapFirmware 3 and later.

**Parameters**

* **X,Y,Z,U,V,W,A,B,Cnnn**: Target position (up until RRF 3.1.1 these are expected to be machine coordinates)
* **Pnn**: Probe number to use, default 0 (deprecated in RRF 3.3 and later)
* **Knn**: Probe number to use, default 0 (RRF 3.3 and later)

**Modes**

* **G38.2**: probe toward workpiece, stop on contact, signal error if failure
* **G38.3**: probe toward workpiece, stop on contact
* **G38.4**: probe away from workpiece, stop on loss of contact, signal error if failure
* **G38.5**: probe away from workpiece, stop on loss of contact

**Conditions considered an error** (for G38.2 and G38.4)

* Probe already in target state (triggered for G38.2 and not triggered for G38.4)
* No target coordinates given
* Target coordinates equal to current position

G38.x Straight Probe will perform a probing move in a straight line with the currently active or selected (**Pnnn**) probe. It will either move towards an obstacle (usually a workpiece) and stop on contact or move away from an obstacle until the probe loses contact.

**Note**: for probing away the probe used does not have to be inverted. This command will take care of that.

Feedrate used for this move is the configured feedrate of the used probe (see [M558 Fnnn](/User_manual/Reference/Gcodes/M558)).

## G38.3: Straight Probe

Probe toward workpiece, stop on contact Supported in RepRapFirmware 3 and later.
See 'G38.2: Straight Probe' for usage.

## G38.4: Straight Probe

Probe away from workpiece, stop on loss of contact, signal error if failure. Supported in RepRapFirmware 3 and later.
See 'G38.2: Straight Probe' for usage.

## G38.5: Straight Probe

Probe away from workpiece, stop on loss of contact. Supported in RepRapFirmware 3 and later.
See 'G38.2: Straight Probe' for usage.

## G53: Use machine coordinates

Supported on the Duet 2 and later Duets only.

This causes all coordinates in movement commands on the remainder of the current line of GCode to be interpreted as machine coordinates, ignoring any coordinate offset of the workplace coordinate system currently in use, and also ignoring any tool offsets. See the NIST GCode Interpreter Version 3 standard for more details.

## G54: Select coordinate system

Selects coordinate system 1. Supported on the Duet 2 and later Duets only.

G54 selects coordinate system 1, G55 selects coordinate system 2 etc. up to G59 which selects coordinate system 6. G59.1 selects coordinate system 7, G59.2 selects system 8 and G59.3 selects system 9.

Initially, coordinate system 1 is in use, and all coordinate systems have zero offset from the machine coordinates. To set coordinate system offsets, use the G10 command with the L2 parameter.

See the [NIST GCode Interpreter Version 3 standard](http://www.nist.gov/manuscript-publication-search.cfm?pub_id=823374) for more details.

## G55: Select coordinate system

Selects coordinate system 2. Supported on the Duet 2 and later Duets only.
See 'G54: Select coordinate system' for usage.

## G56: Select coordinate system

Selects coordinate system 3. Supported on the Duet 2 and later Duets only.
See 'G54: Select coordinate system' for usage.

## G57: Select coordinate system

Selects coordinate system 4. Supported on the Duet 2 and later Duets only.
See 'G54: Select coordinate system' for usage.

## G58: Select coordinate system

Selects coordinate system 5. Supported on the Duet 2 and later Duets only.
See 'G54: Select coordinate system' for usage.

## G59: Select coordinate system

Selects coordinate system 6. Supported on the Duet 2 and later Duets only.
See 'G54: Select coordinate system' for usage.

## G59.1: Select coordinate system

Selects coordinate system 7. Supported on the Duet 2 and later Duets only.
See 'G54: Select coordinate system' for usage.

## G59.2: Select coordinate system

Selects coordinate system 8. Supported on the Duet 2 and later Duets only.
See 'G54: Select coordinate system' for usage.

## G59.3: Select coordinate system

Selects coordinate system 9. Supported on the Duet 2 and later Duets only.
See 'G54: Select coordinate system' for usage.

## G60: Save current position to slot

Supported in firmware 1.21 and later.

**Usage**

* **G60 Snn**

**Parameters**

* **Snn** specifies memory slot number (0-based) to save into (default 0)

RepRapFirmware for Duets generally provides slots 0 thru 5. When a print is paused the coordinates are saved to slot 1 automatically, and at the start of a tool change the coordinates are saved to slot 2 automatically. The remaining slots are free to use for any purpose. Use G0 or G1 with the appropriate R parameter to move the current tool to a saved position.

## G68: Coordinate rotation

**Usage**

* G68 Xnnn Ynnn Rnnnn
* G68 Annn Bnnn Rnnnn

**Parameters**

* **Xnnn, Ynnn...** Centre coordinates to rotate about
* **Annn** first centre coordinate in the selected plane (e.g. equivalent to Xnnn if the selected plane is XY)
* **Bnnn** second centre coordinate in the selected plane (e.g. equivalent to Ynnn if the selected plane is XY)
* **Rnnn** angle to rotate in degrees. Positive angles rotate anticlockwise when viewing the selected plane from above.

Rotates the coordinate system in the current plane as selected by [G17](/User_manual/Reference/Gcodes/G17), [G18](/User_manual/Reference/Gcodes/G18) or [G19](/User_manual/Reference/Gcodes/G19). You may either specify the coordinates of the two axes of the selected plan (e.g. X and Y if using the default XY plane or after G17) or you may specify A and B coordinates.

RepRapFirmware implements G68 for the XY plane only.

## G69: Cancel coordinate rotation

**Usage**

* G69

This cancels any coordinate rotation that was set up by G68.

## G90: Set to Absolute Positioning

**Usage**

* G90

All coordinates from now on are absolute, relative to the origin of the machine.

Note: RepRapFirmware uses [M82](/User_manual/Reference/Gcodes/M82) to set the extruder to absolute mode: extrusion is NOT set to absolute using G90

## G91: Set to Relative Positioning

**Usage**

* G91

All coordinates from now on are relative to the last position.

Note: RepRapFirmware uses [M83](/User_manual/Reference/Gcodes/M83) to set the extruder to relative mode: extrusion is NOT set to relative using G91

## G92: Set Position

**Parameters**

* *This command can be used without any additional parameters.*
* **Xnnn** new X axis position
* **Ynnn** new Y axis position
* **Znnn** new Z axis position
* **Ennn** new extruder position

**Examples**

```
G92 X10 E90
```

Allows manual specification of the axis positions by specifying the current position to the values given. This example would set the machine's X coordinate to 10, and the extrude coordinate to 90. No physical motion will occur. In RepRapFirmware, a G92 without coordinates does nothing.

# M-commands

## M0: Stop or Unconditional stop

**Parameters**

* *This command can be used without any additional parameters.*
* **Hnnn** Keep heaters on

**Examples**

```
M0
```

The effect of M0 depends on the state of the machine.

1. The firmware finishes any moves left in its buffer.
1. **Either**: if the axes are homed and if a print is being cancelled (M25), it executes the macro file **cancel.g** if present. **Or**: if M0 is sent at any other time, **stop.g** is run if present.
1. All motors are put into idle mode.
1. If there is no stop.g or cancel.g file (as appropriate) then all heaters are turned off too. In RRF versions prior to 3.4 you can prevent heaters being turned off using parameter H1.

See also M1, M112.

## M1: Sleep or Conditional stop

**Examples**

```
M1
```

The effect of M1 depends on the state of the machine.

1. The firmware finishes any moves left in its buffer.
1. **Either**: if the axes are homed and if a print is being cancelled (M25), it executes the macro file **cancel.g** if present. **Or**: if M1 is sent at any other time, **sleep.g** is run if present.
1. All motors and heaters are are turned off.

G and M codes can still be sent, the first of which will wake it up again. See also M0, M112.

If Marlin is emulated in RepRapFirmware, this does the same as [M25](/User_manual/Reference/Gcodes/M25) if the code was read from a serial or Telnet connection, else the macro file **sleep.g** is run before all heaters and drives are turned off.

## M3: Spindle On, Clockwise (CNC specific)/ Laser on (Laser specific)

Supported in RepRapFirmware version 1.20 and later when the printer mode is set to CNC mode (see [M453](/User_manual/Reference/Gcodes/M453)) or laser mode ([M452](/User_manual/Reference/Gcodes/M452)).

**Parameters**

* **Snnn** Spindle RPM (CNC mode) or laser power (laser mode). In laser mode, the relationship between the S parameter and laser power depends on the R parameter that was specified in the M452 command.
* **Pnnn** Spindle slot (CNC mode only). Directly address a spindle slot.

**Examples**

```
M3 S4000 ; turn on spindle at speed of 4000 RPM, clockwise
```

#### M3 in RepRapFirmware 3.3 and later

M3 can be called without any parameters and will start the spindle of the current tool turning clockwise at the spindle RPM of that tool.

Using the S parameter will additionally set the spindle RPM of the current tool.

It is an error if there is no tool active or the active tool does not have a spindle assigned and there is no P parameter provided to define which spindle this should be applied to.

## M4: Spindle On, Counterclockwise (CNC specific)

Supported in RepRapFirmware version 1.20 and later when the printer mode is set to CNC (see M453).

**Parameters**

* **Snnn** Spindle RPM
* **Pnnn** Spindle slot (CNC mode only). Directly address a spindle slot.

**Examples**

```
M4 S4000 ; turn on spindle at speed of 4000 RPM, counterclockwise
```

#### M4 in RepRapFirmware 3.3 and later

M4 can be called without any parameters and will start the spindle of the current tool turning counterclockwise at the spindle RPM of that tool.

Using the S parameter will additionally set the spindle RPM of the current tool.

It is an error if there is no tool active or the active tool does not have a spindle assigned and there is no P parameter provided to define which spindle this should be applied to.

## M5: Spindle Off (CNC specific), laser off (Laser specific)

Supported in RepRapFirmware version 1.20 and later when the printer mode is set to CNC (see M453) or Laser (see M452).

**Parameters**

* none

**Examples**

```
M5 ; turn of spindle/laser
```

#### M5 in RepRapFirmware 3.3 and later

M5 will stop the spindle of the current tool (if any) or all defined spindles if the current tool has no spindle assigned or there is no active tool.

## M17: Enable all stepper motors

Available in RepRapFirmware 3.3beta2 and later.

**Parameters**

* *This command can be used without any additional parameters.*
* **X** X axis
* **Y** Y axis
* **Z** Z axis
* **U** U axis
* **V** V axis
* **W** W axis
* ... or any other defined axis
* **E[n]** Extruder drive(s)

**Examples**

```
M17
M17 X E0
```

Enables all stepper motors when used without parameters. Stepper motors can also be enabled selectively. For example, M17 X E0:2 will enable the X, extruder 0 and extruder 2 motors. Use this command to energise a motor for stealthChop tuning, followed by a short pause eg G4 P100 to allow the driver to establish the motor parameters.

## M18: Disable all stepper motors

**Parameters**

* *This command can be used without any additional parameters.*
* **X** X axis
* **Y** Y axis
* **Z** Z axis
* **U** U axis
* **V** V axis
* **W** W axis
* ... or any other defined axis
* **E[n]** Extruder drive(s)

**Examples**

```
M18
M18 X E0
```

Disables stepper motors and allows axes to move 'freely.' Stepper motors can be disabled selectively. For example, M18 X E0:2 will disable the X, extruder 0 and extruder 2 motors. Also see M84.

## M20: List SD card

**Parameters**

* *This command can be used without any additional parameters.*
* **Snnn** Output style: 0 = text (default), 2 = JSON
* **P"path"** Folder to list, defaults to the standard folder for GCode files (0:/gcodes in RepRapFirmware)
* **Rnnn** Number of files to skip, default 0

**Examples**

```
M20                      ; lists all files in the root folder of the internal SD card
M20 S2 P"/gcodes/subdir" ; lists all files in the gcodes/subdir folder of the internal SD card, using JSON format
M20 P"1:/"               ; lists all files on the secondary (PanelDue) SD card's root
```

If Marlin compatibility is enabled, a file list response is encapsulated:

```
Begin file list:
 Traffic cone.g
 frog.gcode
 calibration piece.g
 End file list
 ok
```

If RepRapFirmware emulates no firmware compatibility, a typical response looks like:

```
GCode files:
 "Traffic cone.g","frog.gcode","calibration piece.g"
```

RepRapFirmware always returns long filenames in the case in which they are stored.

If the S2 parameter is used then the file list is returned in JSON format as a single array called "files" with each name that corresponds to a subdirectory preceded by an asterisk, and the directory is returned in variable "dir". Example:

```
M20 S2 P"/gcodes"
 {"dir":"/gcodes","first":0,"next":0,"files":["4-piece-1-2-3-4.gcode","Hinged_Box.gcode","Hollow_Dodecahedron_190.gcode","*Calibration pieces"]}
```

Returned value "first" is the number of files that were skipped (as specified in the R parameter), and "next" is the number to skip next time to retrieve the next block of filenames. If "next" is zero then there are no more filenames.

## M21: Initialize SD card

**Parameters**

* *This command can be used without any additional parameters.*
* **Pnnn** SD card number (default 0)

**Examples**

```
M21
M21 P1
```

The specified SD card is initialized. If an SD card is loaded when the machine is switched on, this will happen by default. SD card must be initialized for the other SD functions to work.

In SBC mode and v3.4 or newer this code may be used to mount block devices or remote endpoints using the mount command. Note that this requires the DuetPiManagementPlugin to be running.

**Parameters**

* **Pnnn** Device node or remote endpoint
* **Snnn** Local directory to mount to (e.g. `0:/gcodes/remote`, optional if the device node is already present in /etc/fstab)
* **Tnnn** Mount type (-t flag, e.g. nfs)
* **Onnn** Mount options (-o flag)

**Examples**

```
M21 P"192.168.1.222:3D" S"0:/gcodes/remote" T"nfs"
M21 P"//192.168.1.222/3D" S"0:/gcodes/remote" T"cifs" O"user=myUser,password=myPass"
M21 P"curlftpfs#ftp://192.168.1.222:/3D" S"0:/gcodes/remote" T"fuse" O"user=myUser:myPass" ; requires curlftpfs package
```

## M22: Release SD card

**Parameters**

* *This command can be used without any additional parameters.*
* **Pnnn** SD card number (default 0)

**Examples**

```
M22
M22 P1
```

The specified SD card is released, so further (accidental) attempts to read from it are guaranteed to fail. Helpful, but not mandatory before removing the card physically.

In SBC mode and v3.4 or newer this code may be used to unmount block devices or remote endpoints using the mount command. Note that this requires the DuetPiManagementPlugin to be running.

**Parameters**

* **Pnnn** Device node or remote endpoint

**Examples**

```
M22 P"0:/gcodes/remote"
```

## M23: Select SD file

**Examples**

```
M23 filename.gco
```

The file specified as filename.gco is selected ready for printing. RepRapFirmware supports long filenames as well as 8.3 format.

Full paths, including all directories and subdirectories are limited to 120 characters.

e.g.;

```
0:/gcodes/0123456789/012.gcode
```

counts as 30 characters

## M24: Start/resume SD print

**Examples**

```
M24
```

The machine prints from the file selected with the M23 command. If the print was previously paused with M25, printing is resumed from that point. To restart a file from the beginning, use M23 to reset it, then M24.

When this command is used to resume a print that was paused, the macro file **resume.g** is run prior to resuming the print.

## M25: Pause SD print

**Examples**

```
M25
```

The machine pauses printing at the current position within the file. To resume printing, use [M24](/User_manual/Reference/Gcodes/M24). Do not use this code to pause the print in the currently printing GCode file, use [M226](/User_manual/Reference/Gcodes/M226) instead. M226 is intended for use in the GCode file being printed, for example to pause after a particular layer has completed. So it waits until all the moves in the queue have been completed. M25 is intended for use from a different source of GCodes than the current print from SD card (like the web interface console, PanelDue or Macro).

M25 attempts to execute as quickly as possible and follows the following logic:

* When RRF receives M25 it will look for a move in the current queue after which it can stop without violating the configured jerk limits.
* If it finds one it stops after that move without decelerating (because the jerk limits allow that)
* If it can't find one it will plan and execute a deceleration. in this case the pause will occur 1 move+2 seconds after M25 is sent.

That means the longest it will take to pause is 1 move+2 seconds. In most situations pause occurs much quicker than that.

After movement is halted as described above but prior to the pause operation completing, the macro file **pause.g** is run. This allows the head to be moved away from the print, filament to be retracted, etc.

## M26: Set SD position

**Parameters**

* Snnn File position from start of file in bytes
* Pnnn (Optional) Proportion of the first move to be skipped, default 0.0, must be less than 1.0

**Examples**

```
M26 S49315
```

Set the file offset in bytes from the start of the SD card file selected by M23. The offset must correspond to the start of a GCode command.

## M27: Report SD print status

**Examples**

```
M27
```

Reports the number of bytes processed in this format, which can be processed by Pronterface:

```
SD printing byte 2134/235422
```

If no file is being printed, only this message is reported:

```
Not SD printing.
```

## M28: Begin write to SD card

**Examples**

```
M28 filename.gco
```

File specified by filename.gco is created (or overwritten if it exists) on the SD card and all subsequent commands sent to the machine are written to that file.

## M29: Stop writing to SD card

**Examples**

```
M29
```

File opened by M28 command is closed, and all subsequent commands sent to the machine are executed as normal.

## M30: Delete a file on the SD card

**Examples**

```
M30 filename.g
M30 "filename.g"
```

filename.g is deleted.

## M32: Select file and start SD print

**Examples**

```
M32 filename.g
M32 "filename.g"
```

It can be used when printing from SD card and does the same as M23 and M24.

## M36: Return file information

**Examples**

```
M36 "filename.g"
```

In RRF 3.x, the quotation marks around the filename are mandatory. Returns information for the specified SD card file in JSON format. If no path is specified in the filename, the default path is the folder where GCode file to print are normally stored, which in RepRapFirmware is 0:/gcodes.

A sample response is:

`{"err":0,"size":457574,"height":4.00,"layerHeight":0.25,"filament":[6556.3],"generatedBy":"Slic3r 1.1.7 on 2014-11-09 at 17:11:32"}`

The "err" field is zero if successful, nonzero if the file was not found or an error occurred while processing it. The "size" field should always be present if the operation was successful. The presence or absence of other fields depends on whether the corresponding values could be found by reading the file. The "filament" field is an array of the filament lengths required from each spool. The size is in bytes, all other values are in mm. The fields may appear in any order, and additional fields may be present.

If the file name parameter is not supplied and a file on the SD card is currently being printed, then information for that file is returned including additional field "fileName". This feature is used by the web interface and by PanelDue, so that if a connection is made when a file is already being printed, the name and other information about that file can be shown.

## M37: Simulation mode

**Parameters**

* **S1** Enter simulation mode
* **S0** Leave simulation mode
* **P"filename"** (optional) Simulate printing a file from SD card

**Examples**

```
M37 S1
M37 P"MyModel.g"
```

Used to switch between printing mode and simulation mode. Simulation mode allows the electronics to compute an accurate printing time, taking into account the maximum speeds, accelerations etc. that are configured.

M37 S1 enters simulation mode. All G and M codes will not be acted on, but the time they would take to execute will be calculated.

M37 S0 exits simulation mode and prints the total time taken by simulated moves since entering simulation mode..

M37 with no S parameter prints the time taken by the simulation, from the time it was first entered using M37 S1, up to the current point (if simulation mode is still active) or the point that the simulation was ended (if simulation mode is no longer active).

## M38: Compute SHA1 hash of target file

**Examples**

```
M38 gcodes/myfile.g
```

Used to compute a hash of a file on the SD card and returns a hexadecimal string which is the SHA1 of the file. If the file cannot be found, then the string "Cannot find file" is returned instead.

## M39: Report SD card information

Supported in ReprapFirmware 1.21 and later.

**Parameters**

* **Pn** SD slot number, default 0
* **Sn** Response format. S0 returns a plain text response, S2 returns a response in JSON format.

**Examples**

```
M39       ; report information for SD card 0 in plain text format
M39 P1 S2 ; report information for SD card 1 in JSON format
```

This command returns information about the SD card in the specified slot in the requested format. At least the following is returned:

* Whether or not a usable card is present in the slot
* The capacity of the card in bytes (if a card is present)
* The amount of free space on the card in bytes (if a card is present)

The JSON response has the following format (this is provisional):

`{"SDinfo":{"slot":0,"present":1,"capacity":4294967296,"free":2147485184,"speed":20971520}}`

The capacity and free space are in bytes and the interface speed is in bytes/second.

## M42: Switch I/O pin

### {.tabset}

**See also**

[M280](/User_manual/Reference/Gcodes/M280), [M950](/User_manual/Reference/Gcodes/M950)

#### RepRapFirmware 3.x

**Parameters**

* **Pnnn** GPIO port number (set by M950)
* **Snnn** Pin value

**Examples**

```
M950 P0 C"exp.heater3" Q500  ; allocate GPIO port 0 to heater3 on expansion connector, 500Hz
...
M42 P0 S0.5  ; set 50% PWM on GPIO port 0
```

M42 switches a general purpose I/O pin which is defined by M950. Use M42 Px Sy to set pin x to value y. The S field may be in the range 0..1 or 0..255.

**Notes**

Before you can use M42 you must create a GPIO port using M950. Then in the M42 command, the P parameter is the GPIO port number. 

The F (PWM frequency) and I (invert PWM) parameters are no longer supported in M42. Instead, use the Q (PWM frequency) and C (pin name, with ! to invert) parameters in M950 when you create the GPIO port.

In RRF 3.4, Duet 3 supports up to 32 outpus and 16 inputs, Duet 2 Wifi/Ethernet support up to 20 GPIO ports, and Duet 2 Maestro supports 10 GPIO ports. No GPIO ports are allocated by default.

#### RepRapFirmware 2.x

**Parameters**

* **Pnnn** Logical pin number
* **Snnn** Pin value
* **Fnnn** PWM frequency (optional)
* **Innn** Invert PWM (optional). I0 (no inversion) is default, I1 inverts.

**Examples**

```
M42 P3 I1 S0.5 F500  ; set Heater 3 pin to 50% PWM at 500Hz, inverted
```

M42 switches a general purpose I/O pin. Use M42 Px Sy to set pin x to value y. The S field may be in the range 0..1 or 0..255.

**Notes**

In RRF 2.x, the 'P' pin number reference is an internal firmware reference named "digital pin" which It maps on different connector pins depending the hardware. See [Controlling unused IO pins](/User_manual/Connecting_hardware/IO_GPIO) for all the unused pin mappings in RRF 2.x.

For Duet 0.8.5 and 0.6, along with pre 1.16 versions of RepRapFirmware, see the [RepRap GCode dictionary M42 entry](http://reprap.org/wiki/G-code#M42:_Switch_I.2FO_pin).

## M73: Set remaining print time

**Parameters**

* **Pnn** Percentage of the print that has been completed (not used by RRF)
* **Rnn** Remaining print time in minutes

M73 is generated by some slicers, to inform the firmware about the state of the print so that it can provide a better estimate of the remaining print time.

## M80: ATX Power On

**Parameters**

* **C"port_name"** (RRF 3.4beta4 and later) Name of the pin used to control the power supply

**Examples**

```
M80          ; sets pin in the power on state
M80 C"pson"  ; allocates the pin and sets the pin in the power on state.
M80 C"!pson" ; inverts the PS_ON output for Meanwell power supplies
```

Turns on the ATX power supply from standby mode to fully operational mode using the power supply control pin on the External 5V header. If a deferred power down command was set up using M81 S1 then it is cancelled.

In RRF 3.4.0beta6 and later, M80 will do nothing unless you have previously allocated a pin to control power using either M80 or M81 with C parameter. This would normally be done in the config.g file.

## M81: ATX Power Off

**Parameters**

* **C"port_name"** (RRF 3.4beta6 and later) Name of the pin used to control the power supply
* **Sn** n=0 turn power off immediately (default), n=1 turn power off when all thermostatic fans have turned off (RepRapFirmware 1.20 and later only). This parameter optional and ignored if the C parameter is present. The default is to turn off power as soon as the movement queue is empty.

**Examples**

```
M81 C"pson" ; allocate the PS_ON pin to power control but leave power off
M81         ; turn power off immediately
M81 S1      ; turn power off when all thermostatic fans have turned off
```

Turns off the ATX power supply. Counterpart to M80. A deferred power down command (M81 S1) that has not yet happened can be cancelled using M80.

In RRF 3.4beta6 and later, M81 will have no effect unless a power control pin has previously been assigned using M80 or M81 with the C parameter. This would normally be done in the config.g file.

## M82: Set extruder to absolute mode

**Examples**

```
M82 ; absolute extrusion mode
```

Makes the extruder interpret extrusion as absolute positions.

## M83: Set extruder to relative mode

**Examples**

```
M83 ; relative extrusion mode
```

Makes the extruder interpret extrusion values as relative positions.

## M84: Stop idle hold

**Parameters**

* *This command can be used without any additional parameters.*
* **Snnn** Idle Time-out
* X,Y, E0:1.. etc

**Examples**

```
M84
M84 E0:1:2:3:4
```

Stop the idle hold on all axis and extruder. Be aware that by disabling idle hold during printing, you will get quality issues.

For example, "M84 S10" will idle the stepper motors after 10 seconds of inactivity.

You can disable individual motors with the standard X, Y, Z etc switches.

## M92: Set axis steps per unit

**Parameters**

* *This command can be used without any additional parameters.*
* **Xnnn** Steps per mm for the X drive
* **Ynnn** Steps per mm for the Y drive
* **Znnn** Steps per mm for the Z drive
* **Unnn** Steps per mm for the U drive
* **Vnnn** Steps per mm for the V drive
* **Wnnn** Steps per mm for the W drive
* **Ennn** Steps per mm for the extruder drive(s)
* **Snnn** Defines in which microstepping the above steps per unit are given. If omitted it will use the microstepping currently set by M350. This parameter is supported in RRF >=2.03.

**Order dependency**

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

**Examples**

```
M92 X80 Y80 Z80
M92 E420:500
```

Allows programming of steps per mm for motor drives. These values are reset to those set in config.g on power on, unless saved with M500. It will report the current steps/mm if you send M92 without any parameters.

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour.

Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M92 Z80, not M92 Z80:80

RepRapFirmware uses floating point maths so it is possible to use floating point numbers for steps/mm.

## M98: Call Macro/Subprogram

**Parameters**

* **P"nnn"** Macro filename^1^
* **Rn** (when no P parameter is provided) 1 = remainder of current macro can be interrupted and the macro restarted, 0 = remainder of current macro cannot be interrupted (RRF 3.4 and later). Macros cannot be interrupted by default, except in the case of power failure.
* If the P parameter is provided then any additional parameters will be passed to the macro^2^

**Examples**

```
M98 P"mymacro.g"
```

Runs the macro in the file mymacro.g. Macro calls can be nested (i.e. a macro can call another macro).

The filename may include a path to a subdirectory. For relative paths, the default folder is /sys. Absolute file paths are also supported starting with "0:/" for the internal SD card or "1:/" for the external SD card if fitted.

**Notes**

^1^ In RRF 3.x, quotation marks around the filename are mandatory. In RRF2.x and earlier, string can be enclosed in quotes if required. See [Quoted Strings](/User_manual/Reference/Gcodes#quoted-strings) for details. 

^2^ In RRF 3.3 and later M98 supports additional parameters used to pass information to the macro being called. See the [GCode Meta Commands](/User_manual/Reference/Gcode_meta_commands) documentation for the details.

## M99: Return from Macro/Subprogram

**Examples**

```
M99
```

Returns from an M98 call, this is not required to return from the end of a macro and the macro naturally returns at the end of file.

RepRapFirmware closes the currently active macro file. If a nested macro is being run, RepRapFirmware goes up one stack level.

## M101: Un-retract filament

Supported as part of compatibility with Simplify3D firmware retraction commands.

Same behaviour as G11 (unretract)

Must select "Generate M101, M102, M103" in Simplify3D.

## M102: Turn extruder on (ignored)

Supported as part of compatibility with Simplify3D firmware retraction commands. Command is accepted and ignored as it serves no function in RRF.

Must select "Generate M101, M102, M103" in Simplify3D.

## M103: Retract filament

Supported as part of compatibility with Simplify3D firmware retraction commands.

Same behaviour as G10 without parameters (retract)

Must select "Generate M101, M102, M103" in Simplify3D.

## M104: Set Extruder Temperature

**Parameters**

* **Snnn** target temperature
* **Tn** optional tool number

**Examples**

```
M104 S190
M104 S220 T1
```

The first example sets the active and standby temperature of the current tool to 190^o^C and return control to the host immediately (*i.e.* before that temperature has been reached by the extruder). If no tool is active, it will apply to the lowest non-negative tool number.

The second example sets the active and standby temperatures of tool 1 to 220^o^C. If tool 1 is off, it will be set to standby as well (resulting in tool 1 heating up to the temperature given).

See also [M109](/User_manual/Reference/Gcodes/M109).

This is deprecated because temperatures should be set using the G10/M568 and T commands.

## M105: Get Extruder Temperature

**Parameters**

* *This command can be used without any additional parameters.*
* **Rnnn** Response sequence number^1^
* **Snnn** Response type^1^

**Examples**

```
M105
M105 S2
```

Request the temperature of the current extruder and the build base in degrees Celsius. Reports the current and target temperatures of all active heaters.

**Notes**

^1^ Returns a JSON-formatted response if parameter S2 or S3 is specified. Additionally, parameter Rnn may be provided, where nn is the sequence number of the most recent GCode response that the client has already received. M105 S2 is equivalent to M408 S0, and M105 S3 is equivalent to M408 S2. Usage of these forms of M105 is deprecated, please use M408 instead.

## M106: Fan On

### {.tabset}

#### RepRapFirmware 3.x

**Parameters**

* **Pnnn** Fan number (optional, defaults to 0). Relates to the fan number created by M950,
* **Snnn** Fan speed (0 to 255 or 0.0 to 1.0))
* **Lnnn** Set the minimum fan speed (0 to 255 or 0.0 to 1.0) when a non-zero fan speed is requested.
* **Xnnn** Set the maximum fan speed (0 to 255 or 0.0 to 1.0) when a non-zero fan speed is requested.
* **Bnnn** Blip time - fan will be run at full PWM for this number of seconds when started from standstill. Default is B0.1 which means that there is a 100ms burst after starting the fan.
* **Hnn:nn:nn...** Enable thermostatic mode and select sensor monitored. H-1 disables thermostatic mode. Relates to the sensor number(s) created by M308.
* **Rnnn** Restore fan speed to the value it has when the print was paused (R1) or the last time the fan speed was set and no P parameter was provided (R2).
* **Tnnn** or **Tnn:nn** Set thermostatic mode trigger temperature, or temperature control range
* **C"name"** Set custom name for this fan (supported in RRF >= 2.01)

**Examples**

```
M106 S127
M106 P1 T45 S0.7 H1:2
M106 P1 T40:50 H1:2
```

The first example turns on the default cooling fan at half speed. The second example sets the second fan to a thermostatic fan for sensors 1 and 2 (e.g. the extruder heaters in a dual-nozzle machine) such that the fan will be on at 70% PWM when either hot end is at or above 45C. The third example also sets up a thermostatic fan, but this time it runs in proportional mode. 

```
M308 S10 Y"mcu-temp" A"MCU"                      ; defines sensor 10 as MCU temperature sensor
M308 S11 Y"drivers" A"Duet stepper drivers"      ; defines sensor 11 as stepper driver temperature sensor
M308 S12 Y"drivers-duex" A"Duex stepper drivers" ; for Duet 2 WiFi/Ethernet with DueX2/5, defines sensor 12 as DueX2/5 stepper driver temps
M950 F2 C"fan2" Q100                             ; create fan 2 on pin fan2 and set its frequency
M106 P2 H10:11:12 T40:70                         ; set fan 2 value
```

This example sets up an electronics cooling fan that starts to turn on when the MCU temperature reaches 45C and reaches full speed when the MCU temperature reaches 70C or if any TMC2660 drivers report that they are over-temperature. The sensors are defined with M308, the fan with M950, then the fan is configured with M106. See [Configuring the on-board MCU and stepper driver temperature sensors](/User_manual/Connecting_hardware/Temperature_configuring_mcu_temp#firmware-configuration) for further guidance.

**RepRapFirmware 3 Notes**

The A (logical pin number), F (fan PWM frequency) and I (invert pwm) parameters are no longer supported. Instead, specify the corresponding parameters in the M950 command when you create the fan.

The P parameter relates to the fan number created by M950, NOT the fan pin number on the board as in RRF2.x.

The H parameter relates to the sensor number(s) created by M308, not the temperature sensor pin number on the board as in RRF2.x.

If a fan is configured to trigger on a sensor that represents a stepper driver over-temperature flags (ie M308 ... Y'drivers'), then when the fan turns on it will delay the reporting of an over-temperature warning for the corresponding drivers for a few seconds, to give the fan time to cool the driver down.

If you were using the PB6 tacho input on Duet 2 WiFi/Ethernet running RRF 2.x, you must declare this in a M950 command for the fan concerned in RRF 3.x.

Example
```
; RRF 2.x code
M106 P2 I1 F25000  ; fan 2 is a 4-wire PWM fan so invert it and use high PWM frequency. tacho connected to PB6 on expansion connector. PB6 is defined by default.

; RRF 3.x code
M950 F2 C"!Fan2+exp.pb6" Q25000  ; fan 2 is a 4-wire PWM fan so invert it, use high PWM frequency, tacho connected to PB6 on expansion connector
```

#### RepRapFirmware 2.x

**Parameters**

* **Pnnn** Fan number (optional, defaults to 0). (In RRF2 this relates to the fan pin number on the board)
* **Snnn** Fan speed (0 to 255 or 0.0 to 1.0))
* **Innn** Invert PWM (I1), disable fan (I-1), or normal mode (I0, default)
* **Fnnn** Fan PWM frequency
* **Lnnn** Set the minimum fan speed (0 to 255 or 0.0 to 1.0) when a non-zero fan speed is requested.
* **Xnnn** Set the maximum fan speed (0 to 255 or 0.0 to 1.0) when a non-zero fan speed is requested. (supported in RRF >= 2.02)
* **Bnnn** Blip time - fan will be run at full PWM for this number of seconds when started from standstill. Default is B0.1 which means that there is a 100ms burst after starting the fan.
* **Hnn:nn:nn...** Enable thermostatic mode and select heaters monitored. H-1 disables thermostatic mode. (In RRF2 this relates to the sensor number(s) created by M308, not the temperature sensor pin number on the board)
* **Rnnn** Restore fan speed to the value it has when the print was paused (R1) or the last time the fan speed was set and no P parameter was provided (R2).
* **Tnnn** or **Tnn:nn** Set thermostatic mode trigger temperature, or temperature control range
* **C"name"** Set custom name for this fan (supported in RRF >= 2.01)
* **Ann** Logical pin number that this fan is connected to (supported in RRF >= 2.02) (not supported in RRF3, use M950)

**Examples**

```
M106 S127
M106 P1 T45 S0.7 H1:2
M106 P1 T40:50 H1:2
M106 P2 T45:65 H100:101:102
```

The first example turns on the default cooling fan at half speed. The second example sets the second fan to a thermostatic fan for heaters 1 and 2 (e.g. the extruder heaters in a dual-nozzle machine) such that the fan will be on at 70% PWM when either hot end is at or above 45C. The third example also sets up a thermostatic fan, but this time it runs in proportional mode. The fourth example sets up an electronics cooling fan that starts to turn on when the MCU temperature (virtual heater 100) reaches 45C and reaches full speed when the MCU temperature reaches 65C or if any TMC2660 drivers (virtual heaters 101 and 102) report that they are over-temperature

**RepRapFirmware 2 Notes**

The F parameter sets the fan PWM frequency, in Hz. The default is F250, which works with most fans, try F100 or lower if you find that you can't control the speed of your fan. This parameter is ignored for fans connected to the fan outputs of a DueX2 or DueX5 because those outputs don't support variable PWM frequency.

The I parameter causes the fan output signal to be inverted if its value is greater than zero. This makes the cooling fan output suitable for feeding the PWM input of a 4-wire fan via a diode. If the parameter is present and zero, the output is not inverted. If the I parameter is negative then in RRF 1.16 and later the fan is disabled, which frees up the pin for use as a general purpose I/O pin that can be controlled using M42.

The A parameter can be used to assign a fan to a different output pin, for example a spare heater output (use a M307 command to disable the heater and free up the pin first). 

In firmware versions 1.19 and later, fans can respond to virtual heaters (which have heater numbers 100 upwards) as well as real heaters. If a fan is configured to trigger on a virtual heater whose sensor represents TMC2660 driver over-temperature flags, then when the fan turns on it will delay the reporting of an over-temperature warning for the corresponding drivers for a few seconds, to give the fan time to cool the driver down.

**Notes - all firmware versions**

The parameter 'S' declares the PWM value (0-255 or 0-1.0). M106 S0 turns the fan off.

If an S parameter is provided but no other parameter is present, then the speeds of the print cooling fans associated with the current tool will be set (see the F parameter in the M563 command). If no tool is active then the speed of Fan 0 will be set. Either way, the speed is remembered so that it can be recalled using the R2 parameter (see below).

If no S parameter is given but the R1 parameter is used, the fan speed when the print was last paused will be set, this allows the pause.g macro to switch off the fans and have them resume when the print is resumed. If the R2 parameter is used, then the speeds of the print cooling fans associated with the current tool will be set to the remembered value (see above). R2 allows the configured fan speed to be passed between tools which is useful on multi extruder printers where the slicing software may not specify the fan speed on tool change and the tool change macros shut down fans when not in use.

The T and H parameters allow a fan to be configured to operate in thermostatic mode, for example to use one of the fan channels to control the hot end fan. In this mode the fan will be on with the PWM set by the S parameter (subject to a minimum of 0.5) when the temperature of any of the heaters listed in the H parameter is at or above the trigger temperature set by the T parameter, and off otherwise. Thermostatic mode can be disabled using parameter H-1.

In firmware 1.19 and later, the T parameter may be of the form Taaa:bbb where aaa is the temperature at/below which the fan should be fully off and bbb is the temperature at which the fan should be fully on. The PWM will be set proportionally if the temperature is between these limits.

The B parameter sets the time for which the fan will be operated at full PWM when started from cold, to allow low fan speeds t be used. A value of 0.1 seconds is usually sufficient.

The L parameter defines the minimum PWM value that is usable with this fan. If a lower value is commanded that is not zero, it will be rounded up to this value.

The X parameter defines the same for a maximum PWM value. If a higher value is commanded, it will be rounded down to this value.

## M107: Fan Off

Deprecated in RepRapFirmware. Use M106 S0 instead.

*Note that M107 will turn off the print cooling fan(s) associated with the current tool (see the F parameter in the M563 command).*

## M108: Cancel Heating

Breaks out of an M109 or M190 wait-for-temperature loop, continuing the print job. Use this command with caution! If cold extrusion prevention is enabled (see M302) and the temperature is too low, this will start "printing" without extrusion. If cold extrusion prevention is disabled and the hot-end temperature is too low, the extruder may jam.

## M109: Set Extruder Temperature and Wait

**Parameters**

* **Snnn** minimum target temperature, waits until within 2.5C of target if heating
* **Rnnn** accurate target temperature, waits until within 2.5C of target always
* **Tn** optional tool number

**Examples**

```
M109 S215
```

Deprecated in RRF, but widely generated by slicers. To get the same behaviour, use G10/M568 followed by M116

To better support slicers that don't select a tool when doing a single-extrusion print, RRF will always select a tool when processing M109. If the T parameter is present, that tool will be selected. If no T parameter is present and no tool is already selected, the tool with the lowest non-negative tool number will be selected (typically tool 0).

*Note: M109 will not wait for temperatures below 40c because in many cases they may never be reached due to ambient temps. So if you want to wait for a hotend to cool, use 41c or higher.*

## M110: Set Current Line Number

**Parameters**

* **Nnnn** Line number

**Examples**

```
M110 N123
```

This example sets the current line number to 123. Thus the expected next line after this command will be 124.

## M111: Set Debug Level

**Parameters**

* **Pnn** Debug module number
* **Sn** Debug on (S1), off (S0)

**Examples**

```
M111
M111 P1 S1 ; enable debugging for module 1
```

Enable or disable debugging features for the module number specified by the P parameter. M111 without parameters lists all the modules, their numbers, and whether debugging is enabl d for each..

Note, print quality may be affected when debug output is enabled because of the volume of data sent to USB. Debug output should normally be used only for debugging firmware, or when instructed to help with diagnosis of particular issues.

The details of what debugging information is output when debugging is enabled varies from one firmware revision to another, so it is not specified here.

## M112: Emergency Stop

**Examples**

```
M112
```

Any moves in progress are immediately terminated, then RepRap shuts down. All motors and heaters are turned off. It can be started again by pressing the reset button or power cycling the board. See also M0 and M1.

## M114: Get Current Position

**Examples**

```
M114
```

This reports the X, Y, Z (,U, V, W, A, B, C if configured) and E coordinates to the host. The coordinates reported are those at the end of the last completed move.

For example, the machine returns a string such as:

```
C: X:10.000 Y:20.000 Z:5.000 E:51.000 E0:51.0 E1:0.0 Count 800 1600 2000 Machine 10.00 20.00 5.00 Bed comp 0.00
```

The first E value (without an extruder number) is the current virtual extruder position, and is included for the benefit of GCode senders that don't understand multiple extruders. The E0, E1 etc. values are for each individual extruder. The Count values are the microstep positions of each individual motor or motor group. The Machine values are the machine axis coordinates, which are calculated from the user coordinates by accounting for workplace coordinate offsets, bed compensation, axis skew compensation, babystepping and Z lift.

Note: there is no agreed definition of what the response to M114 should be. We try to keep the M114 response compatible with other firmwares as far as we can, but this is not always possible. Users writing applications which need to fetch current positions from RepRapFirmware 3 are recommended to use object model queries instead.

## M115: Get Firmware Version and Capabilities

**Parameters**

* *This command can be used without any additional parameters.*
* **Pnnn** Electronics type (See Notes)
* **Bnnn** Board number (RRF3/Duet 3 only, see Notes)

**Examples**

```
M115
M115 P2
```

Request the Firmware Version and Capabilities of the current microcontroller.

The details are returned to the host computer as key:value pairs separated by spaces and terminated with a linefeed.:

```
M115
FIRMWARE_NAME: RepRapFirmware for Duet 2 WiFi/Ethernet FIRMWARE_VERSION: 2.04RC1 ELECTRONICS: Duet WiFi 1.0 or 1.01 FIRMWARE_DATE: 2019-07-14b1
```

See the M408 command for a more comprehensive report on machine capabilities.

**Notes**

The 'P' parameter is used tell the firmware about the hardware on which it is running, if RRF can't identify it. Should only be used in config.g, if you're having problems. If the P parameter is present then the integer argument specifies the hardware being used. The following are supported on first-generation Duets:

* M115 P0 - Automatic board type selection if supported, or default if not
* M115 P1 - Duet 0.6
* M115 P2 - Duet 0.7
* M115 P3 - Duet 0.85

The 'B' parameter is used on Duet 3 only, using RRF3. M115 can take an optional B (board number) parameter which is the CAN address of the board to be queried, default 0 (i.e. main board). Example:

```
M115 B1
Board EXP3HC firmware 3.0beta1 2019-10-28b1
```

## M116: Wait

**Parameters**

* *This command can be used without any additional parameters.*
* **Pnnn** Tool number
* **Hnnn** Heater number
* **Cnnn** Chamber number
* **Snn** Tolerance in degC (firmware 2.02/1.23 and later, default 2)

**Examples**

```
M116
M116 P1
M116 H0 S5
```

The first example waits for *all* temperatures and other slowly-changing variables to arrive at their set values.

The second shows the optional 'P' parameter that is used to specify a tool number. If this parameter is present, then the system only waits for temperatures associated with that tool to arrive at their set values. This is useful during tool changes, to wait for the new tool to heat up without necessarily waiting for the old one to cool down fully.

The third example waits for the bed to reach its temperature +-5 degC.

See also M109.

Recent versions of RepRapFirmware also allow a list of the heaters to be specified using the 'H' parameter, and if the 'C' parameter is present, this will indicate that the chamber heater should be waited for.

'S' parameter sets the temperature tolerance, default 2 degC.

*Note: M116 will not wait for temperatures below 40c because in many cases they may never be reached due to ambient temps. So if you want to wait for a hotend or bed to cool, use 41c or higher.*

## M117: Display Message

**Examples**

```
M117 Hello World
M117 "Hello world"
```

This causes the given message to be shown in the status line on an attached LCD or if no LCD is attached, this message will be reported on the web interface. Quotation marks around the string to be displayed are recommended but not mandatory.

RRF >= 3.2.0-beta3: All messages sent via M117 will be logged with log level INFO if logging is enabled at least at log level INFO.

*Note: Due to the way M117 messages are communicated, messages sent in quick succession may not all display. Use M118 for those cases.*

## M118: Send Message to Specific Target

Supported in RepRapFirmware 1.21 and later.

**Parameters**

* **Pnnn** Message type (0 = Generic [default], 1 = USB, 2 = PanelDue/UART, 3 = HTTP, 4 = Telnet, 5 = second UART) (optional)
* **S"msg"** Message to send
* **Lnnn** Log level of this message (0 = do not log this line, 1 = log as WARN, 2 = log as INFO, 3 = log as DEBUG (default)) (RRF >= 3.2.0-beta3)

**Examples**

```
M118 S"Hello Duet"
M118 S"Hello Logfile" L1
M118 P0 S"Hello Logfile and DWC" L1
M118 S"Don't log me" L0
```

This code may be used to send messages to a specific target. Basically it is a simple wrapper for RepRapFirmware's Platform::Message method.

Note that the implementation in RepRapFirmware always requires the S-parameter to be passed. If it is omitted, an error will be reported.

The second example shows how to send a message to the log file in log level WARN instead of the default log level DEBUG.

The third example shows how to prevent a message from being logged.

Note that a message only having the **Lnnn** parameter but no **Pnnn** parameter will only go to the log file (if the log level matches the current log level of the system) and will not be sent to other outputs. If you want it to show on DWC as well then send:

```
M118 P0 S"message" L1
```

## M119: Get Endstop Status

**Examples**

```
M119
```

Returns the current state of the configured endstops. Takes into account any 'inverted endstop' settings, so one can confirm that the machine is interpreting the endstops correctly.

## M120: Push

**Examples**

```
M120
```

Push the state of the machine onto a stack. Called automatically when a macro file is run. It pushes the following values on the stack:

1. Current feedrate
1. Extruder positions
1. Whether moves (and separately extrusion) are relative or absolute

## M121: Pop

**Examples**

```
M121
```

Recover the last state pushed onto the stack.

## M122: Diagnose

**Parameters**

* *This command can be used without any additional parameters.*
* **Pnnn** report specific information (See Notes)
* **Bnnn** Board number (RRF3/Duet 3 only, see Notes)
* **"DSF"** Immediate DSF diagnostics (RRF3/Duet3 only with attached SBC)

**Examples**

```
M122
```

Sending an M122 causes the RepRap to transmit diagnostic information.

**Notes**

The 'P' parameter is used to report specific information. The details vary between releases. As at RepRapFirmware version 2.03 they are:

* P1 print summary test report (additional parameters: Taa:bb = min/max accepted MCU temperature reading, Vaa:bb = min/max VIN voltage reading, Waa:bb = min/max 12V regulator voltage reading if applicable)
* P100 print a summary of recent moves (only if move logging is enabled in the firmware build)
* P101 print the status of an attached DueX expansion board
* P102 print how long it takes to evaluate the square root of a 62-bit unsigned integer
* P103 print how long it takes to evaluate sine and cosine
* P104 print how long it takes to write a file to the SD card (specify the file size in Mbytes in the S parameter, default 10)
* P105 print the sizes of various objects used by RepRapFirmware

Note: do not use M122 with a P parameter of 1000 or greater. There are a few values that deliberately cause the firmware to crash, which are used to test the error reporting facilities. As at firmware 2.03 these are:

* P1001 cause a watchdog reset
* P1002 test what happens when a module gets stuck in a spin loop
* P1003 test what happens when we write a blocking message to USB
* P1004 test integer division by zero
* P1005 test the response to an unaligned memory access
* P1006 test the response to accessing an invalid region of memory

The 'B' parameter is used in RepRapFirmware 3 on Duet 3 only, to report diagnostic information from connected boards. The B (board number) parameter is the CAN address of the board to be queried, default 0 (i.e. main board). Example:

```
M122 B1
Diagnostics for board 1:
Board EXP3HC firmware 3.0beta1 2019-10-28b1
Never used RAM 163.4Kb, max stack 376b
HEAT 1284 CanAsync 1456 CanRecv 1424 TMC 168 AIN 532 MAIN 2220
Driver 0: standstill, reads 26609, writes 11 timeouts 0, SG min/max 0/0
Driver 1: standstill, reads 26611, writes 11 timeouts 0, SG min/max 0/0
Driver 2: standstill, reads 26614, writes 11 timeouts 0, SG min/max 0/0
Move hiccups: 0
VIN: 24.4V, V12: 12.2V
MCU temperature: min 43.8C, current 43.9C, max 44.1C
```

## M135: Set PID sample interval

*NOTE: removed in RRF 2.03beta2*

**Parameters**

* **Snnn** Heat sample time in milliseconds

**Examples**

```
M135 S300
```

Set the PID to measure temperatures and calculate the power to send to the heaters every 300ms.

## M140: Set Bed Temperature (Fast) or Configure Bed Heater

**Parameters**

* **Pnnn** (RRF 1.20 and later) Bed heater index, default 0
* **Hnnn** Heater number
* **Snnn** Active/Target temperature
* **Rnnn** Standby temperature

**Order dependency**

In RRF3 a M140 command with H parameter (other than H-1) must come after the M950 command that creates that heater, and before any M143 command that sets a temperature limit for that heater.

**Examples**

```
M140 H0
M140 S55
M140 S65 R40
M140 S-273
```

The first example informs the firmware that bed heater 0 (implied, because no P parameter is provided) uses heater 0.
The second example sets the temperature of the bed heater to 55^o^C and returns control to the host immediately (*i.e.* before that temperature has been reached by the bed).
The third example sets the bed temperature to 65^o^C and the bed standby temperature to 40^o^C.
The fourth example sets the active/target bed temperature to absolute negative temperature (-273 or lower). This switches off the bed heater.

**Notes**

RepRapFirmware also provides an optional 'H' parameter to set the hot bed heater number(s). If no heated bed is present, a negative value may be specified to disable it. M140 commands with H parameters would normally be used only in the config.g file.

On the Duet 3 MB6HC you can configure up to 12 bed heaters; on Duet 3 Mini 5+, 2 bed heaters; on Duet 2 WiFi/Ethernet, 4 bed heaters; on Duet 2 Maestro, 2 bed heaters.


## M141: Set Chamber Temperature (Fast) or Configure Chamber Heater

**Parameters**

* **Pnnn** (RRF 2.03 and later only) Chamber heater index, default 0
* **Hnnn** Heater number
* **Snnn** Active/Target temperature
* **Rnnn** Standby temperature

**Order dependency**

In RRF3 a M141 command with H parameter (other than H-1) must come after the M950 command that creates that heater, and before any M143 command that sets a temperature limit for that heater.

**Examples**

```
M141 S30
M141 H3
```

The first example sets the temperature of the chamber to 30^o^C and return control to the host immediately (*i.e.* before that temperature has been reached by the chamber).
The second example specifies that chamber heater 0 uses heater 3.

**Notes**

M141 commands with H parameters would normally be used only in the config.g file.

On the Duet 3 MB6HC you can configure up to 4 chamber heaters; on Duet 3 Mini 5+, 2 chamber heaters; on Duet 2 WiFi/Ethernet, 4 chamber heaters; on Duet 2 Maestro, 2 chamber heaters.

## M143: Maximum heater temperature

### {.tabset}

#### M143 in RRF 3.01RC2 and later

**Parameters**

* **H** Heater number to monitor (default 1 which is normally the first hot end)
* **S** Maximum permitted temperature
* **P** Heater monitor number, default 0
* **T** Sensor number used to monitor the heater. It default to the sensor that controls the heater (as was specified in the M305 command when the heater was created).
* **A** Action to trigger (0: Generate heater fault [default] 1: Switch off permanently 2: Switch off temporarily 3: Shut down the printer)^1^
* **C** Condition for temperature event (0: Temperature too high [default] 1: Temperature too low, -1: Monitor is disabled)

Each heater supports a certain number (3 in most builds of RRF) of monitors for that heater. The P parameter allows you to choose which monitor to configure.

By default, monitor 0 is set up to generate a heater fault if a temperature limit is exceeded, and monitors 1 and 2 are disabled.

**Examples**

```
M143 H1 P0 S275 A2 ; switch off heater 1 temporarily if it exceeds 275°C
M143 H1 P1 S285 A0 ; raise a heater fault if it exceeds 285C
```

#### M143 in RRF 3.01RC1 and earlier

**Parameters**

* **H** Heater number to turn off if an anomaly is detected (default 1 which is normally the first hot end). This must be a real heater.
* **S** Maximum permitted temperature
* **P** Heater protection instance (defaults to H parameter if omitted)^1^
* **X** Heater number whose temperature sensor is used to monitor the heater specified in the H parameter. This can be a virtual heater. It default to the value of the H parameter.^1^
* **A** Action to trigger (0: Generate heater fault [default] 1: Switch off permanently 2: Switch off temporarily)^1^
* **C** Condition for temperature event (0: Temperature too high [default] 1: Temperature too low)^1^

**Examples**

```
M143 S275 ; set the maximum temperature of the hot-end to 275°C
M143 H0 S125 ; set the maximum bed temperature to 125C
M143 H1 S275 X103 ; use virtual heater 103 to monitor heater 1
```

You have heater 3 mapped to a chamber heater which is supposed to be temporarily turned off when the temperature in it exceeds 65C. The thermistor for the chamber is set up as a virtual heater on channel 104 and can be viewed on the "Extra" panel on DWC (refer to M305 for further details on how to set this up). To achieve this you can configure an extra heater protection with the following GCode:
```
M143 P100 H3 X104 A2 C0 S65
```

**RepRapFirmware 2.x notes**

To control heaters via two thermistors, RepRapFirmware allows the configuration of extra heater protection elements whose index starts from 100. On the Duet 2 Wifi and Duet 2 Ethernet there are 8 extra heater protections available (100-107). See also [M305](/User_manual/Reference/Gcodes/M305).

**Order dependency**

If the heater is a bed or chamber heater then the M143 command must come after the M140 or M141 command that declares the heater as a bed or chamber heater.

**Notes**

^1^ Supported in RepRapFirmware 1.20 and later. Starting from this version RepRapFirmware allows more granular control over the heater subsystem. By default each heater has one heater protection instance assigned to it, which is by default configured to generate a heater fault if the maximum heater temperature is exceeded.

The default maximum temperature for all heaters was 300°C prior to RepRapFirmware version 1.13, and 262°C from 1.13. At RepRapFirmware 1.17 the default maximum temperatures were 262C for extruders and 125C for the bed. In more recent versions the default maximum heater temperature is 290C, to allow the hot end to be tightened at 285C as per the E3D recommendation. When the temperature of the heater exceeds this value a heater error will be triggered.

With A0 set on RepRapFirmware 1.26.1, PS_ON is triggered after the fault has exisited for the duration defined by the S parameter set in M570.

## M144: Bed Standby

**Parameters**

* **Pnnn** Bed index (supported in RepRapFirmware 1.20 and later)
* **Sn** 0 = put bed heater on standby (default), 1 = make bed heater active

**Examples**

```
M144
```

Switch the bed to its standby temperature. M144 S1 will set it back to its active temperature.

## M150: Set LED colours

**Parameters**

* **Rnnn** Red component, 0-255
* **Unnn** Green component, 0-255
* **Bnnn** Blue component, 0-255
* **Wnnn** White component, 0-255 (Only for RGBW NeoPixel, RepRapFirmware 3.3 and later)
* **Pnnn** Brightness, 0-255 (RepRapFirmware 2.03 and later)
* **Snnn** Number of individual LEDs to set to these colours
* **Fn** Following command action. F0 (default) means this is the last command for the LED strip, so the next M150 command starts at the beginning of the strip. F1 means further M150 commands for the remainder of the strip follow this one.
* **Xn** LED type: X0 = DotStar (default prior to RRF 3.2), X1 = RGB NeoPixel (default in RRF 3.2 and later), X2 = bit-banged RGB NeoPixel, X3 = RGBW NeoPixel (from RRF 3.3), X4 = bit-banged RGBW NeoPixel (from RRF3). This parameter is remembered from one call to the next, so it only needs to be given once. Not all boards support all the modes. On the Duet 3 Mini, X1 selects the NeoPixel output on the main board, and X2 addresses the RGB LEDs on some 12864 displays.
* **Ynn** 'Brightness, 0-31 (alternative to P 0-255)
* **Qnnn** (optional) Use specified SPI frequency (in Hz) instead of the default frequency. This parameter is not normally needed, and is only processed if X parameter also present. When using NeoPixels, only frequencies in the range 2.4 to 4MHz will work.

**Examples**

```
M150 X1 Q3000000      ; set LED type to NeoPixel and set SPI frequency to 3MHz
M150 R255 P128 S20 F1    ; set first 20 LEDs to red, half brightness, more commands for the strip follow
M150 U255 B255 P255 S20   ; set next 20 LEDs to cyan, full brightness, finished programming strip
```

This command is only supported on controllers that have an output connector for DotStar or NeoPixel LEDs.

The specified RGB values will be sent to the number of LEDs in the LED strip as specified by the S parameter, pushing the existing colours along the strip. To set all the LEDs the same colour, make the S parameter equal to or a little longer than the number of LEDs in the strip.

Оn **Fysetc 12864mini** you can configure all three LEDs separately. For display and for encoder illumination:

**Examples**

```
M918 P2 E-4 F2000000           ; Fysetc 12864mini
M150 X2 R255 U0 B0 P255 S1 F1      ; display led
M150 X2 R0 U255 B0 P255 S1 F1      ; left encoder led
M150 X2 R0 U255 B0 P255 S1 F0      ; right encoder led
```

## M190: Wait for bed temperature to reach target temp

**Parameters**

* **Snnn** minimum target temperature, waits while heating
* **Pnnn** Bed index (supported in RepRapFirmware 1.20 and later)
* **Rnnn** accurate target temperature, waits while heating and cooling

**Examples**

```
M190 S60
```

Set the temperature of the bed to 60C and wait for the temperature to be reached.

*Note: M190 will not wait for temperatures below 40c because in many cases they may never be reached due to ambient temps. So if you want to wait for a bed to cool, use 41c or higher.*

## M191: Wait for chamber temperature to reach target temp

**Parameters**

* **Snnn** minimum target temperature, waits while heating
* **Rnnn** accurate target temperature, waits while heating and cooling
* **Pnnn** Chamber index. This defaults to 0 and the maximum is dependent on the board type. (Supported in RepRapFirmware 1.20 and later)

**Examples**

```
M191 S60
```

Set the temperature of the build chamber to 60C and wait for the temperature to be reached.

## M200: Set filament diameter

**Parameters**

* **Daaa:bbb:ccc...** Sets filament diameter to aaa for extruder 0, bbb for extruder 1 and so on. If any of aaa, bbb etc. are zero then volumetric extrusion is disabled for that extruder.
* **Daaa** Sets filament diameter (or disables volumetric extrusion) for all extruders

**Examples**

```
M200 D0 ; disable volumetric extrusion on all extruders
M200 D1.75 ; set all extruder filament diameters to 1.75mm
M200 D1.75:3.0:1.75 ; set extruder 0 to 1.75mm, extruder 1 to 3.0mm and all remaining extruders to 1.75mm
```

**Notes**

Volumetric extrusion is an option you can set in some slicers whereby all extrusion amounts are specified in mm^3^ (cubic millimetres) of filament instead of mm of filament. This makes the GCode independent of the filament diameter, potentially allowing the same GCode to run on different printers. The purpose of the M200 command is to inform the firmware that the GCode input files have been sliced for volumetric extrusion, and to provide the filament diameter so that the firmware can adjust the requested extrusion amount accordingly.

Sending M200 without parameters reports the current volumetric extrusion state and (where appropriate) filament diameter for each extruder.

Note that if you use slicer-commanded retraction, the retraction amounts must be specified in mm^3^ too. If instead you use firmware retraction, then the firmware retraction amounts specified using the M207 command are still interpreted as mm.

This command is supported in RepRapFirmware 1.19beta10 and later.

## M201: Set max acceleration

**Parameters**

* **Xnnn** Acceleration for X axis
* **Ynnn** Acceleration for Y axis
* **Znnn** Acceleration for Z axis
* **Unnn** Acceleration for U axis
* **Vnnn** Acceleration for V axis
* **Wnnn** Acceleration for W axis
* **Ennn** Acceleration for extruder drives

**Order dependency**

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

**Examples**

```
M201 X1000 Y1000 Z100 E2000
```

**Notes**

Sets the acceleration that axes can do in mm/second^2 for print moves. For consistency with the rest of GCode movement this should be in mm/(minute^2), but that gives really silly numbers and one can get lost in all the zeros. So for this we use seconds.

To calculate the maximum acceleration values for an axis an online [Maximum Acceleration Calculator](https://wilriker.github.io/maximum-acceleration-calculator) can be used.

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour.

Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M201 Z100, not M201 Z100:100

## M201.1: Set reduced acceleration for special move types

**Parameters**

* **Xnnn** Acceleration for X axis in units/s2
* **Ynnn** Acceleration for Y axis in units/s2
* **Znnn** Acceleration for Z axis in units/s2
* **Ennn:nnn...** Acceleration for the extruders in units/s2

**Examples**

```
M201.1 X500 Y500 Z20 E500:500
```

**Notes**

Set the acceleration that axes should use for special types of move that should be done using reduced acceleration.

These values are used for probing moves (because some types of Z probe can be triggered by high acceleration at the start of the move) and for moves that involve stall detection endstops (because high acceleration can bring the motor close to stalling). If a single E value is provided, that value is applied to all extruders. The values must be provided in mm/sec^2^ even if G20 has been used to set units to inches. M201.1 without parameters reports the current settings.

## M203: Set maximum feedrate

**Parameters**

* **Xnnn** Maximum feedrate for X axis
* **Ynnn** Maximum feedrate for Y axis
* **Znnn** Maximum feedrate for Z axis
* **Unnn** Maximum feedrate for U axis
* **Vnnn** Maximum feedrate for V axis
* **Wnnn** Maximum feedrate for W axis
* **Ennn:nnn...** Maximum feedrates for extruder drives
* **Innn** Minimum overall movement speed (firmware 2.03 and later), default 30mm/min

**Order dependency**

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

**Examples**

```
M203 X6000 Y6000 Z300 E10000
```

**Notes**

Sets the maximum feedrates that your machine can do in mm/min

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour.

Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M203 Z300, not M203 Z300,300

## M204: Set printing and travel accelerations

**Parameters**

* **Pnnn** Acceleration for printing moves
* **Tnnn** Acceleration for travel moves

**Examples**

```
M204 P500 T2000
```

**Notes**

Use M201 to set per-axis accelerations and extruder accelerations. RepRapFirmware applies the M204 accelerations to the move as a whole, and also applies the limits set by M201 to each axis and extruder.

Values are in mm/s².

This command is supported by firmware version 1.18RC1 and later.

## M205: Set max instantaneous speed change in mm/sec

* **Xnnn** X axis max instantaneous speed change in mm/sec
* **Ynnn** Y axis max instantaneous speed change in mm/sec
* **Znnn** Z axis max instantaneous speed change in mm/sec
* **Unnn, Vnnn, Wnnn etc.** U, V, W axis max instantaneous speed change in mm/sec

**Order dependency**

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

**Notes**

This command is provided as an alternative to [M566](/User_manual/Reference/Gcodes/M566) for compatibility with Marlin. In M566 the units are mm/min as with all other speeds. In M205 they are in mm/sec.

## M206: Offset axes

*This command is deprecated. Use G10 L2 P1 instead.*

**Parameters**

* **Xnnn** X axis offset
* **Ynnn** Y axis offset
* **Znnn** Z axis offset
* **Unnn** U axis offset
* **Vnnn** V axis offset
* **Wnnn** W axis offset

**Order dependency**

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

**Examples**

```
M206 X10.0 Y10.0 Z-0.4
```

The values specified will be subtracted from the coordinates given in G0, G1 and related commands. In firmware builds that support workplace coordinates, this command is equivalent to G10 P1 L2 with the X, Y, Z... parameters negated.

## M207: Set retract length

**Parameters**

* **Pn** Tool number (optional, supported in RRF 3.01 and later only)
* **Snnn** positive length to retract, in mm
* **Rnnn** positive or negative additional length to un-retract, in mm, default zero
* **Fnnn** retraction feedrate, in mm/min
* **Tnnn** feedrate for un-retraction if different from retraction, mm/min (RepRapFirmware 1.16 and later only)
* **Znnn** additional zlift/hop

**Examples**

```
M207 S4.0 F2400 Z0.075
```

Sets the retract length used by the G10 and G11 firmware retraction and reprime commands. In RRF 3.01 and later, if a P parameter is provided then only the retraction parameters for the specified tool will be set. In other cases, the new retraction parameters will apply to all tools.

## M208: Set axis max travel

**Parameters**

* **Snnn** 0 = set axis maximum (default), 1 = set axis minimum
* **Xnnn** X axis limit
* **Ynnn** Y axis limit
* **Znnn** Z axis limit

Alternative (from RepRapFirmware 2.02/1.23)

* **Xaaa:bbb** X axis minimum and maximum limit
* **Yaaa:bbb** Y axis minimum and maximum limit
* **Zaaa:bbb** Z axis minimum and maximum limit

**Order dependency**

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

**Examples**

```
M208 X200 Y200 Z90 ; set axis maxima
M208 X-5 Y0 Z0 S1 ; set axis minima
M208 X-5:200 Y0:200 Z0:90 ; set axis minima and maxima
```

**Notes**

The values specified set the software limits for axis travel in the specified direction. The axis limits you set are also the positions assumed when an endstop is triggered.

The M208 minimum Z value applies to deltas. The M208 XY min/max and Z max values don't.

## M220: Set speed factor override percentage

**Parameters**

* **Snnn** Speed factor override percentage (0..100 or higher)

**Examples**

```
M220 S80
```

Sets the speed factor override percentage.

## M221: Set extrude factor override percentage

**Parameters**

* **Snnn** Extrude factor override percentage (0..100 or higher), default 100%
* **Dnnn** Extruder drive number (RepRapFirmware only), default 0

**Examples**

```
M221 S70
M221 S95 D1
```

Sets extrude factor override percentage. In the case of RepRapFirmware, sets the extrusion factor percentage for the specified extruder drive only.

## M226: Synchronous Pause

**Examples**

```
M226
```

**Notes**

Initiates a pause in the same way as if the pause button is pressed, except that execution of all prior GCode commands in the same input stream is completed first. Then the SD card input stream is paused and file sys/pause.g is run.

M226 is intended for use in the GCode file being printed, for example to pause after a particular layer has completed. So it waits until all the moves in the queue have been completed. M25 is intended for use from a different source of GCodes (such as the web interface console, PanelDue or a Macro), so if you need to pause from those use M25 instead.

## M260: i2c Send and/or request Data

Send and/or receive data over the i2c bus. Supported in RepRapFirmware 1.21 and later.

**Parameters**

* **Ann** I2C address
* **Bnn:nn:nn...** Bytes to send (optional in firmware 2.02 and later)
* **Rnn** Number of bytes to receive (optional) - firmware 2.02 and later only

**Examples**

```
M260 A5 B65                   ; Send 'A' to address 5 now
M260 A"x7F" B65               ; Send 'A' to address 7F (hex)
M260 A0 B82:101:112:82:97:112 ; Send 'RepRap' to address 0
M260 A"x71" B19 R2            ; send 19 to address 71 (hex) and read 2 bytes back
```

Hex addresses are only supported in firmware 2.02 and later.

## M261: i2c Request Data

Deprecated in RRF 2.02 and later. Use M260 instead.

**Parameters**

* **Ann** I2C address
* **Bnn** How many bytes to request

**Examples**

```
M261 A99 B5 ; Request 5 bytes from Address 99
```

## M280: Set servo position

### {.tabset}

#### RepRapFirmware 3.x

**Parameters**

* **Pnnn** Servo index
* **Snnn** Angle (see notes) or microseconds

**Examples**

```
M950 S0 C"exp.heater3"  ; assign GPIO port 0 to heater3 on expansion connector, servo mode
...
M280 P0 S80  ; set 80deg servo position on GPIO port 0
```

**RRF 3.x notes**

Before you can use M280 you must create a GPIO port using M950. Then in the M280 command, the P parameter is the GPIO port number. 

To invert the output, specify an inverted pin name in M950 when you create the GPIO port.

In RRF 3.4, Duet 3 supports up to 32 output and 16 input ports, Duet 2 Wifi/Ethernet support up to 20 GPIO ports, and Duet 2 Maestro supports 10 GPIO ports. No GPIO ports are allocated by default.

#### RepRapFirmware 2.x

**Parameters**

* **Pnnn** Servo index
* **Snnn** Angle (see notes) or microseconds
* **I1** Invert polarity (not supported in RRF3)

**Examples**

```
M280 P1 S50    ; set Heater 1 pin to 50deg servo position
M280 P3 I1 S80 ; set Heater 3 pin to 80deg servo position, inverted
```

**RRF 2.x notes**

The optional I1 parameter causes the polarity of the servo pulses to be inverted compared to normal for that output pin. The I parameter is not remembered between M280 commands (unlike the I parameter in M106 commands), so if you need inverted polarity then you must include I1 in every M280 command you send.

The servo index is the same as the pin number for the M42 command.

**Notes**

S values below 544 are treated as angles, and 544 or greater as the pulse width in microseconds.

The relationship between the S parameter and the pulse width output to the port is the same as in other 3D printer firmwares, so that devices such as BLTouch will perform the same way. However, **there is no standard for servos on the relationship between pulse width and servo angle**. Therefore, **for most servos the value of the S parameter does not equal the servo angle**. Almost all servos accept a pulse width range of at least 1us to 2us, which corresponds to an S parameter range of 44.2 to 141.2 degrees. So for many servos, values in the range 44.2 to 141.2 or alternatively 1000 to 2000 will cover the full operating range of the servo.

See also [Using hobby servos and DC motors](/User_manual/Connecting_hardware/Motors_servos).

## M290: Baby stepping

Supported in firmware version 1.18 and later.

**Parameters**

* **Snnn** Amount to baby step Z in mm. Positive values raise the head or lower the bed, negative values do the opposite.
* **Znnn** Synonym for S (RepRapFirmware 1.21 and later)
* **X,Y,U...** Amount to babystep other axes (RRF 2.03 and later)
* **Rn** (Optional, RepRapFirmware 1.21 and later) R1 = relative (add to any existing babystep amount, the default), R0 = absolute (set babystepping offset to the specified amount)

**Examples**

```
M290 S0.05  ; babystep the head up 0.05mm
M290 R0 S0  ; clear babystepping (RepRapFirmware 1.21 and later only)
```

**Notes**

This command tells the printer to apply the specified additional offset to the Z coordinate for all future moves, and to apply the offset to moves that have already been queued if this can be done. Baby stepping is cumulative, for example after M290 S0.1 followed by M290 S-0.02, an offset of 0.08mm is used.

M290 with no parameters reports the accumulated baby stepping offset. Marlin doesn't track accumulated babysteps.

In RepRapFirmware 1.19 and earlier, the babystepping offset is reset to zero when the printer is homed or the bed is probed. In RepRapFirmware 1.21 and later, homing and bed probing don't reset babystepping, but you can reset it explicitly using M290 R0 S0.

## M291: Display message and optionally wait for response

Supported in firmware version 1.19 and later.

**Parameters**

* **P"message"** The message to display, which must be enclosed in double quotation marks. If the message itself contains a double quotation mark, use two double quotation marks to represent it.
* **R"message"** Optional title for the message box. Must be enclosed in double quotation marks too.
* **Sn** Message box mode (see below), default 1
* **Tn** Timeout in seconds, ignored if S=2 or S=3. The message will be cancelled after this amount of time, if the user does not cancel it before then. A zero or negative value means that the message does not time out (it may still be cancelled by the user if it has a Close button). The default value is 10 seconds (this applies to modes 0 and 1 only).
* **X, Y, Zn** 0 = no special action (default), 1 = display jog buttons alongside the message to allow the user to adjust the head position on the specified axis. Only valid in conjunction with S2 or S3.

**Description**

This command provides a more flexible alternative to M117, in particular messages that time out, messages that suspend execution until the user acknowledges them, and messages that allow the user to adjust the height of the print head before acknowledging them.

Allowed message box modes (S parameter) are:

```
0. No buttons are displayed (non-blocking)
1. Only "Close" is displayed (non-blocking)
2. Only "OK" is displayed (blocking, send M292 to resume the execution)
3. "OK" and "Cancel" are displayed (blocking, send M292 to resume the execution or M292 P1 to cancel the operation in progress)
```

**Notes**

The combination S0 T0 is not permitted, because that would generate a message box with no close button and that never times out, which would lock up the user interface.

Duet Web Control 2.03 and later support HTML messages but that may not be displayed correctly on an attached PanelDue.

When using Duet 3 with attached SBC, DSF versions before v3.1.1 support only non-blocking calls are supported in DuetSoftwareFramework. M291 is fully supported in DSF v3.1.1 and later.

The limit in RRF3 is 200 characters in the entire GCode command. In RRF2 it's 160 characters.

## M292: Acknowledge blocking message

**Caution**: do not use this command in macros!

**Parameters**

* **Pnnn** Whether the current operation shall be cancelled (P=1) or continued (P=0).

This command is sent by the user interface when the user acknowledges a message that was displayed because of a M291 command with parameter S=2 or S=3. The P parameter is ignored unless M291 was called with S=3.

Supported in firmware version 1.19 and later.

## M300: Play beep sound

**Parameters**

* **Snnn** frequency in Hz
* **Pnnn** duration in milliseconds

**Examples**

```
M300 S300 P1000
```

**Notes**

Play beep sound, use to notify events like the end of printing. If an LCD device is attached to RepRapFirmware, a sound is played via the add-on touch screen control panel. Else the web interface will play a beep sound.

If you intend to play multiple notes in a row, you will need to insert a G4 delay command between them at least equal to the length of the tone.

Example:

```
M300 S2000 P200
G4 P200
M300 S2500 P300
G4 P300
```

See also [Macros, sounds section](/User_manual/Tuning/Macros#sounds)

## M301: Set PID parameters

**Parameters**

* **Hnnn** heater number
* **Pnnn** proportional (Kp)
* **Innn** integral (Ki)
* **Dnnn** derivative (Kd)

**Examples**

```
M301 H1 ; Report PID values
M301 H1 P20 I0.5 D100 ; Set PID values
```

**Notes**

Sets Proportional (P), Integral (I) and Derivative (D) values for hot end. See also M303

* H: Is the heater number, and is compulsory. H0 is the bed, H1 is the first hot end, H2 the second etc.
* P: Proportional value
* I: Integral value
* D: Derivative value

The P, I and D values must be provided scaled by a factor of 255, for compatibility with older firmwares.

Note: PID parameters are computed automatically when the M307 command is used to define the heater model, or from the default heater model if no M307 command is provided. You can use M301 to override those computed PID parameters, but this is not recommended because it prevents RepRapFirmware from using different PID parameters depending on the heating phase.

## M302: Allow cold extrudes

**Parameters**

* *This command can be used without any additional parameters.*
* **Pnnn** Cold extrude allow state
* **Snnn** Minimum extrusion temperature (RepRapFirmware 2.02 and later)
* **Rnnn** Minimum retraction temperature (RepRapFirmware 2.02 and later)

**Examples**

```
M302 ; Report current state
M302 P1 ; Allow cold extrusion
M302 S120 R110 ; Allow extrusion starting from 120°C and retractions already from 110°C
```

**Notes**

This tells the printer to only allow movement of the extruder motor above a certain temperature, or if disabled, to allow extruder movement when the hotend is below a safe printing temperature.

The minimum temperatures for extrusion can be set using the Snnn parameter with a default value of 160°C if unset. A minimum retraction temperature can be set with the Rnnn parameter. The default for this is 90°C.

M302 with no parameters it will report the current cold extrusion state.

One limitation of M302 is that it requires a thermistor to be present for the temperature to be monitored. If your system does not have a thermistor or heater to be monitored, you can define your tool in M563 without a heater to disable cold extrusion protection on that tool. Example: 
```
M563 P0 S"Pump" D0 F0
``` 
Note the lack of H parameter.

## M303: Run heater tuning

**Parameters**

* **Hnnn** heater number (in RRF 3.2 and later, this parameter is optional if the T parameter is given)
* **Pnnn** PWM to use, 0 to 1 (you should normally use 1 i.e. full power), default 1
* **Snnn** target temperature
* **Tnnn** (RRF 3.2 and later, optional) Tool whose primary heater is to be tuned
* **Annn** (RRF 3.2 and later, optional) ambient temperature - use this parameter if you want to tune a heater that has been on and has not cooled down to ambient temperature yet
* **Ynn** (RRF 3.3beta3 and later optional) Tuning cycle hysteresis, default 5C. When tuning bed heaters that are slow to cool down, tuning will be faster if you use a lower value, provided that there is no noise in the temperature readings.
* **F**nn (RRF 3.3beta3 and later) Fan PWM to use when the print cooling fan is turned on (ignored if the T parameter is not present), default 1.0. Use a lower value if your printer uses a powerful print cooling fan that you do not normally run at full PWM.

**Examples**

```
M303 H1 P1 S240 ; tune heater 1 using 100% PWM, target temperature 240C
M303 T0 S205 ; tune the primary heater of tool 0 (RRF 3.2beta3.2 and later)
```

**Notes**

PID Tuning refers to a control algorithm used to tune heating behaviour for hot ends and heated beds. This command computes the process model parameters (see [M307](/User_manual/Reference/Gcodes/M307)), which are in turn used to calculate the PID constants. Note that the tuning algorithm will overshoot the target temperature; the amount of overshoot depends on how powerful the heater is. See [Tuning heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning).

Tuning is performed asynchronously. Run M303 with no parameters while a tuning is underway to see the current tuning state, or the last tuning result if the tuning process has already completed.

## M304: Set PID parameters - Bed

**Parameters**

* *This command can be used without any additional parameters.*
* **Pnnn** proportional (Kp)
* **Innn** integral (Ki)
* **Dnnn** derivative (Kd)

**Examples**

```
M304 P1 I2 D3
M304 ; Report parameters
```

**Notes**

Sets Proportional, Integral and Derivative values for bed. This command is identical to M301 except that the H parameter (heater number) defaults to zero.

## M305: Set temperature sensor parameters

RepRapFirmware 3: Use M308 instead (see Notes).

**Parameters**

* **Pnnn** Heater number (0, 1, 2...) or virtual heater number (100, 101, 102...)
* **S"name"** Heater name (optional). Named virtual heaters are shown in Duet Web Control; anonymous virtual heaters are not.
* **Xnnn** Heater ADC channel, or thermocouple or PT100 adapter channel; defaults to the same value as the P parameter
* **Tnnn** (for thermistor sensors) The thermistor resistance at 25^o^C
* **T"c"** (for MAX31856-based thermocouple sensor daughter boards) The thermocouple type letter, default K
* **Bnnn** If the sensor is a thermistor, this is the Beta value. For the Steinhart-Hart thermistor model, this is the *reciprocal* of the B coefficient
* **Cnnn** If the sensor is a thermistor, this is the Steinhart-Hart C coefficient, default 0
* **Rnnn** If the sensor is a thermistor or PT1000 sensor, this is the Series resistor value, see here for more information: [M305 R parameter](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000#the-m305m308-r-parameter).
* **Lnnn** If the sensor is a thermistor, this is the ADC low offset. If it is a current loop sensor, this is the temperature when the current is 4mA.
* **Hnnn** If the sensor is a thermistor, this is the ADC high offset. If it is a current loop sensor, this is the temperature when the current is 20mA.
* **Fnn** (where nn is 50 or 60) If the sensor interface uses a MAX31856 thermocouple chip or MAX31865 PT100 chip, this is the local mains frequency. Readings will be timed to optimise rejection of interference at this frequency.
* **Wn** Configure number of wires used to connect PT100 sensor. Should be [2..4].

**Examples**

```
M305 P1 T100000 R1000 B4200
```

**Description**

Sets the parameters for temperature measurement. The example above tells the firmware that for heater 1 (P parameter: 0 = heated bed, 1 = first extruder) the thermistor 25C resistance (T parameter) is 100Kohms, the thermistor series resistance (R parameter) is 1Kohms, the thermistor beta (B parameter) is 4200. All parameters other than P are optional. If only the P parameter is given, the existing values are displayed. DuetWebControl 1.19.2 and newer support optional units to be set by the S parameter in the form of "Heater name [Unit]".

**Notes**

**Notes - RepRapFirmware 3**

Prior to RRF3, every temperature sensor belongs to a heater. For sensors with no controllable heater (e.g. the MCU temperature sensor) you have to create a "virtual heater" in order to be able to use the sensor. In RRF3, sensors are created and configured independently from heaters, using the M308 command. When creating a heater using M950, you tell it which sensor to use. You must create the sensor before you refer to it in a M950 command. M305 is not used.

**Notes - RepRapFirmware 2.x and earlier**

RepRapFirmware also supports ADC gain and offset correction and a thermistor selection facility. Example:
```
M305 P1 T100000 R1000 B4200 H14 L-11 X2
```
The H correction affects the reading at high ADC input voltages, so it has the greatest effect at low temperatures. The L correction affects the reading at low input voltages, which correspond to high temperatures. The ADC on the Duet 2 WiFi and Duet 2 Ethernet is self-calibrating, so you should not need to provide any corrections when using these controllers.

The X parameter tells the firmware which temperature sensor channel to use channel, as follows:

* Channels 0, 1... are the thermistor inputs for heaters 0, 1 etc.
* Channels 100, 101... are MAX31855 thermocouple channels using chip selects CS1, CS2... on the SPI bus, see [Connecting thermocouples](/User_manual/Connecting_hardware/Temperature_connecting_thermocouples).
* Channels 150, 151... are MAX31856 thermocouple channels using chip selects CS1, CS2... on the SPI bus
* Channels 200, 201... are MAX31865 PT100 channels using chip selects CS1, CS2... on the SPI bus, see [Connecting PT100 temperature sensors](/User_manual/Connecting_hardware/Temperature_connecting_PT100).
* Channels 300, 301... are current loop channels using chip selects CS1, CS2... on the SPI bus
* Channels 400, 401... are DHTxx temperature channels. The DATA line of the DHTxx must be connected to one of pins CS1, CS2... on the SPI bus. Specify the sensor type (11 for DHT11, 21 for DHT21 or 22 for DHT22) via the T-parameter. e.g. M305 P102 X401 T22 S"DHT temperature"
* Channels 450, 451... are as 400, 401... but specify the corresponding humidity sensor of the DHTxx
* Channels 500, 501... are the thermistor inputs, but configured for PT1000 sensors rather than thermistors. Only the R, H and L parameters are significant, see example below.
* Channel 1000 is the on-chip microcontroller temperature sensor
* Channel 1001 represents the temperature warning and overheat flags on the TMC2660, TMC2224 or other smart drivers on the Duet main board. It reads 0C when there is no warning, 100C if any driver reports over-temperature warning , and 150C if any driver reports over temperature shutdown.
* Channel 1002 is as channel 1001 but for drivers on the Duex 2 or Duex 5 expansion board.

If the M305 command for a real heater does not specify a sensor channel and the heater has not been configured yet, then it defaults to using the thermistor associated with that heater.

PT1000 Example:
```
M305 P1 X501 R2200 ; heater 1 uses a PT1000 connected to thermistor channel 1 which has a 2.2K series resistor (i.e a Duet 2 Maestro)
```
Note: PT1000 sensors connected to thermistor inputs have lower resolution than PT100 sensors connected via the PT100 daughter board. The accuracy of PT1000 sensors should be very good on the Duet 2 Maestro and generally good on the Duet 2 Wifi and Duet 2 Ethernet. See the [PT1000 documentation](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000) for more details.

Virtual heaters 100, 101 and 102 are preconfigured to use temperature sensor channels 1000, 1001 and 1002 respectively. We suggest you use virtual heaters 103 upwards if you want to create additional virtual heaters.

If you send the following command:
```
M305 P101
```
you should get the response "Heater 101 uses TMC2660 temperature warnings sensor channel 1001". But as this virtual heater has no name, it doesn't show up in DWC. You can fix that by sending:
```
M305 P101 S"Drivers"
```
After that, if you go to the "Extra" tab in DWC (where is says Tools/Heaters/Extra), you will see "Drivers" as an entry.

## M307: Set or report heating process parameters

### {.tabset}

#### RRF 3.4.0beta7 and later

**Parameters**

* **Hn** Heater number (0 is usually the bed heater)
* **Rnnn** Heating rate in degC/sec at full power when the heater temperature is close to ambient (RRF 3.2 and later)
* **Dnnn** Dead time in seconds
* **Ennn** (RRF 3.4.0beta7 and later) Exponent of the cooling rate curve, default 1.35. Used in conjunction with the K parameter.
* **Knnn** or **Knnn:nnn** (RRF 3.4.0beta7 and later) Cooling rate when the heater is 100C above ambient. If one value is given then the cooling rate is calculated as K*((Th-Ta)/100)^E where Th is the heater temperature and Ta is the ambient temperature. If two values are given then the cooling rate is calculated as K[0]\*((Th-Ta)/100)^E + K[1]\*((Th-Ta)/100)\*F where F is the fan PWM in the range 0 to 1.

**Additional parameters to help control the heating process:**

* **Bn** selects Bang-bang control instead of PID if non-zero. Default at power-up is 0 for extruder heaters, 1 for the bed heater.
* **Innn** Invert PWM signal (I0 = not inverted, I1 = invert PWM/bang-bang signal for inverted temperature control [e.g. with Peltier elements])
* **Snnn** maximum PWM to be used used with this heater on a scale of 0 to 1. Default 1.0.
* **Vnnn** VIN supply voltage at which the R parameter was calibrated. This allows the PID controller to compensate for changes in supply voltage. A value of zero disables compensation for changes in VIN voltage. Supply voltage compensation is applied to hot end heaters only, not to bed or chamber heaters.

**Examples (RRF 3.4.0beta7 and later)**

```
M307 H0 ; report the process parameters for heater 0
M307 H1 R2.186 K0.17:0.11 D5.67 S1.00 V24.0 ; set the process parameters for heater 1
```

**Notes (RRF 3.4.0beta7 and later)**

* The C parameter is deprecated in RRF 3.4.0beta7 and later in favour of the K and E parameters.
* See notes on previous RRF 3.x tabs for all changes since RRF 2.x.

#### RRF 3.3 and 3.2

**Parameters**

* **Hn** Heater number (0 is usually the bed heater)
* **Rnnn** Heating rate in degC/sec at full power when the heater temperature is close to ambient (RRF 3.2 and later)
* **Cnnn** or **Caaa:bbb** dominant time Constant of the heating process in seconds. If two values are provided (supported in RRF 3.2 and 3.3), the first value is with the fan off and the second is with the fan on at full PWM.
* **Dnnn** Dead time in seconds

**Additional parameters to help control the heating process:**

* **Bn** selects Bang-bang control instead of PID if non-zero. Default at power-up is 0 for extruder heaters, 1 for the bed heater.
* **Innn** Invert PWM signal (I0 = not inverted, I1 = invert PWM/bang-bang signal for inverted temperature control [e.g. with Peltier elements]
* **Snnn** maximum PWM to be used used with this heater on a scale of 0 to 1. Default 1.0.
* **Vnnn** VIN supply voltage at which the R parameter was calibrated (RepRapFirmware 1.20 and later). This allows the PID controller to compensate for changes in supply voltage. A value of zero disables compensation for changes in VIN voltage. Supply voltage compensation is applied to hot end heaters only, not to bed or chamber heaters.

**Examples (RRF 3.2 and 3.3)**

```
M307 H0 ; report the process parameters for heater 0
M307 H1 R2.186 C202.1:155.0 D5.67 S1.00 V24.0 ; set the process parameters for heater 1
```

**Notes (RRF 3.2 and 3.3)**

* The A parameter is deprecated in RRF 3.2 and later in favour of the R parameter.
* See notes on previous RRF 3.x tabs for all changes since RRF 2.x.

#### RRF 3.1 and 3.0

**Parameters**

* **Hn** Heater number (0 is usually the bed heater)
* **Annn** gAin, expressed as ultimate temperature rise obtained in degC divided by the PWM fraction. For example, if G=180 then at 50% PWM the ultimate temperature rise would be 90C.
* **Cnnn** dominant time Constant of the heating process in seconds.
* **Dnnn** Dead time in seconds

**Additional parameters to help control the heating process:**

* **Bn** selects Bang-bang control instead of PID if non-zero. Default at power-up is 0 for extruder heaters, 1 for the bed heater.
* **Innn** Invert PWM signal (I0 = not inverted, I1 = invert PWM/bang-bang signal for inverted temperature control [e.g. with Peltier elements])
* **Snnn** maximum PWM to be used used with this heater on a scale of 0 to 1. Default 1.0.
* **Vnnn** VIN supply voltage at which the A parameter was calibrated (RepRapFirmware 1.20 and later). This allows the PID controller to compensate for changes in supply voltage. A value of zero disables compensation for changes in VIN voltage. Supply voltage compensation is applied to hot end heaters only, not to bed or chamber heaters.

**Examples (RRF 3.1 and 3.0)**

```
M307 H0 ; report the process parameters for heater 0
M307 H1 A346.2 C140 D5.3 B0 S0.8 V23.8; set process parameters for heater 1, use PID, and limit heater 1 PWM to 80%
```

**Notes**

* The F parameter is no longer supported. Use M950 to set the frequency in the M950 command that you use to define the heater.
* The I2 parameter is no longer supported, and I3 does the same as I1. You can use M950 to invert the output.
* You can no longer disable a heater using M307 A-1 C-1 D-1. To use the pin for something else, don't create a heater on that pin. 

Example:
```
;RRF 2.x code
M307 H0 F100  ; change heater 0 PWM frequency to 100Hz
M307 H2 A-1 C-1 D-1 ; disable heater 2 so we can use its pin to drive a fan

;RRF 3.x code
M950 H0 C"bed_heat" Q100 T0 ; heater 0 uses the bed_heat pin, sensor 0, PWM frequency 100Hz
; No need to disable heater 2 because we didn't define it in the first place
```

#### RRF 2.x and 1.20 and later 1.x

**Parameters**

* **Hn** Heater number (0 is usually the bed heater)
* **Annn** gAin, expressed as ultimate temperature rise obtained in degC divided by the PWM fraction. For example, if G=180 then at 50% PWM the ultimate temperature rise would be 90C.
* **Cnnn** dominant time Constant of the heating process in seconds.
* **Dnnn** Dead time in seconds

**Additional parameters to help control the heating process:**

* **Bn** selects Bang-bang control instead of PID if non-zero. Default at power-up is 0 for extruder heaters, 1 for the bed heater.
* **Innn** Invert PWM signal (I0 = not inverted, I1 = invert PWM/bang-bang signal for inverted temperature control [e.g. with Peltier elements], I2 = full PWM signal inversion, I3 = both I1 and I2)
* **Snnn** maximum PWM to be used used with this heater on a scale of 0 to 1. Default 1.0.
* **Vnnn** VIN supply voltage at which the A parameter was calibrated (RepRapFirmware 1.20 and later). This allows the PID controller to compensate for changes in supply voltage. A value of zero disables compensation for changes in VIN voltage. Supply voltage compensation is applied to hot end heaters only, not to bed or chamber heaters.
* **Fnnn** PWM frequency to use

**Notes (RRF 2.x and 1.20 and later 1.x)**

* RepRapFirmware 1.16 and later allow the PID controller for a heater to be disabled by setting the A, C and D parameters to -1. This frees up the corresponding heater control pin for use as a general purpose I/O pin to use with the M42 or M280 command. Example: `M307 H2 A-1 C-1 D-1`.

**Notes**

Each heater and its corresponding load may be approximated as a first order process with dead time, which is characterised by the gain, time constant and dead time parameters. The model can used to calculate optimum PID parameters (including using different values for the heating or cooling phase and the steady state phase), to better detect heater faults, and to calculate feed-forward terms to better respond to changes in the load. Normally these model parameters are found by auto tuning - see [M303](/User_manual/Reference/Gcodes/M303) and [Tuning heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning).

For those platforms that provide power voltage monitoring, the calibration voltage setting allows the heating controller to adjust the power automatically in response to changes in the power supply voltage. For example, if a bed or chamber heater is turned on or off, this may cause the power supply voltage to change a little, which if not corrected for would change the extruder heater power.

## M308: Set or report sensor parameters

M308 is supported in RepRapFirmware 3. If running RRF2.x or earlier, use M305.

**Parameters**

* **Sn** Sensor number
* **P"pin_name"** The name of the control board pin that this sensor uses. For thermistors it is the thermistor input pin name, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names). For sensors connected to the SPI bus it is the name of the output pin used as the chip select.
* **Y"sensor_type"** The sensor and interface type, one of: "thermistor", "pt1000", "rtd-max31865", "thermocouple-max31855", "thermocouple-max31856", "linear-analog", "dht21", "dht22", "dhthumidity", "current-loop-pyro", "drivers". All boards except the Duet 3 Mini also support "mcu-temp". Duet WiFi/Ethernet with an attached DueX2 or DueX5 also support "drivers-duex". Firmware 3.2 and earlier also supports "dht11" but this support is likely to be removed in future firmware versions.

* **A"name"** Sensor name (optional), displayed in the web interface

**Additional parameters for thermistors**

* **Tnnn** (for thermistor sensors) Thermistor resistance at 25°C
* **Bnnn** Beta value, or the reciprocal of the Steinhart-Hart thermistor model B coefficient
* **Cnnn** Steinhart-Hart C coefficient, default 0
* **Rnnn** Series resistor value. Leave blank to use [the default for your board](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000#the-m305m308-r-parameter).
* **Lnnn** ADC low offset correction, default 0 (ignored if the hardware supports automatic ADC gain and offset calibration)
* **Hnnn** ADC high offset correction, default 0 (ignored if the hardware supports automatic ADC gain and offset calibration)

**Additional parameters for PT1000 sensors**

* **Rnnn** Series resistor value. Leave blank to use [the default for your board](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000#the-m305m308-r-parameter).
* **Lnnn** ADC low offset correction, default 0 (ignored if the hardware supports automatic ADC gain and offset calibration)
* **Hnnn** ADC high offset correction, default 0 (ignored if the hardware supports automatic ADC gain and offset calibration)

**Additional parameters for MAX31856-based thermocouple sensors**

* **T"c"** The thermistor type letter, default K
* **Fnn** (where nn is 50 or 60) The local mains frequency. Readings will be timed to optimise rejection of interference at this frequency.

**Additional parameters for MAX31865-based PT100 sensors**

* **Rnnn** Series resistor value. Leave blank to use [the default for your board](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000#the-m305m308-r-parameter).
* **Fnn** (where nn is 50 or 60) The local mains frequency. Readings will be timed to optimise rejection of interference at this frequency.

**Additional parameters for linear analog sensors**

* **Fn** F0 = unfiltered (fast response), F1 = filtered (slower response, but noise reduced and ADC oversampling used to increase resolution)
* **Bnnn** The temperature or other value when the ADC output is zero
* **Cnnn** The temperature or other value when the ADC output is full scale

**Note on the drivers temperature sensor**

The Trinamic drivers used on Duets do not report temperature, rather they report one of: temperature OK, temperature overheat warning, and temperature overheat error. RRF translates these three states into readings of 0C, 100C and 130C.

**P"nnn" parameter for "dhthumidty"**

Reimplementing DHT support in RepRapFirmware 3 has led to a new concept of additional outputs a sensor can have. With the exception of DHT sensors all other sensors have just a primary output (output number 0). All DHT variants though have - besides their primary output for temperature - a secondary output that delivers the humidity values. "dhthumidity" will be attached to an existing DHT sensor's secondary output by using its full sensor number (including the leading S) and the output's index separated by a dot.

**Examples**

```
M308 S10 P"0.spi.cs1" Y"dht22" A"Filament Temp"      ; define DHT22 temperature sensor
M308 S11 P"S10.1" Y"dhthumidity" A"Filament Hum[%]"  ; Attach DHT22 humidity sensor to secondary output of temperature sensor
```

**Notes**

This code replaces M305 in RepRapFirmware 3. In earlier versions of RepRapFirmware, sensors only existed in combination with heaters, which necessitated the concept of a "virtual heater" to represent a sensor with no associated heater (e.g. MCU temperature sensor). RepRapFirmware 3 allows sensors to be defined independently of heaters, up to a max of 32 sensors. The association between heaters and sensors is defined using M950.

M308 can be used in the following ways:
* **M308 Snn Y"type" [other parameters]**: delete sensor nn if it exists, create a new one with default settings, and configure it using the other parameters. At least the pin name must also be provided, unless the sensor doesn't use a pin (e.g. MCU temperature sensor).
* **M308 Snn**: report the settings of sensor nn, this will also report the last error on that sensor if applicable
* **M308 A"name"*: report the settings of the first sensor named "name"
* **M308 Snn [any other parameters except Y]**: amend the settings of sensor nn

Sensor type names obey the same rules as [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names), i.e. case is not significant, neither are hyphen and underscore characters.

When converting from older versions of RRF to RRF3 you must replace each M305 command by a similar M308 command, which must be earlier in config.g than any M950 command that uses it. You must also use M950 to define each heater that you use, because there are no default heaters.

Example - old code:

```
M305 P0 T100000 B3950 ; bed heater uses a B3950 thermistor
M305 P1 T100000 B4725 C7.06e-8 ; E0 heater uses E3D thermistor
```

New code:

```
M308 S0 P"bed_temp" Y"thermistor" T100000 B3950 ; define bed temperature sensor
M308 S1 P"e0temp" Y"thermistor" T100000 B4725 C7.06e-8 ; define E0 temperature sensor
M950 H0 C"bed_heat" T0 ; heater 0 uses the bed_heat pin, sensor 0
M950 H1 C"e0heat" T1 ; heater 1 uses the e0heat pin and sensor 1
```

## M309: Set or report heater feedforward

Supported in RepRapFirmware v3.4 and later

**Usage**

* M309 Pn Saaa:bbb

**Parameters**

* **Pn** Tool number
* **Saaa:bbb:ccc...** Feedforward coefficients. The number of coefficients provided must equal the number of heaters configured for the tool when it was created (see M563).

**Notes**

If the P parameter is not provided, the current tool is assumed. If the S parameter is not provided, the existing coefficients are reported.

The units of S are PWM fraction (on a scale of 0 to 1) per mm/sec of filament forward movement.

## M350: Set microstepping mode

**Usage**

* M350 Xnn Ynn Znn Enn Inn

**Parameters**

* *Not all parameters need to be used, but at least ***one*** should be used. As with other commands, RepRapFirmware reports the current settings if no parameters are used*.
* **Xnn** Set stepping mode for the X axis
* **Ynn** Set stepping mode for the Y axis
* **Znn** Set stepping mode for the Z axis
* **Enn** Set stepping mode for Extruder 0 (use Enn:nn:nn etc. for multiple extruders)
* **Inn** Enable (nn=1) or disable (nn=0) microstep interpolation mode for the specified drivers, if they support it. All Duet 3 boards, and Duet 2 Maestro supports interpolation at all microstep settings. Duet 2 WiFi/Ethernet support interpolation (to x256 microstepping) only when configured for x16 microstepping.

**Modes (nn)**

* 1 = full step
* 2 = half step
* 4 = quarter step
* 8 = 1/8 step
* 16 = 1/16 step
* 32 = 1/32 step
* 64 = 1/64 step
* 128 = 1/128 step
* 256 = 1/256 step

**Order dependency**

This command must be later in config.g than any M584 command.

**Examples**

```
M350 Z1 ;set the Z-axis' driver to use full steps
M350 E4:4:4 ;set extruders 0-2 to use quarter steps)
```

When M350 is processed, the steps/mm will be adjusted automatically to allow for any changes in microstepping. Therefore you can either:

a) Set Steps/mm correctly for the default 1/16 microstepping, then set the microstepping to the desired amount using M350:

```
M92 X80 Y80 Z400 ; set axis steps/mm
M92 E420:430 ; set extruder 0 and 1 steps/mm
M350 X128 Y128 Z128 E128:128 ; set microstepping
```

or

b) Set the microstepping using M350 and then set the correct steps/mm for that microstepping amount:

```
M350 X128 Y128 Z128 E128:128 ; set microstepping
M92 X640 Y640 Z3200 ; set axis steps/mm @128 microstepping
M92 E3360:3440 ; set extruder 0 and 1 steps/mm
```

Assuming that in the first example the microstepping was initially at the default x16, both the above examples result in the same steps/mm settings.

**Notes**

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour.

Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M350 Z16, not M350 Z16,16

Microstep interpolation at all microstep settings is supported on all Duet 3 boards with onboard drivers (TMC5160, TMC2160 or TMC2209), and on Duet 2 Maestro (TMC2224 drivers). The TMC2660 drivers used on the Duet 2 WiFi and Duet 2 Ethernet support microstep interpolation, but only when microstepping is set to 16. In other configurations, specifying I1 has no effect.

## M374: Save height map

**Parameters**

* **P"filename"** Name of the file to save to

**Usage**

* M374
* M374 P"MyAlternateHeightMap.csv"

**Notes**

This saves the grid parameters and height map into the specified file, or the default file **heightmap.csv** if no filename was specified. To load the height map automatically at startup, use command G29 S1 or M375 in the config.g file.

The G29 S0 command automatically saves the height map to file, therefore you do not need to use M374 after G29 S0.

## M375: Load height map

Loads the grid matrix file

**Parameters**

* **P"filename"**

**Examples**

```
M375
M375 P"MyAlternateHeightMap.csv"
```

**Notes**

Without parameters loads default grid (**sys/heightmap.csv**), and with specified filename attempts to load the specified grid. If not available will not modify the current grid.

In RepRapFirmware this command is equivalent to the G29 S1 command.

## M376: Set bed compensation taper

**Parameters**

* **Hnnn** Height (mm) over which to taper off the bed compensation

**Examples**

```
M376 H10
```

**Notes**

This command specifies that bed compensation should be tapered linearly over the specified height, so that full bed compensation is applied at Z=0 and no bed compensation is applied when Z is at or above that height. If H is zero or negative then no tapering is applied, so compensation is performed throughout the entire print.

RepRapFirmware does not adjust the extrusion factor to account for the layer height varying when tapered bed compensation is used. Therefore it is recommended that the taper height be set to at least 20x the maximum error in the height map, so that the maximum amount of the resulting over- or under- extrusion is limited to 5%.

## M400: Wait for current moves to finish

**Examples**

```
M400
```

**Notes**

Finishes all current moves and and thus clears the buffer. That's identical to G4 P0.

## M401: Deploy z-probe

**Parameters**

* **P** Probe number (RRF 3.01RC4 and later)

**Examples**

```
M401
M401 P1
```

This runs macro file **sys/deployprobe#.g** (where # is the probe number) if it exists, otherwise **sys/deployprobe.g** if it exists.

## M402: Retract z-probe

**Parameters**

* **P** Probe number (RRF 3.01RC4 and later)

**Examples**

```
M402
M402 P1
```

**Notes**

This runs macro file **sys/retractprobe#.g** (where # is the probe number) if it exists, otherwise **sys/retractprobe.g** if it exists.

## M404: Filament width and nozzle diameter

**Parameters**

* **Nnnn** Filament width (in mm)
* **Dnnn** Nozzle diameter (in mm)

**Examples**

```
M404 N1.75
M404 N3.0 D1.0
```

**Notes**

Enter the nominal filament width (3mm, 1.75mm) or will display nominal filament width without parameters.

The 'D' parameter is used to properly detect the first layer height when files are parsed or a new print is being started.

The values of this command are currently only used for the print monitor.
