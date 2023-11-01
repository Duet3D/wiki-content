---
title: GCode dictionary
description: 
published: true
date: 2023-11-01T16:11:15.847Z
tags: 
editor: markdown
dateCreated: 2021-04-27T14:09:24.591Z
---

# Introduction

GCodes are a widely used machine control language. They are human readable and editable. This page describes the RepRapFirmware supported GCodes. RepRapFirmware follows the philosophy of "GCode everywhere", in essence the users or external program's interaction with the firmware should be through GCodes. There are GCodes for all supported control and configuration inputs along with status and debugging information.

RepRapFirmware GCodes were originally based on the information from the [RepRap wiki GCode page](http://reprap.org/wiki/G-code){target=_blank}. There are some GCodes listed on that page that are not implemented in RepRapFirmware. More details can be found on the [GCodes not implemented](/User_manual/Reference/Gcodes_not_implemented){target=_blank} page.

# GCode and RepRapFirmware

A typical piece of GCode sent to a machine running RepRapFirmware might look like this (The meaning of these codes (and more) is explained below on this page.)
<br>
<pre class="cblock">
G10 P0 S195 R175
T0
G1 X100 Y100 Z0.3 F3000
G1 X100.4 Y99.3 E0.23 F600
...many 1000s more lines...
</pre>

## GCode Everywhere

A design philosophy of RepRapFirmware is "GCode everywhere" what this means is explained in this sub section

The GCode can originate from a number of sources:

* Sent to over USB (for example from [Pronterface](http://www.pronterface.com/){target=_blank})
* Sent by the [Duet Web Control (DWC)](/User_manual/Reference/Duet_Web_Control_Manual){target=_blank} Interface
* Sent by an external controller such as the [PanelDue](/Duet3D_hardware/Accessories/PanelDue){target=_blank}

In all cases the GCode could

* be entered by user one line at time, for example during configuration or testing
* be sent by the User Interface (Pronterface, Web Interface or PanelDue) in response to the user pressing buttons
* originate from [Macros](/User_manual/Tuning/Macros){target=_blank} that are triggered on startup, on certain events (such as error conditions), or called by the user or UI.
* be from a GCode file which are normally stored on the on-board or external SD card.

A key difference from other 3d printer firmwares is not employing a separate command set (other than GCodes) to configure the printer. To that end RepRapFirmware has a large collection of configuration GCodes that allow the behaviour of the machine to be controlled. For some examples of when these GCodes are employed have a look at these wiki pages:

* [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian){target=_blank}
* [Configuring RepRapFirmware for a Linear Delta printer](/User_manual/Machine_configuration/Configuration_linear_delta){target=_blank}
* [Configuring RepRapFirmware for a CoreXY printer](/User_manual/Machine_configuration/Configuration_coreXY){target=_blank}
* [Configuring RepRapFirmware for an IDEX printer](/User_manual/Machine_configuration/Configuration_IDEX){target=_blank}
* [Tuning the heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning){target=_blank}
* [Setting up automatic probing of the print bed](/User_manual/Connecting_hardware/Z_probe_auto_probing){target=_blank}
* [Connecting hobby servos and DC motors](/User_manual/Connecting_hardware/Motors_servos){target=_blank}
* [Controlling unused IO pins](/User_manual/Connecting_hardware/IO_GPIO){target=_blank}
<!--* [Configuring RepRapFirmware for a SCARA printer **UPDATE LINK**](){target=_blank}
* [Configuring RepRapFirmware for a Polar printer **UPDATE LINK**](){target=_blank}
* [Configuring RepRapFirmware for a Hangprinter printer **UPDATE LINK**](){target=_blank}-->

The advantage of "GCode everywhere" is the same commands can be send from any of the GCode sources, and originate from the user, a UI, macro or file and it will generate the same response from the firmware. This greatly improves the ease and power of firmware configuration and operation.

# GCode Structure

This section explains the elements that make up a GCode command.

## Command Order

The general rule on command order in config.g is: don't try to change the parameters of anything that you haven't already created and doesn't exist by default.

For example:

* Don't refer to any axis letter or extruder number that you haven't created using M584 (except that XYZ already exist by default)
* Don't refer to any temperature sensor in M950 or M106 before you create it using M308
* Don't refer to a heater in M307, M140, M141, M143 or M563 before you create it using M950
* Don't refer to a fan in M106 before you create it using M950
* Don't refer to a tool in G10, M567 or M568 before you create it using M563

## Comments

GCode comments begin at a semicolon, and end at the end of the line:
<br>
<pre class="cblock">
T0 ; This is a comment
G92 E0
;So is this
G28
</pre>

In CNC mode only ([M453](/User_manual/Reference/Gcodes/M453){target=_blank}), comments can be enclosed in a **single** pair of parentheses, e.g. `(comment)`. Comments cannot include double or nested parentheses, e.g. `(comment (a bit more comment))`, and they must start and end on the same line. This complies with NIST Gcode interpreter guidelines.
<br>
<pre class="cblock">
G28 (here come the axes to be homed) X Y
</pre>

Comments and white space will be ignored by RepRapFirmware when executing the GCode.

## Fields

A RepRap GCode is a list of fields that are separated by white spaces or line breaks. A field can be interpreted as a command, parameter, or for any other special purpose. It consists of one letter directly followed by a number, or can be only a stand-alone letter (Flag). The letter gives information about the meaning of the field (see the list below in this section). Numbers can be *integers* (128) or *fractional* numbers (12.42), depending on context. For example, an X coordinate can take integers (**X175**) or fractionals (**X17.62**), but selecting extruder number 2.76 would make no sense. In this description, the numbers in the fields are represented by **nnn** as a placeholder.

In RepRapFirmware 3.01 and later, instead of a number you may use an expression enclosed in braces, for example {2+2}. See [GCode Meta Commands](/User_manual/Reference/Gcode_meta_commands){target=_blank} for details of the supported expression types.

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

### Case sensitivity

The original NIST GCode standard requires GCode interpreters to be case-insensitive, except for characters in comments. However, not all 3D printer firmwares conform to this and some recognise uppercase command letters and parameters only.

RepRapFirmware version 1.19 and later is case-insensitive, except for characters within quoted strings. RepRapFirmware version 1.18 and earlier accept only uppercase letters for command and parameter letters.

### Quoted strings

In RepRapFirmware, quoted strings are permitted anywhere a string parameter is expected. This allows file names, WiFi passwords etc. to contain spaces, semicolons and other characters that would otherwise not be permitted. Double-quote characters are used to delimit the string, and any double-quote character within the string must be repeated.

Unfortunately, many GCode sender programs convert all characters to uppercase and don't provide any means to disable this feature. Therefore, within a quoted-string, the single-quote character is used as a flag to force the following character to lowercase. If you want to include a single quote character in the string, use two single quote characters to represent one single quote character.

Example: to add SSID MYROUTER with password ABCxyz;" 123 to the WiFi network list, use command:
<br>
<pre class="cblock">
M587 S"MYROUTER" P"ABCxyz;"" 123"
</pre>

or if you can't send lowercase characters:
<br>
<pre class="cblock">
M587 S"MYROUTER" P"ABC'X'Y'Z;"" 123"
</pre>

### Checking

This is an optional feature that is seldom used as GCode files are normally printed from the on-board SD card.

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

You can leave both of these out - RepRapFirmware will still work, but it won't do checking. You have to have both or neither though. If only one appears, it produces an error. See [this forum thread](https://forum.duet3d.com/topic/15134/){target=_blank} for an example of usage, in this case sending GCode to the PanelDue port without disabling checksums.

The checksum "cs" for a GCode string "cmd" (including its line number) is computed by xor-ing the bytes in the string up to and not including the * character as follows:
<br>
<pre class="cblock">
int cs = 0;
for(i = 0; cmd[i] != '*' && cmd[i] != NULL; i++)
 cs = cs ^ cmd[i];
cs &= 0xff; // Defensive programming...
</pre>

and the value is appended as a decimal integer to the command after the * character.

Checksums provide poor protection against data corruption. RRF 3.4 and later allow a 16-bit CRC to be used instead.

## Conditional execution, loops, and other command words

In RepRapFirmware 3.01 and later, if the line begins with a recognised keyword (optionally preceded by N and a line number, and/or space or tab characters) then that whole line of GCode is interpreted as a meta-command. Recognised keywords are:

**abort echo elif else global if set var while**

See [GCode Meta Commands](/User_manual/Reference/Gcode_meta_commands){target=_blank} for details of these commands.

A line that does not start with one of these keywords must start with command letter G, M or T or be empty apart from white space and comments. Exception: when in CNC or Laser mode, if a line does not start with a G, M or T command but nevertheless has other fields, and the previous line that included a command was a G0, G1, G2 or G3 command, then the previous command will be repeated with values from the new fields. This is to support GCode generated for CNC machines.

## Multiple commands on a single line

RepRapFirmware allows multiple G- and M-commands to be included in a single line. Each occurrence of G or M on the line that is preceded by a space or tab character and is not inside a quoted string or a meta command starts a new command. In RRF 3.2 and later, the space or tab character is not required.

**Important**: a command that invokes a macro file (such as G28, G29, G32 and M98) must be the last command in that line of GCode, because any following commands on the same line will not be executed. A T (tool change) command must be on a line by itself.

## Command queueing

RepRapFirmware stores regular G0, G1, G2 and G3 movement commands in a 'move queue' internally for execution, equivalent to a look-ahead buffer. These commands are acknowledged when they are placed in the queue. This means that there is no appreciable delay before a command is acknowledged, unless the move queue is full; and that sequences of line segments can be plotted without coming to a stop between one and the next. 

Some non-movement commands are also queued when executed from a job file or a macro, in a 'deferred command queue'. This operates in parallel to the 'move queue', and together the two queues operate as a single logical queue, and generally can be considered as one queue. M3, M4, M5, M42, M104, M106, M107, M117, M140, M141, M144, M280, M300 and M568 commands are all queued.

As soon as one of these commands is received it is acknowledged and stored locally in the queue. If the queue is full, then the acknowledgement is delayed until space for storage in the queue is available. PC host programs rely on this for flow control when the controller electronics does not support device level flow control.

**Notes**

* Commands are queued when executed from a job file or a macro.
* All moves are always queued, except for homing/probing moves which are special.
* G4/M400 finishes all moves in the current queue and thus clears the queue. RRF 3.5 on some boards supports multiple queues, which run independently.
* M595 can be used to increase the move queue length.
* When a non-queued command is received, it is stored but not acknowledged to the host until the queue is exhausted and then the command has been executed.
* If a command that is usually queued contains a parameter that is an OM expression enclosed in `{ }` then the command is not queued because the value of the OM expression is liable to change, and there isn't a suitable context to evaluate it in if it were to be queued.
* Meta commands such as echo are never queued.
* When M555 P6 is used to select nanoDLP compatibility mode, no commands are queued.

## Filenames and Paths

* '0:/' is root of the on board SD card in stand alone mode; the equivalent folder in SBC mode is '/opt/dsf/sd/'.
* If there is a second SD card slot in the machine then its root is '1:/'.
* Long file names (e.g. longer than 8.3 format) are supported, file names with spaces are supported.
* Full paths, including all directories and subdirectories are limited to 120 characters. e.g.:
  <pre class="cblock">
  0:/gcodes/0123456789/012.gcode
  </pre>
  counts as 30 characters
* See [SD card](/User_manual/RepRapFirmware/SD_card) for a full explanation of the SD card structure.

## Live editing

As a general rule, any G or M command can be sent at any time. This means all settings can be changed 'on the fly', i.e. while printing. This does not take into account logical considerations, or individual pre-requisites for individual commands. For example, sending new values for M566 'jerk', M201 'acceleration', or M203 'max speed' while printing is a great way to tune these values. However, while it is theoretically possible to change M563 'define a tool' *while* printing, it's probably *not a good idea*.

## GCodes not implemented

For a list of GCodes that may be found in other firmwares/CNC control software which are not implemented in RepRapFirmware, see [GCodes not implemented](https://docs.duet3d.com/User_manual/Reference/Gcodes_not_implemented){target=_blank}.

## Custom GCodes

In RepRapFirmware 2.03 and later, you can create custom GCodes, so long as the GCode is not already implemented. If you try to execute a G- or M-command that RRF does not implement, it will execute a system macro of that name if it exists. For example, if you send G40 then it will execute /sys/G40.g if it exists; and if you send M5000 then it will execute /sys/M5000.g if it exists.

In RRF 3.4 and later this extends to G- and M-commands with fractional numbers, for example if you send M55.6 then RRF will look for file "0:/sys/M55.6.g".

## Command Length

The maximum length of a gcode command including the command itself and all parameters is 256 characters. 

# G-commands

<!-- Example entry
## Gx: Short description

*Supported/deprecated from firmware version X.XX*

Short description

### Parameters

* **P** First parameter description
* **X** Second parameter description
* **Y** Third parameter description

### Usage

Gx Pnnn Xnnn Ynnn Znnn etc

### Order dependency

Blah

### Examples
<br>
<pre class="cblock">
Gx P1 X2 Y3 Z4 ; description of effect
</pre>

### Descripton

Blah blah

### Notes

* Blah
* Blah

-->


## G0: Rapid move

Same as G1 except when in Laser and CNC mode, where moves are executed at the maximum feedrate available. See 'G1: Controlled linear' move for usage.

## G1: Controlled linear move

### Usage

* RRF2.02 and later, RRF3
  * G0 Xnnn Ynnn Znnn Ennn Fnnn Snnn Hnnn
  * G1 Xnnn Ynnn Znnn Ennn Fnnn Snnn Hnnn

* RRF2.01 and earlier
  * G0 Xnnn Ynnn Znnn Ennn Fnnn Snnn
  * G1 Xnnn Ynnn Znnn Ennn Fnnn Snnn

### Parameters

* *Not all parameters need to be used, but at least one of XYZEF must be used*
* **Xnnn** The position to move to on the X axis
* **Ynnn** The position to move to on the Y axis
* **Znnn** The position to move to on the Z axis
* **Ennn** The amount to extrude between the starting point and ending point ^1^
* **Fnnn** The feed rate per minute of the move between the starting point and ending point (if supplied)
* **Hnnn** Move type (RRF2.02 and later, RRF3)
* **Snnn** In RRF3, this parameter is used to set laser power, when switched into Laser mode (see [M452](/User_manual/Reference/Gcodes/M452){target=_blank}); its use for defining move type is deprecated, use 'H' parameter instead. In RRF2.02 and later, when switched into Laser mode (see [M452](/User_manual/Reference/Gcodes/M452){target=_blank}), this parameter sets the laser power. When not switched into Laser mode, and always in firmware 2.01 and earlier, it defines the move type (see the description of the H parameter).
* **Rn** Return to the coordinates stored in restore point #n (see [G60](/User_manual/Reference/Gcodes/G60){target=_blank}). Any X, Y, Z and other axis parameters in the command are used as offsets from the stored position. Axes not mentioned are not moved, so use offset 0 for axes you want to restore to the stored value. For example, G1 R0 X0 Y0 Z2 will move to 2mm above the position stored in restore point 0.
* **Pnnnn** (supported only in some builds of RepRapFirmware) IOBITS parameter. Defines the states of output pins while this command is executed. See the M670 command.

^1^Where a tool has more than one extruder drive then Ennn:nnn:nnn etc is supported to allow for the individual movement of each to be controlled directly. This overrides the extruder mix ratio set with M567

**Very important!** If you use M452 to put your machine into Laser mode, when upgrading firmware from 2.01 or earlier to 2.02 or later you must replace all S parameters in G0/G1 commands in homing files etc. by H parameters. This is because S is now used to control laser power, for compatibility with programs that generate GCode files for laser cutters.


### Examples
<br>
<pre class="cblock">
G0 X12               ; (move to 12mm on the X axis)
G0 F1500             ; (Set the feedrate to 1500mm/minute)
G1 X90.6 Y13.8 E22.4 ; (Move to 90.6mm on the X axis and 13.8mm on the Y axis while extruding 22.4mm of material)
G1 E10:10:5:0:0 F300 ; with a tool that has 5 extruder drives, extrude 10mm on drive 0, 10mm on drive 1, 5mm on drive 2 and 0mm on drive 3 and 4.
</pre>

### Notes

RepRapFirmware treats G0 and G1 in the same way **except** as follows:
* On SCARA and similar architectures that normally require linear motion to be approximated by short segments, a single continuous non-segmented movement will be used if this can be done without the print head dropping below the current Z height.
* In Laser and CNC mode, G0 moves are executed at the maximum feed rate available, to comply with the NIST GCode standard, This feed rate is set by the M203 command.
* RRF maintains a flag for feed rate (F parameter). For all G1/2/3 moves (and G0 moves in FDM mode) the following is true:
  * Each input channel (SD card, USB, http, telnet etc) has its own flag for feed rate.
  * At the end of running config.g at startup, the flag state is copied to all input channels. If no feed rate is specified in config.g, the default of 3000mm/min (50mm/s) is used.
  * The feed rate stored by the flag is used if the next G0/1/2/3 command doesn't include an F parameter.
  * The flag state is saved when a macro starts and is restored when a macro ends.

#### G0/G1 H and S parameter

The meaning of the H parameter is as follows:

* **H0** no special action (default)
* **H1** terminate the move when the endstop switch is triggered and set the axis position to the axis limit defined by M208. On delta printers, H1 also selects individual motor mode as for H2. Normally used with relative motor coordinates (see G91).
* **H2** Individual motor mode. X refers to the X motor, Y refers to the Y motor, and so on. Normally used with relative motor coordinates (see G91).
* **H3** terminate the move when the endstop switch is triggered and set the axis limit to the current position, overriding the value that was set by M208.
* **H4** terminate the move when the endstop switch is triggered and update the current position (supported in RRF 3.2-b4 or newer)

The meaning of the S parameter has changed over successive versions of RepRapFirmware. It currently sets the laser power when M452 Laser mode is set, but was also used for homing behaviour. See below.

#### {.tabset}

##### RRF 3

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

##### RRF 2.02 to 2.05.1

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

##### RRF 2.01 and earlier

In **RRF 2.01 and earlier**, S parameter controls the movement type. There is no H parameter or M452 Laser Mode.

| RRF_2.01 and earlier, G0/G1 S parameter ||
|:------------|----------|
| **Parameter** | **Meaning** |
| G1 Xnnn Ynnn Znnn S0 | Ignore endstops while moving. |
| G1 Xnnn Ynnn Znnn S1 | Sense endstops while moving. On Delta (only), axis letters refer to individual towers. |
| G1 Xnnn Ynnn Znnn S2 | Ignore endstops while moving. Also ignore if axis has not been homed. On Delta and CoreXY, axis letters refer to individual motors. |
| G1 Xnnn Ynnn Znnn S3 | Sense endstops while measuring axis length, and set the appropriate M208 limit to the measured position at which the endstop switch triggers. |

#### Feedrate
<br>
<pre class="cblock">
G1 F1500
G1 X50 Y25.3 E22.4
</pre>

In the above example, we set the feedrate to 1500mm/minute on line 1, then move to 50mm on the X axis and 25.3mm on the Y axis while extruding 22.4mm of filament between the two points.
<br>
<pre class="cblock">
G1 F1500
G1 X50 Y25.3 E22.4 F3000
</pre>

However, in the above example, we set a feedrate of 1500mm/minute on line 1, then do the move described above accelerating to a feedrate of 3000 mm/minute as it does so. The extrusion will accelerate along with the X and Y movement, so everything stays synchronized.

Feedrate is treated as simply another variable (like X, Y, Z, and E) to be linearly interpolated. This gives complete control over the acceleration and deceleration of the printer head in such a way that ensures that everything moves smoothly together, and the right volume of material is extruded at all points. The feedrate specified may not be reached due to a lower feedrate limit being configured, or the move being too short for the axis to accelerate and decelerate in time.

**For CNC users especially: RRF has a default minimum movement speed of 0.5mm/sec.** In firmware 2.03 and later this can be changed using the I ('i') parameter of the M203 command.

#### Maximum Length of Moves

The firmware keeps track of the exact number of microsteps sent to each movement axis using a 32-bit signed integer microstep counter, this limits the maximum absolute move and axis length to (2^31 - 1) microsteps. This does not apply to extruder drives. The firmware multiplies the requested axis position (after adding any offsets) by the steps/mm to get the required microstep position and the microstep counter accumulates across multiple moves, as the axis position increases it increments, as it decreases it decrements. The maximum size of the microstep counter is only an issue for situations where an axis needs to accommodate one or more moves or an overall axis length that would overflow the counter (i.e. an extremely long axis, or an extremely high resolution axis). In typical uses this is not a concern, for example on a standard linear axis using 160 microsteps/mm an axis of over 13km is supported. In cases where the microstep counter is not sufficient (e.g. a very high resolution rotary axis moving for a very long time) G92 can be used to set the origin to a new point on the axis and thus reset the counter.

In a similar manner, if the requested axis position gets very large then accuracy will suffer, because it is held and calculated as a 32-bit float.

## G2: Controlled Arc Move

Clockwise arc move. Supported by RRF_1.18 and later.
See 'G3: Controlled Arc Move' for usage.

## G3: Controlled Arc Move

Counter-clockwise arc move. Supported by RRF_1.18 and later.

### Usage

* G2 Xnnn Ynnn Znnn Innn Jnnn Ennn Fnnn *(Clockwise Arc)*
* G3 Xnnn Ynnn Znnn Innn Jnnn Ennn Fnnn *(Counter-Clockwise Arc)*

### Parameters

* **Xnnn** The position to move to on the X axis.
* **Ynnn** The position to move to on the Y axis.
* **Znnn** The position to move to on the Z axis.
* **Innn** The X coordinate of the arc centre **relative to the current X coordinate** (optional, ignored if R parameter is present).
* **Jnnn** The Y coordinate of the arc centre **relative to the current Y coordinate** (optional, ignored if R parameter is present).
* **Knnn** The Z coordinate of the arc centre **relative to the current Z coordinate** (optional, ignored if R parameter is present). (RRF v3.3 and later)
* **Ennn** The amount to extrude between the starting point and ending point.^1^
* **Fnnn** The feedrate per minute of the move between the starting point and ending point (optional, defaults to the current feed rate).
* **Rnnn** The radius of the arc (optional, RRF2.03 and later)

Either the R parameter must be provided, or at least one of I and J must be provided. To draw a complete circle, define the position of the centre using I and/or J and make X and Y the same as the current X and Y coordinates.

^1^Where a tool has more than one extruder drive then Ennn:nnn:nnn etc is supported to allow for the individual movement of each to be controlled directly. This overrides the extruder mix ratio set with M567

### Examples
<br>
<pre class="cblock">
G2 X90.6 Y13.8 I5 J10 E22.4 ; (Move in a Clockwise arc from the current point to point (X=90.6,Y=13.8), with a center point at (X=current_X+5, Y=current_Y+10), extruding 22.4mm of material between starting and stopping)
G3 X90.6 Y13.8 I5 J10 E22.4 ; (Move in a Counter-Clockwise arc from the current point to point (X=90.6,Y=13.8), with a center point at (X=current_X+5, Y=current_Y+10), extruding 22.4mm of material between starting and stopping)
G2 X100 Y50 R200            ; (draw a clockwise arc with radius 200 from the current position to X=100 Y=50)
</pre>

### Notes

* If the required parameters are present in a G2/G3 command, RRF does not report errors in arc commands where the parameters describe an arc that is invalid. It will attempt to draw the ‘best fit’ arc, then complete the move with a linear move to the endpoint. For example, if the generated Gcode is:
<br>
<pre class="cblock">
G1 X50 Y0
G2 X60 Y0 J10
</pre>
RRF will aim to finish the move +10 along the X axis. The J parameter sets the arc centre to Y10 (J is relative to the current Y position). So it draws the arc, to X60 Y10 (a 3/4 circle going clockwise with G2), then a linear move to X60 Y0. If this last linear move is unwanted, the command should be `G2 X60 Y10 J10`. This is inconsistent with the NIST standard, where invalid arcs should result in an error. This is scheduled to be fixed in RRF 3.5.1.
* **RRF 3.3 and later:** Use of I, J and K parameters depends on the plane selected with G17, G18 or G19. Use I and J for the XY plane (G17), I and K for XZ plane (G18), and J and K for YZ plane (G19).
* RRF maintains a flag for feed rate (F parameter). For all G1/2/3 moves (and G0 moves in FDM mode) the following is true:
  * Each input channel (SD card, USB, http, telnet etc) has its own flag for feed rate.
  * At the end of running config.g at startup, the flag state is copied to all input channels. If no feed rate is specified in config.g, the default of 3000mm/min (50mm/s) is used.
  * The feed rate stored by the flag is used if the next G0/1/2/3 command doesn't include an F parameter.
  * The flag state is saved when a macro starts and is restored when a macro ends.

## G4: Dwell

Pause the machine for a period of time.

### Parameters

* **Pnnn** Time to wait, in milliseconds
* **Snnn** Time to wait, in seconds

### Examples
<br>
<pre class="cblock">
G4 P200 ; wait for 200 milliseconds
</pre>

In this case, sit still doing nothing for 200 milliseconds. The state of the machine, for example the bed and extruder temperatures, will still be preserved and controlled during the delay.

## G10: Tool Temperature Setting

Note that this use of G10 may be deprecated in a future version of RRF, although it will remain available for a substantial time. It will be replaced with [M568](/User_manual/Reference/Gcodes/M568){target=_blank} which can already be used for temperature setting in firmware 3.3beta2 and later.

This form of the G10 command is recognised by having a P combined with at least an R or S parameter.

### Usage

* G10 Pnnn Rnnn Snnn

### Parameters

* **Pnnn** Tool number
* **Rnnn** Standby temperature(s)
* **Snnn** Active temperature(s)

### Order dependency

The tool must be created with M563 before it is referenced with this command.

### Examples
<br>
<pre class="cblock">
G10 P1 R140 S205 ; set standby and active temperatures for tool 1
</pre>

Remember that any parameter that you don't specify will automatically be set to the last value for that parameter.

The R value is the standby temperature in ^o^C that will be used for the tool, and the S value is its operating temperature. If you don't want the tool to be at a different temperature when not in use, set both values the same.

Temperatures set with G10 do not wait for the heaters to reach temp before proceeding. In order to wait for the temp use a M116 command after the G10 to wait for temps to be reached.

See the [T code (select tool)](/User_manual/Reference/Gcodes/T){target=_blank} below. In tools with multiple heaters the temperatures for them all are specified thus: R100.0:90.0:20.0 S185.0:200.0:150.0 .

## G10: Set workplace coordinate offset or tool offset

This form of the G10 command is recognised by having either or both of the L and P parameters. Supported on the Duet 2 series and later Duets.

### Parameters

* **Ln** (Default L1) Mode (see below)
* **Pnn** Tool number (default: current tool) if L=1, coordinate system number (default: current coordinate system) if L=2 or L=20
* **Xnnn** X offset
* **Ynnn** Y offset
* **U,V,W,A,B,C,D,...nnn** other axis offsets^1^
* **Znnn** Z offset^2^

**Modes**

* L1: this sets the tool offset, as if the L parameter was not present
* L2: this sets the origin of the coordinate system number specified by the P parameter (1 to 9) to the specified X, Y, Z... values
* L20: this is similar to L2 except that the origin is specified relative to the current position of the tool.

### Notes

^1^Tool offsets are applied after any X axis mapping has been performed. Therefore if for example you map X to U in your M563 command to create the tool, you should specify a U offset not an X offset. If you map X to both X and U, you can specify both offsets.

^2^It's usually a bad idea to put a non-zero Z value in as well unless the tools are loaded and unloaded by some sort of tool changer or are on independent carriages. When all the tools are in the machine at once they should all be positioned at the same Z height to avoid a lower tool colliding with the object while a higher tool is printing.

Tool offsets are given as the offset of the nozzle relative to the print head reference point, so the signs are opposite to what you might expect because tool offsets are subtracted from the required printing locations during printing.

Any parameter that you don't specify will automatically be set to the last value for that parameter. That usually means that you want explicitly to set Z0.0. RepRapFirmware will report the tool parameters if only the tool number is specified.

See also [M585](/User_manual/Reference/Gcodes/M585){target=_blank}.

**Tool Offset Examples**
<br>
<pre class="cblock">
G10 P2 X17.8 Y-19.3 Z0.0    ; sets the offset for tool 2 to the X, Y, and Z values specified
G10 L1 P2 X17.8 Y-19.3 Z0.0 ; sets the offset for tool 2 to the X, Y, and Z values specified
</pre>

**Coordinate Offset Example**

Suppose the current machine coordinates are `X110 Y110 Z20` and you want to make this the origin (i.e. `X0 Y0 Z0`) of the second coordinate system (accessible via G55) then there are two options:

1. `G10 L2 P2 X110 Y110 Z20`
1. `G10 L20 P2 X0 Y0 Z0`

The first example will set offsets to be subtracted from the current machine coordinates.

The second example will set the coordinates of the current position in the specified coordinate system directly.

### Order dependency

If this command refers to any axes other than X, Y and Z then it must appear later in config.g than the M584 command that creates those additional axes.

## G10: Retract

This form of the G10 command is recognised by having no parameters.

### Parameters

* (no parameters in the RepRapFirmware implementation)

### Examples
<br>
<pre class="cblock">
G10
</pre>

### Notes

* Retracts filament then performs any zlift/hop according to settings of M207.
* RepRapFirmware recognizes G10 as a command to set tool offsets and/or temperatures if the P parameter is present, and as a retraction command if it is absent.
* G10 will retract all extruders associated with a tool as defined by M563, regardless of the mixing ratio set in M567

## G11: Unretract

### Parameters

* no parameters in the RepRapFirmware implementation)

### Examples
<br>
<pre class="cblock">
G11
</pre>

### Notes

* Unretracts/recovers filament after undoing any zlift/hop according to settings of M207.
* G11 will unretract all extruders associated with a tool as defined by M563, regardless of the mixing ratio set in M567

## G17: Select XY plane for arc moves

Supported from RepRapFirmware 3.3beta1.

**Parameters:** none

### Description

The active plane determines how the tool path of an arc (G2 or G3) is interpreted. 

### Notes

* RRF maintains a flag for the selected plane for arc moves (G2 or G3). As such, the following is true:
  * Each input channel (SD card, USB, http, telnet etc) has its own flag for the selected plane.
  * At the end of running config.g at startup, the flag state is copied to all input channels. If no plane is specified in config.g, the default G17 (XY plane) is used.
  * The flag state is saved when a macro starts and is restored when a macro ends.
* G17 is supported in RRF 2.03 to RRF 3.2.2, however as no other plane selection command (G18, G19) is supported, it accepts this command, but takes no action on receiving it.

## G18: Select XZ plane for arc moves

Supported from RepRapFirmware 3.3beta1.
See 'G17: Select XY plane for arc moves' for usage.

## G19: Select YZ plane for arc moves

Supported from RepRapFirmware 3.3beta1.
See 'G17: Select XY plane for arc moves' for usage.

## G20: Set Units to Inches

### Examples
<br>
<pre class="cblock">
G20 ; set units to inches
</pre>

### Description

Units from this command onwards are in inches. Note that this is only intended to affect G0, G1 and other commands commonly found in GCode files that represent objects to print. Specifically G20 only affects: G0 to G3, G10 and G92. So you should use metric values in config.g when configuring the printer and then change to inches with G20 at the end of it if the GCodes you want to send to move the machine are expressed in inches by default.

### Notes

* From RRF 2.03 and later, RRF maintains a flag for the set units (mm or inches, G20 or G21). As such, the following is true:
  * Each input channel (SD card, USB, http, telnet etc) has its own flag for the units setting.
  * At the end of running config.g at startup, the flag state is copied to all input channels. If no units are specified in config.g, the default G21 (mm) is used.
  * The flag state is saved when a macro starts and is restored when a macro ends.

## G21: Set Units to Millimeters

### Examples
<br>
<pre class="cblock">
G21 ; set units to millimeters
</pre>

### Description

Units from this command onwards are in millimeters. This is the default.

### Notes

* From RRF 2.03 and later, RRF maintains a flag for the set units (mm or inches, G20 or G21). As such, the following is true:
  * Each input channel (SD card, USB, http, telnet etc) has its own flag for the units setting.
  * At the end of running config.g at startup, the flag state is copied to all input channels. If no units are specified in config.g, the default G21 (mm) is used.
  * The flag state is saved when a macro starts and is restored when a macro ends.

## G28: Home

### Parameters

*This command can be used without any additional parameters.*

* **X** Flag to home the X axis
* **Y** Flag to home the Y axis
* **Z** Flag to home the Z axis
* **U, V, W, A, B, C, D** Flags to home additional axes that may have been created

**Restrictions**

This command may not be used within a homing file.

### Examples
<br>
<pre class="cblock">
G28    ; Home all axes
G28 XZ ; Home the X and Z axes
</pre>

The **X** and **Z** parameters in this example act only as flags. Any coordinates given are ignored. For example, G28 Z10 results in the same behavior as G28 Z. Delta printers cannot home individual axes, but must always home all three towers, so the **X Y Z** parameters are simply ignored if the Firmware is in Delta mode.

The purpose of homing is to move the specified axes in such a way as to establish a known position for them, for example by moving an axis motor until an endstop switch is triggered. Homing an axis normally leaves it in a fixed position, however this position needs not be the zero position.

The way in which each axis is homed is completely configurable using the homing macro files, which specify what actions are taken. Execution of the G28 command is as follows:

* If there are no G28 parameters that correspond to axes that exist, or if the printer is a delta, then all axes are to be homed. Otherwise, the axes to be homed are determined by enumerating parameters of the G28 command.
* Each axis or delta tower to be homed is flagged as "position not known".
* If **ALL** axes or towers are to be homed, the file **homeall.g** is processed, except that on a delta printer **homedelta.g** is processed. If this process results in at least one axis, but not all axes, becoming flagged as "position known", an attempt will be made to home the remaining axes as if the G28 command had listed those axes.
* For each remaining axis flagged as "to be homed" the appropriate homing file is executed (**homex.g**, **homey.g**, **homez.g** etc.).

For Cartesian printers that use a Z probe to home Z instead of an endstop it is sensible to setup the **homeall.g** with the XY axes to home first, then move the carriage to a safe position –usually the middle of the bed– where it can safely probe downward to home Z. For an example see [Configuring RepRapFirmware for a Cartesian printer](/User_manual/Machine_configuration/Configuration_cartesian){target=_blank}.

Neither homeall.g nor any other homing file may itself contain a G28 command, however it may use a M98 command to invoke another homing file.

Because the behaviour of **G28** can be complex, it is recommended to consider the printer actions carefully before including **G28** in your ending GCode. On a Cartesian or CoreXY it could result in colliding with the printed object. An alternative to move the carriage at the completion of a print is to use **G0** or **G1**.

## G29: Mesh bed probe

This command uses a probe to measure the bed height at 4 or more points to determine its tilt and overall flatness. It then enables mesh bed compensation so that the nozzle will remain parallel to the bed. The printer must be homed with [G28](/User_manual/Reference/Gcodes/G28){target=_blank} before using this command.

### Usage

* G29
* G29 S0
* G29 S1 [P"filename"]
* G29 S2
* G29 S3 P"filename"
* G29 S4 P"filename" *(only supported in Duet 3 builds of RRF 3.5 and later)*

### Parameters

* **S0** Probe the bed, save the height map in a file on the SD card, and activate mesh bed compensation. The height map is stored in file is */sys/heightmap.csv*.
* **S1** Load the height map from file and activate mesh bed compensation. The default filename is as for S0 but a different filename can be specified using the P parameter.
* **S2** Disable mesh bed compensation and clear the height map (also clears the map of invalid probe points in builds that support it)
* **S3** Save height map to the specified file (supported in RRF 2.04 and later)
* **S4**  (supported in Duet 3 builds of RRF 3.5 and later) Load the grid definition and map of valid probe points from the specified file, default */sys/probePoints.csv*
* **P"file.csv"** Optional file name for height map file or probe points file to save with **S3** or load with **S1** or **S4**.
* **Kn** (supported in RRF 3.01 and later only, default 0) Z probe number

If G29 is commanded with no S parameter, then file **sys/mesh.g** is run if it exists, and in RRF 3.3 and later any parameters present are passed to mesh.g. If sys/mesh.g is not present then the command behaves like G29 S0.

### Examples
<br>
<pre class="cblock">
G29 S0                    ; Probe the bed, save height map to "heightmap.csv" and enable mesh bed compensation
G29 S3 P"usual.csv"       ; Save the current height map to file "usual.csv"
G29 S2                    ; Disable mesh bed compensation
G29 S1 P"usual.csv"       ; Load height map file "usual.csv" and enable mesh bed compensation
G29 S4 P"probePoints.csv" ; Load probe points file "probePoints.csv"
</pre>

### Notes

* To define the probe grid, see [M557](/User_manual/Reference/Gcodes/M557){target=_blank}.
* You can define a height to taper off the compensation using [M376](/User_manual/Reference/Gcodes/M376){target=_blank}
* You can find more detailed information about setting up [Mesh Bed Compensation here](/User_manual/Connecting_hardware/Z_probe_mesh_bed){target=_blank}.
* To see the format of a height map file, generate one and then download it in DWC
* The S4 subfunction supports selective probing, such as probing a grid with holes in it. The probe points file (default */sys/probePoints.csv*) needs to be manually created. To use:
  1. Create a valid probe points file; the default file name is */sys/probePoints.csv*. The format of a probe points file is similar to a height map file except for the following:
  - The first line must start with "RepRapFirmware probe points file v2" instead of "RepRapFirmware height map file v2" (the rest of the line is not processed)
  - The fourth and subsequent lines have the value 1 at points that are to be probed if they are reachable and 0 in points that are to be omitted.
  2. Send an appropriate G29 S4 command, eg `G29 S4 P"probePoints.csv"`.
  3. When G29 S0 is called subsequently, the grid definition defined in the probe points file is used instead of the grid defined by M557, and reachable points are probed or not as indicated in the file.

## G30: Single Z-Probe

### Usage

* G30 [Pnnn] [Xnnn] [Ynnn] [Znnn] [Hnnn] [Snnn]

### Parameters

* **Pnnn** Probe point number
* **Xnnn** X coordinate
* **Ynnn** Y coordinate
* **Znnn** Z coordinate
* **Hnnn** Height correction
* **Snnn** Set parameter
* **Kn** (supported in RRF 3.01-RC5 and later only, default 0) Z probe number

### Examples
<br>
<pre class="cblock">
G30     ; Probe the bed at the current XY position. When the probe is triggered, set the Z coordinate to the probe trigger height.
G30 S-1 ; Probe the bed at the current XY position. When the probe is triggered, do not adjust the Z coordinate, just report the machine height at which the probe was triggered.
G30 S-2 ; Probe the bed at the current XY position. When the probe is triggered, adjust the Z offset of the current tool to make the current position Z=0.
G30 S-3 ; Probe the bed and set the Z probe trigger height to the height it stopped at (supported in RRF 2.03 and later)<br>
G30 P0 X20 Y50 Z-99999       ; Probe the bed at X20 Y50 and save the XY coordinates and the height error as point 0
G30 P3 X180 Y180 Z-99999 S4  ; Probe the bed at X180 Y180, save the XY coordinates and the height error as point 3 and calculate 4-point compensation or calibration
G30 P3 X180 Y180 Z-99999 S-1 ; As previous example but just report the height errors
</pre>

### Description

**Caution:** the XY coordinates are permitted to be outside the normal printable bed area! This is intentional, because some printers (e.g. delta printers) benefit from probing areas not used for printing.

**G30 without a P parameter**

This probes the bed starting at the current height.  Depending on the value of the S parameter (0, -1, -2 or -3) it can be used to home the Z axis, to measure the Z probe trigger height, to adjust the Z offset of the current tool, or to adjust the Z probe trigger height.

* G30, G30 S0 or G30 S-4 or lower are just normal probes. When the probe is triggered, sets the Z coordinate to the probe trigger height.
* G30 S-1 probes and reports the Z value when the probe triggers.
* G30 S-2 probes and adjusts the tool Z offset to make the actual stop height match the configured value. A tool must be selected first when using G30 S-2.
* G30 S-3 probes and adjusts the probe trigger height to match the actual stop height.

**G30 with a P parameter**

This is used for operations that are performed after the printer has been homed and that require the height error at more than one probe point to be measured. These operations are typically performed in the bed.g file, with a G30 line for each probe point. With a Z parameter of -9999 or lower, the head moves to the specified XY coordinates and the dive height (set using the H parameter in the M558 command), and probes the bed.

For example, a bed.g for a Cartesian/CoreXY machine probing 3 points might be:
<br>
<pre class="cblock">
G28 ; home
G30 P0 X20 Y190 Z-99999 ; probe point 0 near a leadscrew
G30 P1 X180 Y190 Z-99999 ; probe point 1 near a leadscrew
G30 P2 X100 Y10 Z-99999 S3 ; probe point 2 near a leadscrew and calibrate 3 motors
</pre>

The **P parameter** is the probe point number for each G30 command. It should start at P0 and increase sequentially for each additional probe point.

The **S parameter** on the last G30 command in the sequence indicates that a complete set of points has been probed and instructs the firmware what sort of calibration to perform.

* Cartesian/CoreXY kinematics: S-1 will report the Z offset for each probed point, but no calibration is done. S0 specifies that the number of factors to be calibrated is the same as the number of points probed. Otherwise, the value indicates the number of factors to be calibrated, which must be no greater than the number of points probed, eg S3 in the above example with 3 points probed. See also: [Bed levelling using multiple independent Z motors](/User_manual/Connecting_hardware/Z_probe_auto_levelling){target=_blank}
* Linear delta kinematics: S-1 will report the Z offset for each probed point, but no calibration is done. S parameter values of 3, 4, 6, 7, 8 or 9 are for auto calibration. See [Calibrating a Delta Printer, setting up the bed.g file](/User_manual/Tuning/Delta_calibration#setting-up-the-bedg-file){target=_blank} for a more detailed explanation.
* Rotary delta kinematics: S-1 will report the Z offset for each probed point, but no calibration is done. S parameter values of 3, 4, 5, or 7 are for auto calibration (experimental). <!--See [[Configuring RepRapFirmware for a Rotary Delta]] for a more detailed explanation.-->

NOTE: From RepRapFirmware version 1.09 to 3.01-RC4, the number of factors may be 3, 4 or 5 when doing old-style auto bed compensation on a Cartesian or CoreXY printer. This form of bed compensation has been removed in RRF 3.01-RC5 and later.

If a "normal" **Z parameter** is given instead of -9999 or lower, then the bed is not probed, but instead that value is used as if the Z probe had triggered at that height.

The **H parameter** is an optional height correction for that probe point. It allows for the Z probe having a trigger height that varies with XY position. The nominal trigger height of the Z probe (e.g. at bed centre) is declared in the Z parameter of the G31 command in the config.g file. When you probe using G30 and the probe triggers, the firmware will assume that the nozzle is at the nominal trigger height plus the value you have in the H parameter. For example, when doing delta calibration, it can account for the change in trigger height caused by effector tilt, if the vertical offset caused by the tilt has been measured.

The **K parameter** is applicable to all G30 commands. It is the Z probe number, default 0. It is not remembered between G30 commands, it always defaults to 0.

## G31: Set or Report Current Probe status

### Usage

* G31 Pnnn Xnnn Ynnn Znnn Tnnn Snnn Knnn Hnnn ; RRF v3.3 and later
* G31 Pnnn Xnnn Ynnn Znnn Cnnn Snnn Knnn Hnnn ; RRF v3.0 to v3.2
* G31 Pnnn Xnnn Ynnn Znnn Cnnn Snnn Tnnn ; RRF v2.x and earlier

### Parameters

### Tabs {.tabset}

#### RepRapFirmware v3.3 and later

* **Pnnn** Trigger value
* **Xnnn** Probe X offset^1^
* **Ynnn** Probe Y offset^1^
* **U,V,W,A,B,C...nnn** Probe Offsets for all axes except Z^1^
* **Znnn** Trigger Z height
* **Snnn** Calibration temperature^2^
* **Tnnn or Tnnn:nnn** Temperature coefficient^3^
* **Knnn** Selects the Z probe number. If there is no K parameter then the current Z probe number is used. The current Z probe number is 0 at startup.
* **Hnnn** Selects the sensor number (defined by M308) to use for temperature compensation when the S and T parameters are used.^2^

##### Notes

^1^ X,Y,U,V,W,A,B,C...nnn offsets of the Z probe relative to the print head (i.e. the position when the empty tool is selected) can be specified. This allows you to calculate your probe coordinates based on the geometry of the bed, without having to correct them for Z probe X,Y,U,V,W,A,B,C...nnn offset.

^2^ Optional parameter 'S' specifies the temperature in °C at which the specified Z parameter is correct. The default is current temperature. In RRF3 you must specify which temperature sensor to use in the 'H' parameter.

^3^ Optional parameter 'T' specifies one, or two, temperature coefficients of the Z parameter, default zero. This is useful for probes that are affected by temperature.
* If one parameter is specified, it is the variation in Z parameter height with the change in sensor temperature in mm/°C. The parameter is applied to the difference between current measured temperature and calibration temperature 'S'. For example, `G31 Z1.2 T0.02 S20 H2` when sensor 2 measures 26C would calculate trigger height as 1.2 + 0.02x6x6 = 1.92mm
* If two parameters are specified, the first is the variation in Z parameter height with the change in sensor temperature in mm/°C, and the second is variation in Z parameter height with the square of temperature. The parameters are applied to the difference between current measured temperature and calibration temperature 'S'. For example, `G31 Z1.2 T0.03:0.02 S20 H2` when sensor 2 measures 26C would calculate trigger height as 1.2 + 0.03x6 + 0.02x6x6 = 2.1mm

#### RepRapFirmware v3.0 to v3.2

* **Pnnn** Trigger value
* **Xnnn** Probe X offset^1^
* **Ynnn** Probe Y offset^1^
* **Znnn** Trigger Z height
* **Snnn** Calibration temperature^2^
* **Cnnn or Cnnn:nnn** Temperature coefficient^3^
* **Knnn** Selects the Z probe number. If there is no K parameter then the current Z probe number is used. The current Z probe number is 0 at startup.
* **Hnnn** Selects the sensor number (defined by M308) to use for temperature compensation when the C and S parameters are used.^2^

##### Notes

^1^ X and Y offsets of the Z probe relative to the print head (i.e. the position when the empty tool is selected) can be specified. This allows you to calculate your probe coordinates based on the geometry of the bed, without having to correct them for Z probe X and Y offset.

^2^ Optional parameter 'S' specifies the temperature in °C at which the specified Z parameter is correct. The default is current temperature. In RRF3 you must specify which temperature sensor to use in the 'H' parameter.

^3^ Optional parameter 'C' specifies one, or two (RRF v3.2), temperature coefficients of the Z parameter, default zero. This is useful for probes that are affected by temperature.
* If one parameter is specified, it is the variation in Z parameter height with the change in sensor temperature in mm/°C. The parameter is applied to the difference between current measured temperature and calibration temperature 'S'. For example, `G31 Z1.2 C0.02 S20 H2` when sensor 2 measures 26C would calculate trigger height as 1.2 + 0.02x6x6 = 1.92mm
* If two parameters are specified (RRF v3.2), the first is the variation in Z parameter height with the change in sensor temperature in mm/°C, and the second is variation in Z parameter height with the square of temperature. The parameters are applied to the difference between current measured temperature and calibration temperature 'S'. For example, `G31 Z1.2 C0.03:0.02 S20 H2` when sensor 2 measures 26C would calculate trigger height as 1.2 + 0.03x6 + 0.02x6x6 = 2.1mm

#### RepRapFirmware v2.x and earlier

* **Pnnn** Trigger value
* **Xnnn** Probe X offset^1^
* **Ynnn** Probe Y offset^1^
* **Znnn** Trigger Z height
* **Snnn** Calibration temperature^2^
* **Cnnn** Temperature coefficient^3^
* **Tnnn** (RRF 1.17 and later) Z probe type to which the S and C parameters apply, defaults to the current Z probe type as defined by M558 P parameter.^2^

##### Notes

^1^ X and Y offsets of the Z probe relative to the print head (i.e. the position when the empty tool is selected) can be specified. This allows you to calculate your probe coordinates based on the geometry of the bed, without having to correct them for Z probe X and Y offset.

^2^ Optional parameter 'S' specifies the temperature in °C at which the specified Z parameter is correct. The default is current temperature. In RRF2 the bed temperature reading is used.

^3^ Optional parameter 'C' specifies the temperature coefficient of the Z parameter, default zero. This is useful for probes that are affected by temperature. When the parameter is specified, it is the variation in Z parameter height with the change in sensor temperature in mm/°C. The parameter is applied to the difference between current measured temperature and calibration temperature 'S'. For example, `G31 Z1.2 C0.02 S20` when the bed measures 26C would calculate trigger height as 1.2 + 0.02x6x6 = 1.92mm

### Examples
<br>
<pre class="cblock">
G31 P500 Z2.6
G31 X16.0 Y1.5<br>
; RRF 3.3 and later example of probe with thermistor and temperature compensation
M558 P8 C"io2.in" H1 F1000 T6000 A3              				  ; Prusa PindaV2 Endstop
M308 S2 P"temp2" A"Pinda V2" Y"thermistor" T100000 B3950  ; Prusa PindaV2 Thermistor
G31 P500 X23 Y5 Z1.1 S21 H2 T0.02                         ; Nozzle offset - Smooth Sheet
</pre>

### Order dependency

A G31 command to set Z probe parameters must come after the M558 command that defines the Z probe.
It must also come after M584 if it references any axes beyond X and Y (RRF >=3.3beta2).

### Notes

When used on its own this reports whether the Z probe is triggered, or gives the Z probe value in some units if the probe generates height values. If combined with a Z and P field (example: G31 P312 Z0.7) this will set the Z height to 0.7mm when the Z-probe value reaches 312 when a G28 Z0 (zero Z axis) command is sent. The machine will then move a further -0.7mm in Z to place itself at Z = 0. This allows non-contact measuring probes to approach but not touch the bed, and for the gap left to be allowed for. If the probe is a touch probe and generates a simple 0/1 off/on signal, then G31 Z0.7 will tell the RepRap machine that it is at a height of 0.7mm (as configured by Z0.7 in this example) when the probe is triggered.

If you are using the nozzle as a probe (for example with a peizo or switch that the hotend had a travel distance to trigger then remember the Z offset needs to be negative (ie the probe triggers under Z0

Separate G31 parameters may be defined for different probe types (i.e. 0+4 for switches, 1+2 for IR probes and 3 for alternative sensors). To specify which probe you are setting parameters for, send a [M558](/User_manual/Reference/Gcodes/M558){target=_blank} command to select the probe type before sending the G31 command, or use the T parameter.

## G32: Run bed.g macro

### Parameters

In RRF 3.3 and later any parameters will be passed to macro file bed.g.

### Examples
<br>
<pre class="cblock">
G32 ; execute macro bed.g
</pre>

The firmware executes macro file **bed.g**. This macro normally uses G30 commands to probe the bed and then perform auto calibration of a delta printer (see [Calibrating a delta printer](/User_manual/Tuning/Delta_calibration){target=_blank}), or perform bed levelling by moving the Z leadscrews independently, or display the manual corrections needed to the bed levelling screws.

For more detail on using G32 for automatic Delta calibration see: [Calibrating a delta printer](/User_manual/Tuning/Delta_calibration){target=_blank}

For more detail on using G32 for automatic leveling of a cartesian or CoreXY see: [Bed levelling using multiple independent Z motors](/User_manual/Connecting_hardware/Z_probe_auto_levelling){target=_blank}

For more detail on using G32 for manual bed leveling assistant see: [Using the manual bed levelling assistant](/User_manual/Connecting_hardware/Z_probe_manual_levelling){target=_blank}

## G38.2: Straight Probe

Probe toward workpiece, stop on contact, signal error if failure. Supported in RepRapFirmware 3 and later.

### Parameters

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

Feedrate used for this move is the configured feedrate of the used probe (see [M558 Fnnn](/User_manual/Reference/Gcodes/M558){target=_blank}).

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

### Examples
<br>
<pre class="cblock">
G53 G1 X50 Y50 Z20 ; these moves are in machine coordinates
G1 X100 Y100 Z20 ; these moves, on the line after G53, are in the currently selected coordinate system
</pre>

G53 causes all coordinates in movement commands on the remainder of the current line of GCode to be interpreted as machine coordinates, ignoring any coordinate offset of the workplace coordinate system currently in use, and also ignoring any tool offsets. G53 is not modal and must be programmed on each line on which it is intended to be active. On the following line without G53, the coordinate system reverts to the currently selected coordinate system.

See the [NIST GCode Interpreter Version 3 standard](http://www.nist.gov/manuscript-publication-search.cfm?pub_id=823374){target=_blank} for more details.

## G54: Select coordinate system

Selects coordinate system 1. Supported on the Duet 2 and later Duets only.

G54 selects coordinate system 1, G55 selects coordinate system 2 etc. up to G59 which selects coordinate system 6. G59.1 selects coordinate system 7, G59.2 selects system 8 and G59.3 selects system 9.

Initially, coordinate system 1 is in use, and all coordinate systems have zero offset from the machine coordinates. To set coordinate system offsets, use the G10 command with the L2 parameter.

See the [NIST GCode Interpreter Version 3 standard](http://www.nist.gov/manuscript-publication-search.cfm?pub_id=823374){target=_blank} for more details.

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

### Usage

* **G60 Snn**

### Parameters

* **Snn** specifies memory slot number (0-based) to save into (default 0)

RepRapFirmware for Duets generally provides slots 0 thru 5. When a print is paused the coordinates are saved to slot 1 automatically, and at the start of a tool change the coordinates are saved to slot 2 automatically. The remaining slots are free to use for any purpose. Use G0 or G1 with the appropriate R parameter to move the current tool to a saved position.

## G68: Coordinate rotation

### Usage

* G68 Xnnn Ynnn Rnnnn
* G68 Annn Bnnn Rnnnn

### Parameters

* **Xnnn, Ynnn...** Centre coordinates to rotate about
* **Annn** first centre coordinate in the selected plane (e.g. equivalent to Xnnn if the selected plane is XY)
* **Bnnn** second centre coordinate in the selected plane (e.g. equivalent to Ynnn if the selected plane is XY)
* **Rnnn** angle to rotate in degrees. Positive angles rotate anticlockwise when viewing the selected plane from above.

Rotates the coordinate system in the current plane as selected by [G17](/User_manual/Reference/Gcodes/G17){target=_blank}, [G18](/User_manual/Reference/Gcodes/G18){target=_blank} or [G19](/User_manual/Reference/Gcodes/G19){target=_blank}. You may either specify the coordinates of the two axes of the selected plan (e.g. X and Y if using the default XY plane or after G17) or you may specify A and B coordinates.

RepRapFirmware implements G68 for the XY plane only.

## G69: Cancel coordinate rotation

### Parameters

* No additional parameters

### Usage

* G69

### Description

This cancels any coordinate rotation that was set up by G68.

## G90: Set to Absolute Positioning

### Parameters

* No additional parameters

### Usage

* G90

### Description

All coordinates from now on are absolute, relative to the origin of the machine.

### Notes

* RepRapFirmware uses [M82](/User_manual/Reference/Gcodes/M82){target=_blank} to set the extruder to absolute mode: extrusion is NOT set to absolute using G90
* RRF maintains a flag for the absolute/relative positioning state. As such, the following is true:
  * Each input channel (SD card, USB, http, telnet etc) has its own flag for the absolute/relative positioning state.
  * At the end of running config.g at startup, the flag state is copied to all input channels. If no absolute/relative positioning is specified in config.g, the default G90 (absolute) is used.
  * The flag state is saved when a macro starts and is restored when a macro ends.

## G91: Set to Relative Positioning

### Parameters

* No additional parameters

### Usage

* G91

### Description

All coordinates from now on are relative to the last position.

### Notes

* RepRapFirmware uses [M83](/User_manual/Reference/Gcodes/M83){target=_blank} to set the extruder to relative mode: extrusion is NOT set to relative using G91
* RRF maintains a flag for the absolute/relative positioning state. As such, the following is true:
  * Each input channel (SD card, USB, http, telnet etc) has its own flag for the absolute/relative positioning state.
  * At the end of running config.g at startup, the flag state is copied to all input channels. If no absolute/relative positioning is specified in config.g, the default G90 (absolute) is used.
  * The flag state is saved when a macro starts and is restored when a macro ends.

## G92: Set User Position

### Parameters

* *This command can be used without any additional parameters.*
* **Xnnn** new X axis position
* **Ynnn** new Y axis position
* **Znnn** new Z axis position
* **Ennn** new extruder position

### Examples
<br>
<pre class="cblock">
G92 X10 E90
</pre>

Allows manual specification of the axis positions by setting the current user position to the values given. The machine position is updated as necessary taking into account tool offsets and workplace coordinate offsets. The example above would set the machine's X user coordinate to 10, and the extruder coordinate to 90. No physical motion will occur. In RepRapFirmware, a G92 without coordinates does nothing.

## G93: Feed Rate Mode (Inverse Time Mode) 

*CNC specific. Supported from firmware version 3.5*

### Parameters

* No additional parameters

### Examples
<br>
<pre class="cblock">
G93
</pre>

### Description

G93 is Inverse Time Mode. In inverse time feed rate mode, an F word means the move should be completed in (one divided by the F number) minutes. For example, F2.0 means the move should be completed in a half a minute.

### Notes

* When the inverse time feed rate mode is active, an F word must appear on every line which has a G1, G2, or G3 motion.
* An F word on a line that does not have G1, G2, or G3 is ignored.
* Being in inverse time feed rate mode does not affect G0 (rapid move) motions.
* RRF maintains a flag for the feed rate mode selected, which is either Inverse Time Mode or Units per Minute. As such, the following is true:
  * Each input channel (SD card, USB, http, telnet etc) has its own flag for the feed rate mode state.
  * At the end of running config.g at startup, the flag state is copied to all input channels. If no feed rate mode is specified in config.g, the default G94 (Units per Minute) is used.
  * The flag state is saved when a macro starts and is restored when a macro ends.

## G94: Feed Rate Mode (Units per Minute)

*CNC specific. Supported from firmware version 3.5*

### Parameters

* No additional parameters

### Examples
<br>
<pre class="cblock">
G94
</pre>

### Description

G94 is Units per Minute Mode. In units per minute feed mode, an F word is interpreted to mean the controlled point should move at a certain number of inches per minute, millimeters per minute, or degrees per minute, depending upon what length units are being used and which axis or axes are moving. 

### Notes

* RRF maintains a flag for the feed rate mode selected, which is either Inverse Time Mode or Units per Minute. As such, the following is true:
  * Each input channel (SD card, USB, http, telnet etc) has its own flag for the feed rate mode state.
  * At the end of running config.g at startup, the flag state is copied to all input channels. If no feed rate mode is specified in config.g, the default G94 (Units per Minute) is used.
  * The flag state is saved when a macro starts and is restored when a macro ends.

# M-commands

## M0: Stop or Unconditional stop

### Parameters

* *This command can be used without any additional parameters.*

### Examples
<br>
<pre class="cblock">
M0
</pre>

The effect of M0 depends on the state of the machine.

1. The firmware finishes any moves left in its buffer.
1. **Either**: if the axes are homed and if a print is being cancelled (M25), it executes the macro file **cancel.g** if present. **Or**: if M0 is sent at any other time, **stop.g** is run if present.
1. All motors are put into idle mode.
1. If there is no stop.g or cancel.g file (as appropriate) then all heaters are turned off too. In RRF versions prior to 3.4 you can prevent heaters being turned off using parameter H1.

Note: *From RRF 3.5b1, When a print file completes normally then file stop.g is run automatically even if the print file did not end with a M0 command.*


See also [M1 - sleep or conditional stop](/User_manual/Reference/Gcodes/M1){target=_blank}, [M112 - emergency stop](/User_manual/Reference/Gcodes/M112){target=_blank}.

## M1: Sleep or Conditional stop

### Examples
<br>
<pre class="cblock">
M1
</pre>

The effect of M1 depends on the state of the machine.

1. The firmware finishes any moves left in its buffer.
1. **Either**: if the axes are homed and if a print is being cancelled (M25), it executes the macro file **cancel.g** if present. **Or**: if M1 is sent at any other time, **sleep.g** is run if present.
1. All motors and heaters are are turned off.

G and M codes can still be sent, the first of which will wake it up again. See also [M0 - stop or unconditional stop](/User_manual/Reference/Gcodes/M0){target=_blank}, [M112 - emergency stop](/User_manual/Reference/Gcodes/M112){target=_blank}.

If Marlin is emulated in RepRapFirmware, this does the same as [M25](/User_manual/Reference/Gcodes/M25){target=_blank} if the code was read from a serial or Telnet connection, else the macro file **sleep.g** is run before all heaters and drives are turned off.

## M2: Program End

### Examples
<br>
<pre class="cblock">
M2
</pre>

This command terminates the current job. At present, it behaves just like M0.

Supported in v3.5-b1 and later.

## M3: Spindle On, Clockwise

Supported in RepRapFirmware version 1.20 and later when the printer mode is set to CNC mode (see [M453](/User_manual/Reference/Gcodes/M453){target=_blank}) and in CNC and FFF mode from RepRapFirmware version 3.5

### Parameters

* **Snnn** Spindle RPM (CNC mode)
* **Pnnn** Spindle slot (CNC mode only). Directly address a spindle slot.

### Examples
<br>
<pre class="cblock">
M3 S4000 ; turn on spindle at speed of 4000 RPM, clockwise
</pre>

#### M3 in RepRapFirmware 3.3 and later

M3 can be called without any parameters and will start the spindle of the current tool turning clockwise at the spindle RPM of that tool.

Using the S parameter will additionally set the spindle RPM of the current tool.

It is an error if there is no tool active or the active tool does not have a spindle assigned and there is no P parameter provided to define which spindle this should be applied to.

#### M3 in RepRapFirmware 3.5 and later
M3 M4 and M5 commands are now supported in FDM mode as well as CNC mode. This will allow mixing of additive and subtractive tools wihtout switching mode.

## M4: Spindle On, Counterclockwise

Supported in RepRapFirmware version 1.20 and later when the printer mode is set to CNC (see [M453](/User_manual/Reference/Gcodes/M453){target=_blank}) and in CNC and FFF mode from RepRapFirmware version 3.5

### Parameters

* **Snnn** Spindle RPM
* **Pnnn** Spindle slot (CNC mode only). Directly address a spindle slot.

### Examples
<br>
<pre class="cblock">
M4 S4000 ; turn on spindle at speed of 4000 RPM, counterclockwise
</pre>

#### M4 in RepRapFirmware 3.3 and later

M4 can be called without any parameters and will start the spindle of the current tool turning counterclockwise at the spindle RPM of that tool.

Using the S parameter will additionally set the spindle RPM of the current tool.

It is an error if there is no tool active or the active tool does not have a spindle assigned and there is no P parameter provided to define which spindle this should be applied to.

#### M4 in RepRapFirmware 3.5 and later
M3 M4 and M5 commands are now supported in FDM mode as well as CNC mode. This will allow mixing of additive and subtractive tools without switching mode.

## M5: Spindle Off

Supported in RepRapFirmware version 1.20 and later when the printer mode is set to CNC mode (see [M453](/User_manual/Reference/Gcodes/M453){target=_blank}) and in CNC and FFF mode from RepRapFirmware version 3.5

### Parameters

* none

### Examples
<br>
<pre class="cblock">
M5 ; turn of spindle/laser
</pre>

#### M5 in RepRapFirmware 3.3 and later

M5 will stop the spindle of the current tool (if any) or all defined spindles if the current tool has no spindle assigned or there is no active tool.

#### M5 in RepRapFirmware 3.5 and later
M3 M4 and M5 commands are now supported in FDM mode as well as CNC mode. This will allow mixing of additive and subtractive tools without switching mode.

## M17: Enable all stepper motors

Available in RepRapFirmware 3.3beta2 and later.

### Parameters

* *This command can be used without any additional parameters.*
* **X** X axis
* **Y** Y axis
* **Z** Z axis
* **U** U axis
* **V** V axis
* **W** W axis
* ... or any other defined axis
* **E[n]** Extruder drive(s)

### Examples
<br>
<pre class="cblock">
M17
M17 X E0
</pre>

Enables all stepper motors when used without parameters. Stepper motors can also be enabled selectively. For example, M17 X E0:2 will enable the X, extruder 0 and extruder 2 motors. Use this command to energise a motor for stealthChop tuning, followed by a short pause eg G4 P100 to allow the driver to establish the motor parameters.

## M18: Disable all stepper motors

### Parameters

* *This command can be used without any additional parameters.*
* **X** X axis
* **Y** Y axis
* **Z** Z axis
* **U** U axis
* **V** V axis
* **W** W axis
* ... or any other defined axis
* **E[n]** Extruder drive(s)

### Examples
<br>
<pre class="cblock">
M18
M18 X E0
</pre>

Disables stepper motors and allows axes to move 'freely.' Stepper motors can be disabled selectively. For example, M18 X E0:2 will disable the X, extruder 0 and extruder 2 motors. Also see M84.

## M20: List SD card

### Parameters

* *This command can be used without any additional parameters.*
* **Snnn** Output style: 0 = text (default), 2 = JSON, 3 = Verbose JSON
* **P"path"** Folder to list, defaults to the standard folder for GCode files (0:/gcodes in RepRapFirmware)
* **Rnnn** Number of files to skip, default 0, S2 and S3 only

### Examples
<br>
<pre class="cblock">
M20                      ; lists all files in the default folder of the internal SD card (0:/gcodes)
M20 S2 P"/gcodes/subdir" ; lists all files in the gcodes/subdir folder of the internal SD card, using JSON format
M20 P"1:/"               ; lists all files on the secondary (PanelDue) SD card's root
</pre>

If Marlin compatibility is enabled, a file list response is encapsulated:
<br>
<pre class="cblock">
Begin file list:
 Traffic cone.g
 frog.gcode
 calibration piece.g
 End file list
 ok
</pre>

If RepRapFirmware emulates no firmware compatibility, a typical response looks like:
<br>
<pre class="cblock">
GCode files:
 "Traffic cone.g","frog.gcode","calibration piece.g"
</pre>

RepRapFirmware always returns long filenames in the case in which they are stored.

If the S2 parameter is used then the file list is returned in JSON format as a single array called "files" with each name that corresponds to a subdirectory preceded by an asterisk, and the directory is returned in variable "dir". Example:
<br>
<pre class="cblock">
M20 S2 P"/gcodes"
 {"dir":"/gcodes","first":0,"next":0,"files":["4-piece-1-2-3-4.gcode","Hinged_Box.gcode","Hollow_Dodecahedron_190.gcode","*Calibration pieces"]}
</pre>

Returned value "first" is the number of files that were skipped (as specified in the R parameter), and "next" is the number to skip next time to retrieve the next block of filenames. If "next" is zero then there are no more filenames.

The S3 parameter is similar to S2 but includes "type", "name", "size", "date", e.g.
<br>
<pre class="cblock">
M20 S3 R23
{
    "dir": "0:/gcodes/",
    "first": 23,
    "files": [
        {
            "type": "f",
            "name": "Hinged_Box.gcode",
            "size": 179638,
            "date": "2022-11-09T18:56:02"
        },
        {
            "type": "f",
            "name": "frog.gcode.gcode",
            "size": 612786,
            "date": "2022-11-09T14:06:32"
        }
    ],
    "next": 0
}
</pre>



## M21: Initialize SD card

### Tabs {.tabset}

#### Standalone mode

The specified SD card is initialized.

##### Parameters

* *This command can be used without any additional parameters.*
* **Pnnn** SD card number (default 0)

##### Examples
<br>
<pre class="cblock">
M21
M21 P1
</pre>

##### Notes

* If an SD card is loaded when the machine is switched on, this will happen by default. 
* SD cards must be initialized for the other SD functions to work.

#### SBC mode

In SBC mode and RRF v3.4 or newer this code may be used to mount block devices or remote endpoints using the mount command. 

##### Parameters

* **Pnnn** Device node or remote endpoint
* **Snnn** Local directory to mount to (e.g. `0:/gcodes/remote`, optional if the device node is already present in /etc/fstab)
* **Tnnn** Mount type (-t flag, e.g. nfs)
* **Onnn** Mount options (-o flag)

##### Examples
<br>
<pre class="cblock">
M21 P"192.168.1.222:3D" S"0:/gcodes/remote" T"nfs"
M21 P"//192.168.1.222/3D" S"0:/gcodes/remote" T"cifs" O"user=myUser,password=myPass"
M21 P"curlftpfs#ftp://192.168.1.222:/3D" S"0:/gcodes/remote" T"fuse" O"user=myUser:myPass" ; requires curlftpfs package
</pre>

##### Notes

* This requires the DuetPiManagementPlugin to be running. 
* In SBC mode, this command should be in dsf-config.g NOT config.g.

## M22: Release SD card

### Tabs {.tabset}

#### Standalone mode

The specified SD card is released, so further (accidental) attempts to read from it are guaranteed to fail.

##### Parameters

* *This command can be used without any additional parameters.*
* **Pnnn** SD card number (default 0)

##### Examples
<br>
<pre class="cblock">
M22
M22 P1
</pre>

##### Notes

* This command is helpful, but not mandatory before removing the card physically.

#### SBC mode

In SBC mode and v3.4 or newer this code may be used to unmount block devices or remote endpoints using the mount command.

##### Parameters

* **Pnnn** Device node or remote endpoint

##### Examples
<br>
<pre class="cblock">
M22 P"0:/gcodes/remote"
</pre>

##### Notes

* This requires the DuetPiManagementPlugin to be running. 
* In SBC mode, this command should be in dsf-config.g NOT config.g.

## M23: Select SD file

### Examples
<br>
<pre class="cblock">
M23 filename.gco
</pre>

The file specified as filename.gco is selected ready for printing. RepRapFirmware supports long filenames as well as 8.3 format.

Full paths, including all directories and subdirectories are limited to 120 characters.

e.g.;
<br>
<pre class="cblock">
0:/gcodes/0123456789/012.gcode
</pre>

counts as 30 characters

## M24: Start/resume SD print

### Examples
<br>
<pre class="cblock">
M24
</pre>

The machine prints from the file selected with the M23 command. If the print was previously paused with M25, printing is resumed from that point. To restart a file from the beginning, use M23 to reset it, then M24.

When this command is used to resume a print that was paused, the macro file **resume.g** is run prior to resuming the print.

## M25: Pause SD print

### Examples
<br>
<pre class="cblock">
M25
</pre>

The machine pauses printing at the current position within the file. To resume printing, use [M24](/User_manual/Reference/Gcodes/M24){target=_blank}. Do not use this code to pause the print in the currently printing GCode file, use [M226](/User_manual/Reference/Gcodes/M226){target=_blank} instead. M226 is intended for use in the GCode file being printed, for example to pause after a particular layer has completed. So it waits until all the moves in the queue have been completed. M25 is intended for use from a different source of GCodes than the current print from SD card (like the web interface console, PanelDue or Macro).

M25 attempts to execute as quickly as possible and follows the following logic:

* When RRF receives M25 it will look for a move in the current queue after which it can stop without violating the configured jerk limits.
* If it finds one it stops after that move without decelerating (because the jerk limits allow that)
* If it can't find one it will plan and execute a deceleration. In this case the pause will occur 1 move + 2 seconds after M25 is sent.

That means the longest it will take to pause is 1 move + 2 seconds. In most situations pause occurs much quicker than that.

After movement is halted as described above but prior to the pause operation completing, the macro file **pause.g** is run. This allows the head to be moved away from the print, filament to be retracted, etc.

Note that if a pause is commanded while a macro is being executed, the pause will be deferred until the macro has completed.

## M26: Set SD position

### Parameters

* Snnn File position from start of file in bytes
* Pnnn (Optional) Proportion of the first move to be skipped, default 0.0, must be less than 1.0

### Examples
<br>
<pre class="cblock">
M26 S49315
</pre>

Set the file offset in bytes from the start of the SD card file selected by M23. The offset must correspond to the start of a GCode command.

## M27: Report SD print status

### Examples
<br>
<pre class="cblock">
M27
</pre>

Reports the number of bytes processed in this format, which can be processed by Pronterface:
<br>
<pre class="cblock">
SD printing byte 2134/235422
</pre>

If no file is being printed, only this message is reported:
<br>
<pre class="cblock">
Not SD printing.
</pre>

## M28: Begin write to SD card

### Examples
<br>
<pre class="cblock">
M28 filename.gco
</pre>

File specified by filename.gco is created (or overwritten if it exists) on the SD card and all subsequent commands sent to the machine are written to that file.

## M29: Stop writing to SD card

### Examples
<br>
<pre class="cblock">
M29
</pre>

File opened by M28 command is closed, and all subsequent commands sent to the machine are executed as normal.

## M30: Delete a file on the SD card

### Examples
<br>
<pre class="cblock">
M30 filename.g
M30 "filename.g"
</pre>

filename.g is deleted.

## M32: Select file and start SD print

### Examples
<br>
<pre class="cblock">
M32 filename.g
M32 "filename.g"
</pre>

It can be used when printing from SD card and does the same as M23 and M24.

## M36: Return file information

### Examples
<br>
<pre class="cblock">
M36 "filename.g"
M36
</pre>

Returns information in JSON format for the specified SD card file (if a filename was provided) or for the file currently being printed. A sample response is:
<br>
<pre class="cblock">
{"err":0,"size":436831,"fileName":"EscherLizardModified.gcode","lastModified":"2017-09-21T16:58:07","height":5.20,"layerHeight":0.20,"printTime":660,"simulatedTime":1586,"filament":[1280.7],"generatedBy":"Simplify3D(R) Version 4.0.0"}
</pre>

The "err" field is zero if successful, nonzero if the file was not found or an error occurred while processing it. The "size" field should always be present if the operation was successful. The presence or absence of other fields depends on whether the corresponding values could be found by reading the file. The "filament" field is an array of the filament lengths required from each spool. The size is in bytes, the times are in seconds, all other values are in mm. "printTime" is the printing time estimated by the slicer, "simulationTime" is the time measured when the print was simulated by the firmware. The fields may appear in any order, and additional fields may be present. Versions of RepRapFirmware prior to 3.4 do not provide the "fileName" field if information for a specific file was requested.

RepRapFirmware 3.4 and later also return information about thumbnail images embedded in the GCode file via an additional JSON field "thumbnails". A sample value for this field is:
<br>
<pre class="cblock">
"thumbnails":[{"width":32,"height":32,"fmt":"qoi","offset":103,"size":2140},{"width":220,"height":220,"fmt":"qoi","offset":2384,"size":25464}]
</pre>

The "fmt" field denotes the encoding of the thumbnail and is one of "png", "qoi" or "jpeg". The "thumbnails" field is omitted entirely if there are no thumbnails embedded in the GCode file.

## M36.1: Return embedded thumbnail data

This command is used to return the data for a thumbnail image in a GCode file. The offset value should be either the offset of the start of data for a thumbnail as returned by the M36 command, or the value returned in the "next" field by a previous M36.1 command. The response is in JSON format.

### Parameters

* **P"filename"** Name of the GCode file from which thumbnail data is to be retrieved
* **Snnnn** Byte offset into the file at which thumbnail data is to be fetched

### Example

<br>
<pre class="cblock">
M36.1 P"/gcodes/EscherLizardModified.gcode" S103
</pre>

Sample response:
<br>
<pre class="cblock">
{fileName":"EscherLizardModified.gcode","offset":103,"data":"cW9pZgAAACA....AAAAB","next":0,err": 0}
</pre>

The "fileName" and "offset" values are as given in the command. "data" is part or all of the base64-encoded thumbnail data starting at that offset. "next" is zero if there is no more data for that thumbnail, otherwise not all the thumbnail data was returned and "next" is the byte offset in the file of the rest of the thumbnail data. "err" is 0 if the command was successful, otherwise "err" is nonzero and the other fields may or may not be present. 

## M37: Simulation mode

Used to switch between printing mode and simulation mode. Simulation mode allows the electronics to compute an accurate printing time, taking into account the maximum speeds, accelerations etc. that are configured.

### Parameters

* **P"filename"** (optional) Simulate printing a file from SD card
* **Fn** (optional, only recognised if P parameter is present) F1 = update the simulation time stored in the file (default), F0 = do not update the file
* **Snn** (only recognised if P parameter is not present) Set simulation mode to nn

### Examples
<br>
<pre class="cblock">
M37 P"MyModel.g" ; run file MyModel.g in simulation mode and update the simulated time stored in the file
M37 S2           ; set the simulation mode to debug
</pre>

### Notes

M37 P"file.gcode" runs the specified file in normal simulation mode and displays the calculated job time when complete.

M37 Sn where n is nonzero enters simulation mode n. n=1 selects debug mode, n=2 selects normal simulation mode, n=3 selects partial simulation mode. All G and M codes will not be acted on, but the time they would take to execute will be calculated if the mode is 1 or 2.

M37 S0 exits simulation mode and prints the total time taken by simulated moves since entering simulation mode.

M37 with no S parameter prints the time taken by the simulation, from the time it was first entered using M37 S1, up to the current point (if simulation mode is still active) or the point that the simulation was ended (if simulation mode is no longer active).

## M38: Compute SHA1 hash of target file

Used to compute a hash of a file on the SD card and returns a hexadecimal string which is the SHA1 of the file. 

### Examples
<br>
<pre class="cblock">
M38 gcodes/myfile.g
</pre>

### Notes

If the file cannot be found, then the string "Cannot find file" is returned instead.

## M39: Report SD card information

*Supported in ReprapFirmware 1.21 and later.*

This command returns information about the SD card in the specified slot in the requested format.

### Parameters

* **Pn** SD slot number, default 0
* **Sn** Response format. S0 returns a plain text response, S2 returns a response in JSON format.

### Examples
<br>
<pre class="cblock">
M39       ; report information for SD card 0 in plain text format
M39 P1 S2 ; report information for SD card 1 in JSON format<br>
; example output from RRF 3.3
M39
SD card in slot 0: capacity 3.97Gb, free space 3.81Gb, speed 20.00MBytes/sec, cluster size 32kb
M39 S2
{"SDinfo":{"slot":0,"present":1,"capacity":3965190144,"free":3807379456,"speed":20000000,"clsize":32768}}
</pre>

### Notes

In the JSON response, capacity, free space and cluster size are in bytes. and interface speed is in bytes/second.

## M42: Switch I/O pin

### {.tabset}

**See also**

[M280](/User_manual/Reference/Gcodes/M280){target=_blank}, [M950](/User_manual/Reference/Gcodes/M950){target=_blank}

#### RepRapFirmware 3.x

##### Parameters

* **Pnnn** GPIO port number (set by M950)
* **Snnn** Pin value

##### Examples
<br>
<pre class="cblock">
M950 P0 C"exp.heater3" Q500  ; allocate GPIO port 0 to heater3 on expansion connector, 500Hz
...
M42 P0 S0.5  ; set 50% PWM on GPIO port 0
</pre>

M42 switches a general purpose I/O pin which is defined by M950. Use M42 Px Sy to set pin x to value y. The S field may be in the range 0..1 or 0..255.

##### Notes

Before you can use M42 you must create a GPIO port using M950. Then in the M42 command, the P parameter is the GPIO port number. 

The F (PWM frequency) and I (invert PWM) parameters are no longer supported in M42. Instead, use the Q (PWM frequency) and C (pin name, with ! to invert) parameters in M950 when you create the GPIO port.

In RRF 3.4, Duet 3 supports up to 32 outpus and 16 inputs, Duet 2 Wifi/Ethernet support up to 20 GPIO ports, and Duet 2 Maestro supports 10 GPIO ports. No GPIO ports are allocated by default.

#### RepRapFirmware 2.x

##### Parameters

* **Pnnn** Logical pin number
* **Snnn** Pin value
* **Fnnn** PWM frequency (optional)
* **Innn** Invert PWM (optional). I0 (no inversion) is default, I1 inverts.

##### Examples
<br>
<pre class="cblock">
M42 P3 I1 S0.5 F500  ; set Heater 3 pin to 50% PWM at 500Hz, inverted
</pre>

M42 switches a general purpose I/O pin. Use M42 Px Sy to set pin x to value y. The S field may be in the range 0..1 or 0..255.

##### Notes

In RRF 2.x, the 'P' pin number reference is an internal firmware reference named "digital pin" which It maps on different connector pins depending the hardware. See [Controlling unused IO pins](/User_manual/Connecting_hardware/IO_GPIO){target=_blank} for all the unused pin mappings in RRF 2.x.

For Duet 0.8.5 and 0.6, along with pre 1.16 versions of RepRapFirmware, see the [RepRap GCode dictionary M42 entry](http://reprap.org/wiki/G-code#M42:_Switch_I.2FO_pin){target=_blank}.

## M73: Set remaining print time

### Parameters

* **Pnn** Percentage of the print that has been completed (not used by RRF)
* **Rnn** Remaining print time in minutes

M73 is generated by some slicers, to inform the firmware about the state of the print so that it can provide a better estimate of the remaining print time.

## M80: ATX Power On

### Parameters

* **C"port_name"** (RRF 3.4.0 and later) Name of the pin used to control the power supply

### Examples
<br>
<pre class="cblock">
M80          ; sets pin in the power on state
M80 C"pson"  ; allocates the pin and sets the pin in the power on state.
M80 C"!pson" ; inverts the PS_ON output for Meanwell power supplies
</pre>

Turns on the ATX power supply from standby mode to fully operational mode using the power supply control pin on the External 5V header. If a deferred power down command was set up using M81 S1 then it is cancelled.

In RRF 3.4.0 and later, M80 will do nothing unless you have previously allocated a pin to control power using either M80 or M81 with C parameter. This would normally be done in the config.g file.

## M81: ATX Power Off

### Parameters

* **C"port_name"** (RRF 3.4.0 and later) Name of the pin used to control the power supply
* **Sn** n=0 turn power off immediately (default), n=1 turn power off when all thermostatic fans have turned off (RepRapFirmware 1.20 and later only). This parameter optional and ignored if the D parameter is present. The default is to turn off power as soon as the movement queue is empty.
* **Dnnn** Delay powering down for nnn seconds (RRF 3.5 and later only)

### Examples
<br>
<pre class="cblock">
M81 C"pson" ; allocate the PS_ON pin to power control but leave power off
M81         ; turn power off immediately
M81 S1      ; turn power off when all thermostatic fans have turned off
M81 D30 S1  ; turn power off after 30 seconds or when all thermostatic fans have turned off, whichever happens later (RRF 3.5)
</pre>

Turns off the ATX power supply. Counterpart to M80. A deferred power down command (M81 S1) that has not yet happened can be cancelled using M80.

In RRF 3.4 and later, M81 will have no effect unless a power control pin has previously been assigned using M80 or M81 with the C parameter. This would normally be done in the config.g file.

## M82: Set extruder to absolute mode

### Examples
<br>
<pre class="cblock">
M82 ; absolute extrusion mode
</pre>

### Description

Makes the extruder interpret extrusion as absolute positions.

### Notes

* RRF maintains a flag for the extruder absolute/relative positioning state. As such, the following is true:
  * Each input channel (SD card, USB, http, telnet etc) has its own flag for the extruder absolute/relative positioning state.
  * At the end of running config.g at startup, the flag state is copied to all input channels. If no absolute/relative positioning is specified in config.g, the default M82 (absolute) is used.
  * The flag state is saved when a macro starts and is restored when a macro ends.

## M83: Set extruder to relative mode

### Examples
<br>
<pre class="cblock">
M83 ; relative extrusion mode
</pre>

### Description

Makes the extruder interpret extrusion values as relative positions.

### Notes

* RRF maintains a flag for the extruder absolute/relative positioning state. As such, the following is true:
  * Each input channel (SD card, USB, http, telnet etc) has its own flag for the extruder absolute/relative positioning state.
  * At the end of running config.g at startup, the flag state is copied to all input channels. If no absolute/relative positioning is specified in config.g, the default M82 (absolute) is used.
  * The flag state is saved when a macro starts and is restored when a macro ends.

## M84: Stop idle hold

### Parameters

* *This command can be used without any additional parameters.*
* **Snnn** Idle Time-out
* X,Y, E0:1.. etc

### Examples
<br>
<pre class="cblock">
M84
M84 E0:1:2:3:4
</pre>

Stop the idle hold on all axis and extruder. Be aware that by disabling idle hold during printing, you will get quality issues.

For example, "M84 S10" will idle the stepper motors after 10 seconds of inactivity.

You can disable individual motors with the standard X, Y, Z etc switches.

## M92: Set axis steps per unit

### Parameters

* *This command can be used without any additional parameters.*
* **Xnnn** Steps per mm for the X drive
* **Ynnn** Steps per mm for the Y drive
* **Znnn** Steps per mm for the Z drive
* **Unnn** Steps per mm for the U drive
* **Vnnn** Steps per mm for the V drive
* **Wnnn** Steps per mm for the W drive
* **Ennn** Steps per mm for the extruder drive(s)
* **Snnn** Defines in which microstepping the above steps per unit are given. If omitted it will use the microstepping currently set by M350. This parameter is supported in RRF >=2.03.

### Order dependency

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

### Examples
<br>
<pre class="cblock">
M92 X80 Y80 Z80
M92 E420:500
</pre>

### Notes

Allows programming of steps per mm for motor drives. These values are reset to those set in config.g on power on, unless saved with M500. It will report the current steps/mm if you send M92 without any parameters.

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour.

Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M92 Z80, not M92 Z80:80

RepRapFirmware uses floating point maths so it is possible to use floating point numbers for steps/mm.

## M98: Call Macro/Subprogram

### Parameters

* **P"nnn"** Macro filename^1^
* **Rn** (when no P parameter is provided, RRF 3.4 and later) This is used within a macro file to indicate whether the macro can be paused from this point on. 1 = remainder of current macro can be paused **and the macro restarted from the beginning after resuming**, 0 = remainder of current macro cannot be paused. By default, a macro cannot be paused except in the case of power failure. **Do not use R1 in system macros** such as tool change macros, homing macros, pause.g or resume.g.
* If the P parameter is provided then any additional parameters will be passed to the macro^2^

### Examples
<br>
<pre class="cblock">
M98 P"mymacro.g"
</pre>

Runs the macro in the file mymacro.g. Macro calls can be nested (i.e. a macro can call another macro).

The filename may include a path to a subdirectory. For relative paths, the default folder is /sys. Absolute file paths are also supported starting with "0:/" for the internal SD card or "1:/" for the external SD card if fitted.

### Notes

^1^ In RRF 3.x, quotation marks around the filename are mandatory. In RRF2.x and earlier, string can be enclosed in quotes if required. See [Quoted Strings](/User_manual/Reference/Gcodes#quoted-strings){target=_blank} for details. 

^2^ In RRF 3.3 and later M98 supports additional parameters used to pass information to the macro being called. See the [GCode Meta Commands](/User_manual/Reference/Gcode_meta_commands){target=_blank} documentation for the details.

## M99: Return from Macro/Subprogram

### Examples
<br>
<pre class="cblock">
M99
</pre>

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

### Parameters

* **Snnn** target temperature
* **Tn** optional tool number

### Examples
<br>
<pre class="cblock">
M104 S190
M104 S220 T1
</pre>

The first example sets the active and standby temperature of the current tool to 190^o^C and return control to the host immediately (*i.e.* before that temperature has been reached by the extruder). If no tool is active, it will apply to the lowest non-negative tool number.

The second example sets the active and standby temperatures of tool 1 to 220^o^C. If tool 1 is off, it will be set to standby as well (resulting in tool 1 heating up to the temperature given).

See also [M109](/User_manual/Reference/Gcodes/M109){target=_blank}.

This command is deprecated in RRF for multi-tool machines because the G10/M568 commands provide more control, for example separate active and standby temperatures. However, it is widely generated by slicers so RRF will continue to support it.

## M105: Get Extruder Temperature

### Parameters

* *This command can be used without any additional parameters.*
* **Rnnn** Response sequence number^1^
* **Snnn** Response type^1^

### Examples
<br>
<pre class="cblock">
M105
M105 S2
</pre>

Request the temperature of the current extruder and the build base in degrees Celsius. Reports the current and target temperatures of all active heaters.

### Notes

^1^ Returns a JSON-formatted response if parameter S2 or S3 is specified. Additionally, parameter Rnn may be provided, where nn is the sequence number of the most recent GCode response that the client has already received. M105 S2 is equivalent to M408 S0, and M105 S3 is equivalent to M408 S2. Usage of these forms of M105 is deprecated, please use M408 instead.

## M106: Fan On

### {.tabset}

#### RepRapFirmware 3.x

##### Parameters

* **Pnnn** Fan number (optional, defaults to 0). Relates to the fan number created by M950,
* **Snnn** Fan speed (0 to 255 or 0.0 to 1.0))
* **Lnnn** Set the minimum fan speed (0 to 255 or 0.0 to 1.0) when a non-zero fan speed is requested.
* **Xnnn** Set the maximum fan speed (0 to 255 or 0.0 to 1.0) when a non-zero fan speed is requested.
* **Bnnn** Blip time - fan will be run at full PWM for this number of seconds when started from standstill. Default is B0.1 which means that there is a 100ms burst after starting the fan.
* **Hnn:nn:nn...** Enable thermostatic mode and select sensor monitored. H-1 disables thermostatic mode. Relates to the sensor number(s) created by M308.
* **Rnnn** Restore fan speed to the value it has when the print was paused (R1) or the last time the fan speed was set and no P parameter was provided (R2).
* **Tnnn** or **Tnn:nn** Set thermostatic mode trigger temperature, or temperature control range
* **C"name"** Set custom name for this fan (supported in RRF >= 2.01)

##### Examples
<br>
<pre class="cblock">
M106 S127
M106 P1 T45 S0.7 H1:2 ; (RRF 3.3 and earlier, see notes)
M106 P1 T45 H1:2 X0.7 ; (RRF 3.4 and later, see notes)
M106 P1 T40:50 H1:2
</pre>

The first example turns on the default cooling fan at half speed. The second (RRF 3.3 and earlier) and third (RRF 3.4 and later) examples sets the second fan to a thermostatic fan for sensors 1 and 2 (e.g. the extruder heaters in a dual-nozzle machine) such that the fan will be on at 70% PWM when either hot end is at or above 45C. The third example also sets up a thermostatic fan, but this time it runs in proportional mode; fan speed scales from 0% to 100% as temperature rises from 40C to 50C. 
<br>
<pre class="cblock">
M308 S10 Y"mcu-temp" A"MCU"                      ; defines sensor 10 as MCU temperature sensor
M308 S11 Y"drivers" A"Duet stepper drivers"      ; defines sensor 11 as stepper driver temperature sensor
M308 S12 Y"drivers-duex" A"Duex stepper drivers" ; for Duet 2 WiFi/Ethernet with DueX2/5, defines sensor 12 as DueX2/5 stepper driver temps
M950 F2 C"fan2" Q100                             ; create fan 2 on pin fan2 and set its frequency
M106 P2 H10:11:12 T40:70                         ; set fan 2 value
</pre>

This example sets up an electronics cooling fan that starts to turn on when the MCU temperature reaches 45C and reaches full speed when the MCU temperature reaches 70C or if any TMC2660 drivers report that they are over-temperature. The sensors are defined with M308, the fan with M950, then the fan is configured with M106. See [Configuring the on-board MCU and stepper driver temperature sensors](/User_manual/Connecting_hardware/Temperature_configuring_mcu_temp#firmware-configuration){target=_blank} for further guidance.

##### RepRapFirmware 3 Notes

The A (logical pin number), F (fan PWM frequency) and I (invert pwm) parameters are no longer supported. Instead, specify the corresponding parameters in the M950 command when you create the fan.

The P parameter relates to the fan number created by M950, NOT the fan pin number on the board as in RRF2.x.

The H parameter relates to the sensor number(s) created by M308, not the temperature sensor pin number on the board as in RRF2.x.

From RRF 3.4, when a fan is configured as thermostatic using M106, the S parameter is now ignored. If a single T value is given, then when the temperature is above the T parameter the fan will run at the PWM specified by the X (maximum PWM) parameter (default 1.0). In RRF 3.3 and earlier, the fan will run at the PWM specified by the S parameter. 

If a fan is configured to trigger on a sensor that represents a stepper driver over-temperature flags (ie M308 ... Y'drivers'), then when the fan turns on it will delay the reporting of an over-temperature warning for the corresponding drivers for a few seconds, to give the fan time to cool the driver down.

If you were using the PB6 tacho input on Duet 2 WiFi/Ethernet running RRF 2.x, you must declare this in a M950 command for the fan concerned in RRF 3.x.

Example
<br>
<pre class="cblock">
; RRF 2.x code
M106 P2 I1 F25000  ; fan 2 is a 4-wire PWM fan so invert it and use high PWM frequency. tacho connected to PB6 on expansion connector. PB6 is defined by default.

; RRF 3.x code
M950 F2 C"!Fan2+exp.pb6" Q25000  ; fan 2 is a 4-wire PWM fan so invert it, use high PWM frequency, tacho connected to PB6 on expansion connector
</pre>

#### RepRapFirmware 2.x

##### Parameters

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

##### Examples
<br>
<pre class="cblock">
M106 S127
M106 P1 T45 S0.5 H1:2
M106 P1 T40:50 H1:2
M106 P2 T45:65 H100:101:102
</pre>

The first example turns on the default cooling fan at half speed. The second example sets the second fan to a thermostatic fan for heaters 1 and 2 (e.g. the extruder heaters in a dual-nozzle machine) such that the fan will be on at 70% PWM when either hot end is at or above 45C. The third example also sets up a thermostatic fan, but this time it runs in proportional mode. The fourth example sets up an electronics cooling fan that starts to turn on when the MCU temperature (virtual heater 100) reaches 45C and reaches full speed when the MCU temperature reaches 65C or if any TMC2660 drivers (virtual heaters 101 and 102) report that they are over-temperature

##### RepRapFirmware 2 Notes

The F parameter sets the fan PWM frequency, in Hz. The default is F250, which works with most fans, try F100 or lower if you find that you can't control the speed of your fan. This parameter is ignored for fans connected to the fan outputs of a DueX2 or DueX5 because those outputs don't support variable PWM frequency.

The I parameter causes the fan output signal to be inverted if its value is greater than zero. This makes the cooling fan output suitable for feeding the PWM input of a 4-wire fan via a diode. If the parameter is present and zero, the output is not inverted. If the I parameter is negative then in RRF 1.16 and later the fan is disabled, which frees up the pin for use as a general purpose I/O pin that can be controlled using M42.

The A parameter can be used to assign a fan to a different output pin, for example a spare heater output (use a M307 command to disable the heater and free up the pin first). 

In firmware versions 1.19 and later, fans can respond to virtual heaters (which have heater numbers 100 upwards) as well as real heaters. If a fan is configured to trigger on a virtual heater whose sensor represents TMC2660 driver over-temperature flags, then when the fan turns on it will delay the reporting of an over-temperature warning for the corresponding drivers for a few seconds, to give the fan time to cool the driver down.

### Notes - all firmware versions

The parameter 'S' declares the PWM value (0-255 or 0-1.0). M106 S0 turns the fan off.

If an S parameter is provided but no other parameter is present, then the speeds of the print cooling fans associated with the current tool will be set (see the F parameter in the M563 command). If no tool is active then the speed of Fan 0 will be set. Either way, the speed is remembered so that it can be recalled using the R2 parameter (see below).

If no S parameter is given but the R1 parameter is used, the fan speed when the print was last paused will be set, this allows the pause.g macro to switch off the fans and have them resume when the print is resumed. If the R2 parameter is used, then the speeds of the print cooling fans associated with the current tool will be set to the remembered value (see above). R2 allows the configured fan speed to be passed between tools which is useful on multi extruder printers where the slicing software may not specify the fan speed on tool change and the tool change macros shut down fans when not in use.

The T and H parameters allow a fan to be configured to operate in thermostatic mode, for example to use one of the fan channels to control the hot end fan. In this mode the fan will be on when the temperature of any of the heaters listed in the H parameter is at or above the trigger temperature set by the T parameter, and off otherwise. Thermostatic mode can be disabled using parameter H-1. In RRF 3.4 and later, the fan will run at the PWM specified by the X (maximum PWM) parameter (default 1.0). In RRF 3.3 and earlier, the fan will run at the PWM specified by the S parameter.

In firmware 1.19 and later, the T parameter may be of the form Taaa:bbb where aaa is the temperature at/below which the fan should be fully off and bbb is the temperature at which the fan should be fully on. The PWM will be set proportionally if the temperature is between these limits.

The B parameter sets the time for which the fan will be operated at full PWM when started from cold, to allow low fan speeds t be used. A value of 0.1 seconds is usually sufficient.

The L parameter defines the minimum PWM value that is usable with this fan. If a lower value is commanded that is not zero, it will be rounded up to this value.

The X parameter defines the maximum PWM value that is usable with this fan. The requested PWM value (S parameter) is scaled to be between 0 and X parameter value, and rounded up to the minimum if defined (L parameter). e.g. if X128 is set, S255 will set PWM to 128; S128 will set PWM to 64.

## M107: Fan Off

Deprecated in RepRapFirmware. Use M106 S0 instead.

*Note that M107 will turn off the print cooling fan(s) associated with the current tool (see the F parameter in the M563 command).*

## M108: Cancel Heating

Breaks out of an M109, M116, M190 or M191 wait-for-temperature loop, continuing the print job. Use this command with caution! If cold extrusion prevention is enabled (see M302) and the extruder temperature is too low, this will start "printing" without extrusion. If cold extrusion prevention is disabled and the hot-end temperature is too low, the extruder may jam.

## M109: Set Extruder Temperature and Wait

### Parameters

* **Snnn** minimum target temperature, waits until within 2.5C of target if heating
* **Rnnn** accurate target temperature, waits until within 2.5C of target always
* **Tn** optional tool number

### Examples
<br>
<pre class="cblock">
M109 S215
</pre>

Deprecated in RRF, but widely generated by slicers. To get the same behaviour, use G10/M568 followed by M116

To better support slicers that don't select a tool when doing a single-extrusion print, RRF will always select a tool when processing M109. If the T parameter is present, that tool will be selected. If no T parameter is present and no tool is already selected, the tool with the lowest non-negative tool number will be selected (typically tool 0).

*Note: M109 will not wait for temperatures below 40c because in many cases they may never be reached due to ambient temps. So if you want to wait for a hotend to cool, use 41c or higher.*

## M110: Set Current Line Number

### Parameters

* **Nnnn** Line number

### Examples
<br>
<pre class="cblock">
M110 N123
</pre>

This example sets the current line number to 123. Thus the expected next line after this command will be 124.

## M111: Set Debug Level

### Parameters

* **Pnn** Debug module number
* **Sn** Debug on (S1), off (S0). S0 is equivalent to D0. S1 is equivalent to D{0xFFFF}.
* **Dnnn** Set/clear individual debug flags for the specified module

### Examples
<br>
<pre class="cblock">
M111
M111 P1 S1 ; enable debugging for module 1
</pre>

Enable or disable debugging features for the module number specified by the P parameter. M111 without parameters lists all the modules, their numbers, and whether debugging is enabled for each..

Note, print quality may be affected when debug output is enabled because of the volume of data sent to USB. Debug output should normally be used only for debugging firmware, or when instructed to help with diagnosis of particular issues.

The details of what debugging information is output when debugging is enabled varies from one firmware revision to another, so it is not specified here.

## M112: Emergency Stop

### Examples
<br>
<pre class="cblock">
M112
</pre>

Any moves in progress are immediately terminated, then Duet shuts down. All motors and heaters are turned off. PSU power (if controlled by the Duet via the PS_ON pin) is NOT turned off, to allow any always-on fans to continue to run. The Duet can be started again by pressing the reset button or power cycling the board. See also [M0 - stop or unconditional stop](/User_manual/Reference/Gcodes/M0){target=_blank} and [M1 - sleep or conditional stop](/User_manual/Reference/Gcodes/M1){target=_blank}. To turn off PSU power via the PS_ON pin in an emergency stop, make a trigger macro that includes M81 before M112; see [User manual: Connecting an emergency stop](/User_manual/Connecting_hardware/IO_E_stop){target=_blank}.

## M114: Get Current Position

### Examples
<br>
<pre class="cblock">
M114
</pre>

This reports the X, Y, Z (,U, V, W, A, B, C if configured) and E coordinates to the host. The coordinates reported are those at the end of the last completed move.

For example, the machine returns a string such as:
<br>
<pre class="cblock">
C: X:10.000 Y:20.000 Z:5.000 E:51.000 E0:51.0 E1:0.0 Count 800 1600 2000 Machine 10.00 20.00 5.00 Bed comp 0.00
</pre>

The first E value (without an extruder number) is the current virtual extruder position, and is included for the benefit of GCode senders that don't understand multiple extruders. The E0, E1 etc. values are for each individual extruder. The Count values are the microstep positions of each individual motor or motor group. The Machine values are the machine axis coordinates, which are calculated from the user coordinates by accounting for workplace coordinate offsets, bed compensation, axis skew compensation, babystepping and Z lift.

Note: there is no agreed definition of what the response to M114 should be. We try to keep the M114 response compatible with other firmwares as far as we can, but this is not always possible. Users writing applications which need to fetch current positions from RepRapFirmware 3 are recommended to use object model queries instead.

## M115: Get Firmware Version and Capabilities

### Parameters

* *This command can be used without any additional parameters.*
* **Pnnn** Electronics type (See Notes)
* **Bnnn** Board number (RRF3/Duet 3 only, see Notes)

### Examples
<br>
<pre class="cblock">
M115
M115 P2
</pre>

Request the Firmware Version and Capabilities of the current microcontroller.

The details are returned to the host computer as key:value pairs separated by spaces and terminated with a linefeed.:
<br>
<pre class="cblock">
M115
FIRMWARE_NAME: RepRapFirmware for Duet 2 WiFi/Ethernet FIRMWARE_VERSION: 2.04RC1 ELECTRONICS: Duet WiFi 1.0 or 1.01 FIRMWARE_DATE: 2019-07-14b1
</pre>

See the M408 command for a more comprehensive report on machine capabilities.

### Notes

The 'P' parameter is used tell the firmware about the hardware on which it is running, if RRF can't identify it. Should only be used in config.g, if you're having problems. If the P parameter is present then the integer argument specifies the hardware being used. The following are supported on first-generation Duets:

* M115 P0 - Automatic board type selection if supported, or default if not
* M115 P1 - Duet 0.6
* M115 P2 - Duet 0.7
* M115 P3 - Duet 0.85

The 'B' parameter is used on Duet 3 only, using RRF3. M115 can take an optional B (board number) parameter which is the CAN address of the board to be queried, default 0 (i.e. main board). Example:
<br>
<pre class="cblock">
M115 B1
Board EXP3HC firmware 3.0beta1 2019-10-28b1
</pre>

## M116: Wait

### Parameters

* *This command can be used without any additional parameters.*
* **Pnnn** Tool number
* **Hnnn** Heater number
* **Cnnn** Chamber number
* **Snn** Tolerance in degC (firmware 2.02/1.23 and later, default 2)

### Examples
<br>
<pre class="cblock">
M116
M116 P1
M116 H0 S5
</pre>

The first example waits for *all* bed, chamber, and tool heaters to arrive at their set values. Note that in v3.5, the scope of tool heaters to wait for is limited to the heaters of the currently selected tool of the selected motion system.

The second shows the optional 'P' parameter that is used to specify a tool number. If this parameter is present, then the system only waits for temperatures associated with that tool to arrive at their set values. This is useful during tool changes, to wait for the new tool to heat up without necessarily waiting for the old one to cool down fully.

The third example waits for the bed to reach its temperature +-5 degC.

See also M109.

Recent versions of RepRapFirmware also allow a list of the heaters to be specified using the 'H' parameter, and if the 'C' parameter is present, this will indicate that the chamber heater should be waited for.

'S' parameter sets the temperature tolerance, default 2 degC.

*Note: M116 will not wait for temperatures below 40c because in many cases they may never be reached due to ambient temps. So if you want to wait for a hotend or bed to cool, use 41c or higher.*

## M117: Display Message

### Examples
<br>
<pre class="cblock">
M117 Hello World
M117 "Hello world"
</pre>

This causes the given message to be shown in the status line on an attached LCD or if no LCD is attached, this message will be reported on the web interface. Quotation marks around the string to be displayed are recommended but not mandatory.

RRF >= 3.2.0-beta3: All messages sent via M117 will be logged with log level INFO if logging is enabled at least at log level INFO.

*Note: Due to the way M117 messages are communicated, messages sent in quick succession may not all display. Use M118 for those cases.*

## M118: Send Message to Specific Target

Supported in RepRapFirmware 1.21 and later.

### Parameters

* **Pnnn** Message type (0 = Generic [default], 1 = USB, 2 = PanelDue/UART, 3 = HTTP, 4 = Telnet, 5 = second UART, 6 = MQTT Client [RRF 3.5beta2 and later only]) (optional)
* **S"msg"** Message to send , limit of 100 characters
* **Lnnn** Log level of this message (0 = do not log this line, 1 = log as WARN, 2 = log as INFO, 3 = log as DEBUG (default)) (RRF >= 3.2.0-beta3)
* **T"topic"** The topic to publish the message under (only valid on MQTT Client message).
* **Qnn** The QOS level of the message to publish, from `0` to `2` (only valid for MQTT Client message, optional). Defaults to `0` if not specified.
* **Rn** Set publish retain flag, `1` or `0`  (only valid for MQTT Client message, optional). Defaults to `0` if not specified.
* **Dn** Set publish duplicate flag, `1` or `0` (only valid for MQTT Client message, optional). Defaults to `0` if not specified.

### Examples
<br>
<pre class="cblock">
M118 S"Hello Duet"
M118 S"Hello Logfile" L1
M118 P0 S"Hello Logfile and DWC" L1
M118 S"Don't log me" L0
M118 S"My MQTT Message" T"My-MQTT-Topic"
</pre>

This code may be used to send messages to a specific target. Basically it is a simple wrapper for RepRapFirmware's Platform::Message method.

Note that the implementation in RepRapFirmware always requires the S-parameter to be passed. If it is omitted, an error will be reported.

The second example shows how to send a message to the log file in log level WARN instead of the default log level DEBUG.

The third example shows how to prevent a message from being logged.

Note that a message only having the **Lnnn** parameter but no **Pnnn** parameter will only go to the log file (if the log level matches the current log level of the system) and will not be sent to other outputs. If you want it to show on DWC as well then send:
<br>
<pre class="cblock">
M118 P0 S"message" L1
</pre>

**Note**: messages that exceed **100 characters** will be truncated.

## M119: Get Endstop Status

### Examples
<br>
<pre class="cblock">
M119
</pre>

Returns the current state of the configured endstops. Takes into account any 'inverted endstop' settings, so one can confirm that the machine is interpreting the endstops correctly.

## M120: Push

### Examples
<br>
<pre class="cblock">
M120
</pre>

Push the state of the machine onto a stack. Called automatically when a macro file is run. It pushes the following values on the stack:

1. Current feedrate
1. Extruder positions
1. Whether moves (and separately extrusion) are relative or absolute

## M121: Pop

### Examples
<br>
<pre class="cblock">
M121
</pre>

Recover the last state pushed onto the stack.

## M122: Diagnose

### Parameters

* *This command can be used without any additional parameters.*
* **Pnnn** report specific information (See Notes)
* **Bnnn** Board number (RRF3/Duet 3 only, see Notes)
* **"DSF"** Immediate DSF diagnostics (RRF3/Duet3 only with attached SBC)

### Examples
<br>
<pre class="cblock">
M122
</pre>

Sending an M122 causes the RepRap to transmit diagnostic information.

### Notes

The 'P' parameter is used to report specific information. The details vary between releases. As at RepRapFirmware version 3.41 they are:

* P1 print summary test report. Parameters: 
  * required parameters: **Taa:bb** = min/max accepted MCU temperature reading, **Vaa:bb** = min/max VIN voltage reading 
  * optional parameter: **Waa:bb** = min/max 12V regulator voltage reading if applicable (required if the board monitors the 12V rail)
  * optional parameter: **Faa:bb** = min/max inductive sensor frequency in kHz (required if the board has an inductive sensor chip)
  * NOTE: M122 P1 subfunction is provided for factory testing purposes only, so the details are liable to be changed without notice
* P100 print a summary of recent moves (only if move logging is enabled in the firmware build)
* P101 print the status of an attached DueX expansion board (Duet 2 only)
* P102 print how long it takes to evaluate the square root of a 62-bit unsigned integer
* P103 print how long it takes to evaluate sine and cosine
* P104 print how long it takes to write a file to the SD card (specify the file size in Mbytes in the S parameter, default 10)
* P105 print the sizes of various objects used by RepRapFirmware
* P106 print the addresses of various objects used by RepRapFirmware
* P107 time a CRC-32 operation
* P108 time how long it takes to read the step clock
* P109 generate an under-voltage event to test the pause-on-low-power function

The following 'P' parameters are supported by the **LPC and STM32 Port  of RepRapFirmware Only**

* P200 - Lists all of the pins allocated by the firmware and/or board.txt

The following P parameter is supported on boards that use Arm Cortex M4 processors:

* P500 Sn - n=0 disables the processor write buffer; n=1 enables it which is the default at power up. If the S parameter is missing then the current enable/disable state is reported. Disabling the write buffer reduces performance, but can help when debugging if the processor resets and the M122 reset data indicates that the cause was an imprecise exception. Disabling the write buffer will usually make the exception precise in future.

Note: do not use M122 with a P parameter of 1000 or greater. Most of these values are used to test the error reporting facilities and deliberately cause the firmware to crash . As at firmware 3.45 these are:

* P1001 cause a watchdog reset
* P1002 test what happens when a module gets stuck in a spin loop
* P1003 test what happens when we write a blocking message to USB
* P1004 test integer division by zero
* P1005 test the response to an unaligned memory access
* P1006 test the response to accessing an invalid region of memory
* P1007 read/write 32-bit words: A = address, R = number of 32-bit words (optional, default 1), V = value to write (optional)

The 'B' parameter is used in RepRapFirmware 3 on Duet 3 only, to report diagnostic information from connected boards. The B (board number) parameter is the CAN address of the board to be queried, default 0 (i.e. main board). Example:
<br>
<pre class="cblock">
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
</pre>

## M135: Set PID sample interval

*NOTE: removed in RRF 2.03beta2*

### Parameters

* **Snnn** Heat sample time in milliseconds

### Examples
<br>
<pre class="cblock">
M135 S300
</pre>

Set the PID to measure temperatures and calculate the power to send to the heaters every 300ms.

## M140: Set Bed Temperature (Fast) or Configure Bed Heater

### Parameters

* **Pnnn** (RRF 1.20 and later) Bed heater slot, default 0
* **Hnnn** Heater number
* **Snnn** Active/Target temperature
* **Rnnn** Standby temperature

### Order dependency

In RRF3 a M140 command with H parameter (other than H-1) must come after the M950 command that creates that heater, and before any M143 command that sets a temperature limit for that heater.

### Examples
<br>
<pre class="cblock">
M140 H0      ; bed heater 0 uses heater 0
M140 P1 H1   ; bed heater 1 uses heater 1
M140 H-1     ; disable bed heater
M140 S55     ; set bed temperature to 55C
M140 S65 R40 ; set bed temperature to 65C and bed standby temperature to 40C
M140 S-273.1 ; switch off bed heater
</pre>

The first example informs the firmware that bed heater 0 (implied, because no P parameter is provided) uses heater 0.
The second example informs the firmware that bed heater 1 (P1) uses heater 1.
The third example disables the bed heater. No bed heaters will be shown in DWC.
The fourth example sets the temperature of the bed heater to 55^o^C and returns control to the host immediately (*i.e.* before that temperature has been reached by the bed).
The fifth example sets the bed temperature to 65^o^C and the bed standby temperature to 40^o^C.
The sixth example sets the active/target bed temperature to absolute negative temperature (-273 or lower). This switches off the bed heater.

### Notes

* If a temperature close to absolute zero is set (strictly less than -273°C in RRF 3.3 and earlier, less than or equal to -273°C in RRF 3.4.0 and later), the bed heater will be switched off.
* The 'H' parameter sets the heated bed heater number(s). If no heated bed is present, a negative value may be specified to disable it. M140 commands with H parameters would normally be used only in the config.g file.
* On the Duet 3 MB6HC and MB6XD you can configure up to 12 bed heaters; on Duet 3 Mini 5+, 2 bed heaters; on Duet 2 WiFi/Ethernet, 4 bed heaters; on Duet 2 Maestro, 2 bed heaters.

## M141: Set Chamber Temperature (Fast) or Configure Chamber Heater

### Parameters

* **Pnnn** (RRF 2.03 and later only) Chamber heater slot, default 0
* **Hnnn** Heater number
* **Snnn** Active/Target temperature
* **Rnnn** Standby temperature

### Order dependency

In RRF3 a M141 command with H parameter (other than H-1) must come after the M950 command that creates that heater, and before any M143 command that sets a temperature limit for that heater.

### Examples
<br>
<pre class="cblock">
M141 S30
M141 H3
M141 P1 H4
</pre>

The first example sets the temperature of the chamber to 30^o^C and return control to the host immediately (*i.e.* before that temperature has been reached by the chamber).
The second example specifies that chamber heater 0 (the default if no P parameter is given) uses heater 3.
The third example specifies that chamber heater 1 uses heater 4.

### Notes

* M141 commands with H parameters would normally be used only in the config.g file.
* On the Duet 3 MB6HC and MB6XD you can configure up to 4 chamber heaters; on Duet 3 Mini 5+, 2 chamber heaters; on Duet 2 WiFi/Ethernet, 4 chamber heaters; on Duet 2 Maestro, 2 chamber heaters.

## M143: Maximum heater temperature

### {.tabset}

#### M143 in RRF 3.01 and later

##### Parameters

* **H** Heater number to monitor (default 1 which is normally the first hot end)
* **S** Maximum permitted temperature
* **P** Heater monitor number, default 0
* **T** Sensor number used to monitor the heater. It defaults to the sensor that controls the heater (as was specified in the M308 command when the heater was created).
* **A** Action to trigger (0: Generate heater fault [default] 1: Switch off permanently 2: Switch off temporarily 3: Shut down the printer)^1^
* **C** Condition for temperature event (0: Temperature too high [default] 1: Temperature too low, -1: Monitor is disabled)

Each heater supports a certain number (3 in most builds of RRF) of monitors for that heater. The P parameter allows you to choose which monitor to configure.

By default, monitor 0 is set up to generate a heater fault if a temperature limit is exceeded according to the sensor that controls the heater, and monitors 1 and 2 are disabled.

##### Examples
<br>
<pre class="cblock">
M143 H1 P0 S275 A2 ; switch off heater 1 temporarily if it exceeds 275°C
M143 H1 P1 S285 A0 ; raise a heater fault if it exceeds 285C
</pre>

#### M143 in RRF 3.0 and earlier

##### Parameters

* **H** Heater number to turn off if an anomaly is detected (default 1 which is normally the first hot end). This must be a real heater.
* **S** Maximum permitted temperature
* **P** Heater protection instance (defaults to H parameter if omitted)^1^
* **X** Heater number whose temperature sensor is used to monitor the heater specified in the H parameter. This can be a virtual heater. It default to the value of the H parameter.^1^
* **A** Action to trigger (0: Generate heater fault [default] 1: Switch off permanently 2: Switch off temporarily)^1^
* **C** Condition for temperature event (0: Temperature too high [default] 1: Temperature too low)^1^

##### Examples
<br>
<pre class="cblock">
M143 S275 ; set the maximum temperature of the hot-end to 275°C
M143 H0 S125 ; set the maximum bed temperature to 125C
M143 H1 S275 X103 ; use virtual heater 103 to monitor heater 1
</pre>

You have heater 3 mapped to a chamber heater which is supposed to be temporarily turned off when the temperature in it exceeds 65C. The thermistor for the chamber is set up as a virtual heater on channel 104 and can be viewed on the "Extra" panel on DWC (refer to M305 for further details on how to set this up). To achieve this you can configure an extra heater protection with the following GCode:
<br>
<pre class="cblock">
M143 P100 H3 X104 A2 C0 S65
</pre>

##### RepRapFirmware 2.x notes

To control heaters via two thermistors, RepRapFirmware allows the configuration of extra heater protection elements whose index starts from 100. On the Duet 2 Wifi and Duet 2 Ethernet there are 8 extra heater protections available (100-107). See also [M305](/User_manual/Reference/Gcodes/M305){target=_blank}.

### Order dependency

If the heater is a bed or chamber heater then the M143 command must come after the M140 or M141 command that declares the heater as a bed or chamber heater.

### Notes

^1^ Supported in RepRapFirmware 1.20 and later. Starting from this version RepRapFirmware allows more granular control over the heater subsystem. By default each heater has one heater protection instance assigned to it, which is by default configured to generate a heater fault if the maximum heater temperature is exceeded.

The default maximum temperature for all heaters was 300°C prior to RepRapFirmware version 1.13, and 262°C from 1.13. At RepRapFirmware 1.17 the default maximum temperatures were 262C for extruders and 125C for the bed. In more recent versions the default maximum heater temperature is 290C, to allow the hot end to be tightened at 285C as per the E3D recommendation. When the temperature of the heater exceeds this value a heater error will be triggered.

With A0 set on RepRapFirmware 1.26.1, PS_ON is triggered after the fault has exisited for the duration defined by the S parameter set in M570.

## M144: Bed Standby

### Parameters

* **Pnnn** Bed heater slot (supported in RepRapFirmware 1.20 and later)
* **Sn** 0 = put bed heater on standby (default), 1 = make bed heater active

### Examples
<br>
<pre class="cblock">
M144
</pre>

Switch the bed to its standby temperature. M144 S1 will set it back to its active temperature.

## M150: Set LED colours

### Tabs {.tabset}

#### RRF 3.5beta4 and later

##### Parameters

* **Rnnn** Red component, 0-255
* **Unnn** Green component, 0-255
* **Bnnn** Blue component, 0-255
* **Wnnn** White component, 0-255 (Only for RGBW NeoPixel)
* **Pnnn** Brightness, 0-255
* **Ynn** Brightness, 0-31 (alternative to P 0-255)
* **Snnn** Number of individual LEDs to set to these colours, default 1
* **Fn** Following command action. F0 (default) means this is the last command for the LED strip, so the next M150 command starts at the beginning of the strip. F1 means further M150 commands for the remainder of the strip follow this one.
* **En** LED strip number, default 0. See [M950](/User_manual/Reference/Gcodes/M950) for defining the LED strip number, pin and LED type.

##### Examples
<br>
<pre class="cblock">
M950 E0 C"led" T1 Q3000000   ; create a RGB Neopixel LED strip on the LED port and set SPI frequency to 3MHz<br>
M150 E0 R255 P128 S20 F1     ; set first 20 LEDs to red, half brightness, more commands for the strip follow
M150 E0 U255 B255 P255 S20   ; set next 20 LEDs to cyan, full brightness, finished programming strip
</pre>

Оn **Fysetc 12864mini display** you can set all three LEDs separately. For display configuration and encoder illumination:

<br>
<pre class="cblock">
M918 P2 E-4 F2000000           ; Fysetc 12864mini
M950 E0 C"io3.out" T1 U3       ; create a RGB Neopixel LED strip with 3 LEDs on the Duet 3 Mini 5+ 12864_EXP1 header<br>
M150 E0 R0 U0 B255 P255 S1 F1  ; display led blue
M150 E0 R255 U0 B0 P255 S1 F1  ; left encoder led red
M150 E0 R0 U255 B0 P255 S1 F0  ; right encoder led green
</pre>

#### RRF 3.4 and earlier

##### Parameters

* **Rnnn** Red component, 0-255
* **Unnn** Green component, 0-255
* **Bnnn** Blue component, 0-255
* **Wnnn** White component, 0-255 (Only for RGBW NeoPixel, RepRapFirmware 3.3 and later)
* **Pnnn** Brightness, 0-255 (RepRapFirmware 2.03 and later)
* **Ynn** Brightness, 0-31 (alternative to P 0-255)
* **Snnn** Number of individual LEDs to set to these colours, default 1
* **Fn** Following command action. F0 (default) means this is the last command for the LED strip, so the next M150 command starts at the beginning of the strip. F1 means further M150 commands for the remainder of the strip follow this one.
* **Xn** LED type: X0 = DotStar (default prior to RRF 3.2), X1 = RGB NeoPixel (default in RRF 3.2 and later), X2 = bit-banged RGB NeoPixel, X3 = RGBW NeoPixel (from RRF 3.3), X4 = bit-banged RGBW NeoPixel (from RRF3). This parameter is remembered from one call to the next, so it only needs to be given once. Not all boards support all the modes. On the Duet 3 Mini, X1 and X3 select the NeoPixel output on the main board, while X2 and X4 address the RGB LEDs on some 12864 displays.
* **Qnnn** (optional) Use specified SPI frequency (in Hz) instead of the default frequency. This parameter is not normally needed, and is only processed if X parameter also present. When using NeoPixels, only frequencies in the range 2.4 to 4MHz will work.

##### Examples
<br>
<pre class="cblock">
M150 X1 Q3000000          ; set LED type to NeoPixel and set SPI frequency to 3MHz
M150 R255 P128 S20 F1     ; set first 20 LEDs to red, half brightness, more commands for the strip follow
M150 U255 B255 P255 S20   ; set next 20 LEDs to cyan, full brightness, finished programming strip
</pre>

Оn **Fysetc 12864mini display** you can set all three LEDs separately. For display configuration and encoder illumination:

<br>
<pre class="cblock">
M918 P2 E-4 F2000000           ; Fysetc 12864mini<br>
M150 X2 R255 U0 B0 P255 S1 F1  ; display led
M150 X2 R0 U255 B0 P255 S1 F1  ; left encoder led
M150 X2 R0 U255 B0 P255 S1 F0  ; right encoder led
</pre>

### Notes

* In older firmware versions this command is only supported on controllers that have an output connector for DotStar or NeoPixel LEDs. More recent firmwares remove this limitation.
* The specified RGB values will be sent to the number of LEDs in the LED strip as specified by the S parameter, pushing the existing colours along the strip. To set all the LEDs the same colour, make the S parameter equal to or a little longer than the number of LEDs in the strip.
* Caution: in early firmware versions, if the S parameter is omitted then as many LEDs as can be set in a single chunk will be addressed which depends on the board (e.g. 60 RGBW neopixels on Duet2). We recommend users always explicitly set the number of LEDs to address, rather than rely on this behaviour as the number of LEDs addressed in a single chunk may change in the future.
* If a Neopixel LED strip is assigned to a pin that that cannot generate the WS2812 LED timing in hardware, then motion will be suspended while the LED strip is being written.
* Some Neopixel/WS2812 versions have the colour order as RGB and others are GRB. Check the datasheet for the LEDs you are using if the Red and Green colours are switched. If this is the case then you will have to set the red with the U parameter and green with the R parameter.

See also [Neopixel and Dotstar LEDs](/User_manual/Connecting_hardware/IO_Neopixel_DotStar) and [Connecting 12864 or other displays](/User_manual/Connecting_hardware/Display_12864).

## M190: Wait for bed temperature to reach target temp

### Parameters

* **Snnn** minimum target temperature, waits while heating
* **Pnnn** Bed heater slot (supported in RepRapFirmware 1.20 and later)
* **Rnnn** accurate target temperature, waits while heating and cooling

### Examples
<br>
<pre class="cblock">
M190 S60
</pre>

Set the temperature of the bed to 60C and wait for the temperature to be reached.

*Note: M190 will not wait for temperatures below 40c because in many cases they may never be reached due to ambient temps. So if you want to wait for a bed to cool, use 41c or higher.*

## M191: Wait for chamber temperature to reach target temp

### Parameters

* **Snnn** minimum target temperature, waits while heating
* **Rnnn** accurate target temperature, waits while heating and cooling
* **Pnnn** Chamber slot. This defaults to 0 and the maximum is dependent on the board type. (Supported in RepRapFirmware 1.20 and later)

### Examples
<br>
<pre class="cblock">
M191 S60
</pre>

Set the temperature of the build chamber to 60C and wait for the temperature to be reached.

## M200: Volumetric extrusion

*Supported in RRF 1.19 and later*

### Parameters

* **Daaa:bbb:ccc...** Sets filament diameter to aaa for extruder 0, bbb for extruder 1 and so on. If any of aaa, bbb etc. are zero then volumetric extrusion is disabled for that extruder.
* **Daaa** Sets filament diameter (or disables volumetric extrusion) for all extruders
* **S[bool]** Enable or disable volumetric extrusion for this input channel (RepRapFirmware 3.5 and later)

### Examples
<br>
<pre class="cblock">
M200 D0             ; disable volumetric extrusion on all extruders
M200 S0             ; disable volumetric extrusion for this input channel (RRF 3.5 and later)
M200 D1.75          ; set all extruder filament diameters to 1.75mm
M200 D1.75:3.0:1.75 ; set extruder 0 to 1.75mm, extruder 1 to 3.0mm and all remaining extruders to 1.75mm
</pre>

### Description

Volumetric extrusion is an option you can set in some slicers whereby all extrusion amounts are specified in mm^3^ (cubic millimetres) of filament instead of mm of filament. This makes the GCode independent of the filament diameter, potentially allowing the same GCode to run on different printers. The purpose of the M200 command is to inform the firmware that the GCode input files have been sliced for volumetric extrusion, and to provide the filament diameter so that the firmware can adjust the requested extrusion amount accordingly.

### Notes

* Sending M200 without parameters reports the current volumetric extrusion state and (where appropriate) filament diameter for each extruder.
* To set filament diameter without enabling volumetric extrusion, use M404.
* Note that if you use slicer-commanded retraction, the retraction amounts must be specified in mm^3^ too. If instead you use firmware retraction, then the firmware retraction amounts specified using the M207 command are still interpreted as mm.
* RRF maintains a flag for the volumetric extrusion state. As such, the following is true:
  * Each input channel (SD card, USB, http, telnet etc) has its own flag for the volumetric extrusion state.
  * At the end of running config.g at startup, the flag state is copied to all input channels. If no volumetric extrusion is specified in config.g, the default (disabled) is used.
  * The flag state is saved when a macro starts and is restored when a macro ends.



## M201: Set max acceleration

### Parameters

* **Xnnn** Acceleration for X axis
* **Ynnn** Acceleration for Y axis
* **Znnn** Acceleration for Z axis
* **Unnn** Acceleration for U axis
* **Vnnn** Acceleration for V axis
* **Wnnn** Acceleration for W axis
* **Ennn** Acceleration for extruder drives

### Order dependency

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

### Examples
<br>
<pre class="cblock">
M201 X1000 Y1000 Z100 E2000
</pre>

### Notes

Sets the acceleration that axes can do in mm/second^2 for print moves. For consistency with the rest of GCode movement this should be in mm/(minute^2), but that gives really silly numbers and one can get lost in all the zeros. So for this we use seconds.

To calculate the maximum acceleration values for an axis an online [Maximum Acceleration Calculator](https://wilriker.github.io/maximum-acceleration-calculator){target=_blank} can be used.

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour.

Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M201 Z100, not M201 Z100:100

## M201.1: Set reduced acceleration for special move types

*Supported in RRF 3.4 and later*

### Parameters

* **Xnnn** Acceleration for X axis in units/s2
* **Ynnn** Acceleration for Y axis in units/s2
* **Znnn** Acceleration for Z axis in units/s2
* **Ennn:nnn...** Acceleration for the extruders in units/s2

### Examples
<br>
<pre class="cblock">
M201.1 X500 Y500 Z20 E500:500
</pre>

### Notes

Set the acceleration that axes should use for special types of move that should be done using reduced acceleration.

These values are used for probing moves (because some types of Z probe can be triggered by high acceleration at the start of the move) and for moves that involve stall detection endstops (because high acceleration can bring the motor close to stalling). If a single E value is provided, that value is applied to all extruders. The values must be provided in mm/sec^2^ even if G20 has been used to set units to inches. M201.1 without parameters reports the current settings.

## M203: Set maximum feedrate

### Parameters

* **Xnnn** Maximum feedrate for X axis
* **Ynnn** Maximum feedrate for Y axis
* **Znnn** Maximum feedrate for Z axis
* **Unnn** Maximum feedrate for U axis
* **Vnnn** Maximum feedrate for V axis
* **Wnnn** Maximum feedrate for W axis
* **Ennn:nnn...** Maximum feedrates for extruder drives
* **Innn** Minimum overall movement speed (firmware 2.03 and later), default 30mm/min

### Order dependency

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

### Examples
<br>
<pre class="cblock">
M203 X6000 Y6000 Z300 E10000
</pre>

### Notes

Sets the maximum feedrates that your machine can do in mm/min

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour.

Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M203 Z300, not M203 Z300,300

## M204: Set printing and travel accelerations

### Parameters

* **Pnnn** Acceleration for printing moves
* **Tnnn** Acceleration for travel moves

### Examples
<br>
<pre class="cblock">
M204 P500 T2000
</pre>

### Notes

Use M201 to set per-axis accelerations and extruder accelerations. RepRapFirmware applies the M204 accelerations to the move as a whole, and also applies the limits set by M201 to each axis and extruder.

Values are in mm/s².

This command is supported by firmware version 1.18RC1 and later.

## M205: Set max instantaneous speed change in mm/sec

* **Xnnn** X axis max instantaneous speed change in mm/sec
* **Ynnn** Y axis max instantaneous speed change in mm/sec
* **Znnn** Z axis max instantaneous speed change in mm/sec
* **Unnn, Vnnn, Wnnn etc.** U, V, W axis max instantaneous speed change in mm/sec

### Order dependency

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

### Notes

This command is provided as an alternative to [M566](/User_manual/Reference/Gcodes/M566){target=_blank} for compatibility with Marlin. In M566 the units are mm/min as with all other speeds. In M205 they are in mm/sec.

## M206: Offset axes

*This command is deprecated. Use G10 L2 P1 instead.*

### Parameters

* **Xnnn** X axis offset
* **Ynnn** Y axis offset
* **Znnn** Z axis offset
* **Unnn** U axis offset
* **Vnnn** V axis offset
* **Wnnn** W axis offset

### Order dependency

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

### Examples
<br>
<pre class="cblock">
M206 X10.0 Y10.0 Z-0.4
</pre>

The values specified will be subtracted from the coordinates given in G0, G1 and related commands. In firmware builds that support workplace coordinates, this command is equivalent to G10 P1 L2 with the X, Y, Z... parameters negated.

## M207: Set retract length

### Parameters

* **Pn** Tool number (optional, supported in RRF 3.01 and later only)
* **Snnn** positive length to retract, in mm
* **Rnnn** positive or negative additional length to un-retract, in mm, default zero
* **Fnnn** retraction feedrate, in mm/min
* **Tnnn** feedrate for un-retraction if different from retraction, mm/min (RepRapFirmware 1.16 and later only)
* **Znnn** additional zlift/hop

### Order dependency

The M207 command must come later in config.g than the [M563](/User_manual/Reference/Gcodes/M563){target=_blank} command that creates the tool to which it relates.

### Examples
<br>
<pre class="cblock">
M207 S4.0 F2400 Z0.075
</pre>

Sets the retract length used by the G10 and G11 firmware retraction and reprime commands. In RRF 3.01 and later, if a P parameter is provided then only the retraction parameters for the specified tool will be set. In other cases, the new retraction parameters will apply to all tools.

## M208: Set axis max travel

### Parameters

* **Snnn** 0 = set axis maximum (default), 1 = set axis minimum
* **Xnnn** X axis limit
* **Ynnn** Y axis limit
* **Znnn** Z axis limit

Alternative (from RepRapFirmware 2.02/1.23)

* **Xaaa:bbb** X axis minimum and maximum limit
* **Yaaa:bbb** Y axis minimum and maximum limit
* **Zaaa:bbb** Z axis minimum and maximum limit

### Order dependency

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

### Examples
<br>
<pre class="cblock">
M208 X200 Y200 Z90 ; set axis maxima
M208 X-5 Y0 Z0 S1 ; set axis minima
M208 X-5:200 Y0:200 Z0:90 ; set axis minima and maxima
</pre>

### Notes

The values specified set the software limits for axis travel in the specified direction. The axis limits you set are also the positions assumed when an endstop is triggered.

The min/max axis positions are +/- (2^31 - 1) microsteps. Position accuracy will start to suffer when the positions are outside approx. +/- 2^24 microsteps, because it is held and calculated as a 32-bit float. See also this note on [maximum length of moves](/User_manual/Reference/Gcodes#maximum-length-of-moves) in the G1 Gcode entry.

The M208 minimum Z value applies to deltas. The M208 XY min/max and Z max values don't.

## M220: Set speed factor override percentage

### Parameters

* **Snnn** Speed factor override percentage (0..100 or higher)

### Examples
<br>
<pre class="cblock">
M220 S80
</pre>

Sets the speed factor override percentage.

## M221: Set extrude factor override percentage

### Parameters

* **Snnn** Extrude factor override percentage (0..100 or higher), default 100%
* **Dnnn** Extruder drive number (RepRapFirmware only), default 0

### Examples
<br>
<pre class="cblock">
M221 S70
M221 S95 D1
</pre>

Sets extrude factor override percentage. In the case of RepRapFirmware, sets the extrusion factor percentage for the specified extruder drive only.

## M226: Synchronous Pause

### Examples
<br>
<pre class="cblock">
M226
</pre>

### Notes

Initiates a pause in the same way as if the pause button is pressed, except that execution of all prior GCode commands in the same input stream is completed first. Then the SD card input stream is paused and file sys/pause.g is run.

M226 is intended for use in the GCode file being printed, for example to pause after a particular layer has completed. So it waits until all the moves in the queue have been completed. M25 is intended for use from a different source of GCodes (such as the web interface console, PanelDue or a Macro), so if you need to pause from those use M25 instead.

## M260: i2c Send and/or request Data

Send and/or receive data over the i2c bus. Supported in RepRapFirmware 1.21 and later.

### Parameters

* **Ann** I2C address
* **Bnn:nn:nn...** Bytes to send (optional in firmware 2.02 and later)
* **Rnn** Number of bytes to receive (optional) - firmware 2.02 and later only

### Examples
<br>
<pre class="cblock">
M260 A5 B65                   ; Send 'A' to address 5 now
M260 A"x7F" B65               ; Send 'A' to address 7F (hex)
M260 A0 B82:101:112:82:97:112 ; Send 'RepRap' to address 0
M260 A"x71" B19 R2            ; send 19 to address 71 (hex) and read 2 bytes back
</pre>

Hex addresses are only supported in firmware 2.02 and later.

## M261: i2c Request Data

Deprecated in RRF 2.02 and later. Use M260 instead.

### Parameters

* **Ann** I2C address
* **Bnn** How many bytes to request

### Examples
<br>
<pre class="cblock">
M261 A99 B5 ; Request 5 bytes from Address 99
</pre>

## M280: Set servo position

### {.tabset}

#### RepRapFirmware 3.x

##### Parameters

* **Pnnn** Servo index
* **Snnn** Angle (see notes) or microseconds

##### Examples
<br>
<pre class="cblock">
M950 S0 C"exp.heater3"  ; assign GPIO port 0 to heater3 on expansion connector, servo mode
...
M280 P0 S80  ; set 80deg servo position on GPIO port 0
</pre>

##### RRF 3.x notes

Before you can use M280 you must create a GPIO port using M950. Then in the M280 command, the P parameter is the GPIO port number. 

To invert the output, specify an inverted pin name in M950 when you create the GPIO port.

In RRF 3.4, Duet 3 supports up to 32 output and 16 input ports, Duet 2 Wifi/Ethernet support up to 20 GPIO ports, and Duet 2 Maestro supports 10 GPIO ports. No GPIO ports are allocated by default.

#### RepRapFirmware 2.x

##### Parameters

* **Pnnn** Servo index
* **Snnn** Angle (see notes) or microseconds
* **I1** Invert polarity (not supported in RRF3)

##### Examples
<br>
<pre class="cblock">
M280 P1 S50    ; set Heater 1 pin to 50deg servo position
M280 P3 I1 S80 ; set Heater 3 pin to 80deg servo position, inverted
</pre>

##### RRF 2.x notes

The optional I1 parameter causes the polarity of the servo pulses to be inverted compared to normal for that output pin. The I parameter is not remembered between M280 commands (unlike the I parameter in M106 commands), so if you need inverted polarity then you must include I1 in every M280 command you send.

The servo index is the same as the pin number for the M42 command.

### Notes

S values below 544 are treated as angles, and 544 or greater as the pulse width in microseconds.

The relationship between the S parameter and the pulse width output to the port is the same as in other 3D printer firmwares, so that devices such as BLTouch will perform the same way. However, **there is no standard for servos on the relationship between pulse width and servo angle**. Therefore, **for most servos the value of the S parameter does not equal the servo angle**. Almost all servos accept a pulse width range of at least 1us to 2us, which corresponds to an S parameter range of 44.2 to 141.2 degrees. So for many servos, values in the range 44.2 to 141.2 or alternatively 1000 to 2000 will cover the full operating range of the servo.

See also [Using hobby servos and DC motors](/User_manual/Connecting_hardware/Motors_servos){target=_blank}.

## M290: Baby stepping

Supported in firmware version 1.18 and later.

### Parameters

* **Snnn** Amount to baby step Z in mm. Positive values raise the head or lower the bed, negative values do the opposite.
* **Znnn** Synonym for S (RepRapFirmware 1.21 and later)
* **X,Y,U...** Amount to babystep other axes (RRF 2.03 and later)
* **Rn** (Optional, RepRapFirmware 1.21 and later) R1 = relative (add to any existing babystep amount, the default), R0 = absolute (set babystepping offset to the specified amount)

### Examples
<br>
<pre class="cblock">
M290 S0.05  ; babystep the head up 0.05mm
M290 R0 S0  ; clear babystepping (RepRapFirmware 1.21 and later only)
</pre>

### Notes

This command tells the printer to apply the specified additional offset to the Z coordinate for all future moves, and to apply the offset to moves that have already been queued if this can be done. Baby stepping is cumulative, for example after M290 S0.1 followed by M290 S-0.02, an offset of 0.08mm is used.

M290 with no parameters reports the accumulated baby stepping offset. Marlin doesn't track accumulated babysteps.

In RepRapFirmware 1.19 and earlier, the babystepping offset is reset to zero when the printer is homed or the bed is probed. In RepRapFirmware 1.21 and later, homing and bed probing don't reset babystepping, but you can reset it explicitly using M290 R0 S0.

## M291: Display message and optionally wait for response

Supported in firmware version 1.19 and later.

### Parameters

* **P"message"** The message to display, which must be enclosed in double quotation marks. If the message itself contains a double quotation mark, use two double quotation marks to represent it. Maximum length <250 chars. Limited by total gcode length limit of 256 characters.
* **R"message"** Optional title for the message box. Must be enclosed in double quotation marks too. Maximum length 60 chars
* **Sn** Message box mode (see below), default 1 
* **Tn** Timeout in seconds, only used if the mode is 0, 1, 3, or 4 or more with a cancel button. The message will be cancelled after this amount of time if the user does not cancel it before then. A zero or negative value means that the message does not time out (it may still be cancelled by the user if it has a Close button). For modes 0 and 1 there is a default timeout of 10 seconds if no T parameter is provided. For other modes the default is no timeout.
* **X, Y, Zn** 0 = no special action (default), 1 = display jog buttons alongside the message to allow the user to adjust the head position on the specified axis.
* **Jn** (RRF 3.5 and later only, optional) If message box mode >= 4: 0 = no Cancel button (default), 1 = display a Cancel button.
* **K{"choice1","choice1",...}**  (RRF 3.5 and later only) List of choices, required (and only used) when S=4.
* **Lnnn** (RRF 3.5 and later only, optional) Minimum accepted value (S=5 or S=6), or minimum number of characters (S=7).
* **Hnnn** (RRF 3.5 and later only, optional) Maximum accepted value (S=5 or S=6), or maximum number of characters (S=7).
* **Fnnn** or **F"text"** (RRF 3.5 and later only, optional) default choice number (counting from 0) when S=4, or default value when S>=5.

### Description

This command provides a more flexible alternative to M117, in particular messages that time out, messages that suspend execution until the user acknowledges them, and messages that allow the user to adjust the height of the print head before acknowledging them.

Allowed message box modes (S parameter) are:

S0: No buttons are displayed (non-blocking)
S1: Only "Close" is displayed (non-blocking)
S2: Only "OK" is displayed (blocking, send M292 to resume the execution)
S3: "OK" and "Cancel" are displayed (blocking, send M292 to resume the execution or M292 P1 to cancel the operation in progress)
S4: (RRF 3.5 and later only) Display a number of choices. The names of the choices are given by the K parameter as an array of strings. The choice selected by the user, as the array index, is available to be used in the ["input" constant](/User_manual/Reference/Gcode_meta_commands#named-constants)
S5: (RRF 3.5 and later) Prompt for an integer value. L is the minimum accepted value (default 0), H is the maximum accepted value (default unlimited), and F is the default value. The integer is available to be used in the ["input" constant](/User_manual/Reference/Gcode_meta_commands#named-constants)
S6: (RRF 3.5 and later) Prompt for a floating point value. L is the minimum accepted value (default 0.0), H is the maximum accepted value, and F is the default value. The floating point value is available to be used in the ["input" constant](/User_manual/Reference/Gcode_meta_commands#named-constants)
S7: (RRF 3.5 and later) Prompt for a string value. L is the minimum number of characters (default 1), H is the maximum number of characters (default 10), and F is the default value. The string is available to be used in the ["input" constant](/User_manual/Reference/Gcode_meta_commands#named-constants)


### Notes

The combination S0 T0 is not permitted, because that would generate a message box with no close button and that never times out, which would lock up the user interface.

Duet Web Control 2.03 and later support HTML messages but that may not be displayed correctly on an attached PanelDue.

When using Duet 3 with attached SBC, DSF versions before v3.1.1 support only non-blocking calls are supported in DuetSoftwareFramework. M291 is fully supported in DSF v3.1.1 and later.

The limit in RRF 3.4 and later is 256 characters in the entire GCode command. Before 3.4 is was 200 characters, in RRF2 it's 160 characters.

### Examples
<br>
<pre class="cblock">
M291 R"Title" P"Message" K{"Yes","No"} S4
if (input == 1)
    echo "No chosen"

</pre>
<br>
<pre class="cblock">
M291 R"Title" P"Request for string" S7 L5 H40 F"desfault string"
echo {input^" entered by user"}
</pre>



## M292: Acknowledge blocking message

**Caution**: this command is intended to be used by user interfaces only.

### Parameters

* **Pnnn** Whether the current operation shall be cancelled (P=1) or continued (P=0)
* **R{expression}** (RRF 3.5 and later, only present if P=0 and the message box mode was 4 or greater) The returned value. For message box mode 4 this is an integer representing the choice that was selected, counting from 0. For message box modes 5/6/7 this is the integer/float/string value that was entered.
* **Snnn** (RRF 3.5 and later) The sequence number of the message being acknowledged

This command is sent by the user interface when the user acknowledges a message that was displayed because of a M291 command with parameter S=2 or S=3.  DWC and PanelDue 3.5 and later also use thie command to acknowledge a non-blocking message (M291 command with parameter S=0 or S=1) but in that case the S parameter must match the sequence number of the message being acknowledged. The P parameter is ignored unless M291 was called with S=3, and always ignored by RRF 3.5 and later if the R parameter is present.

In RRF versions prior to 3.5, M292 may only be used to acknowledge blocking message boxes. RRF 3.5 and later allow M292 to be used to cancel non-blocking message boxes too, provided that the correct sequence number is specified.

Supported in firmware version 1.19 and later.

## M300: Play beep sound

### Parameters

* **Snnn** frequency in Hz
* **Pnnn** duration in milliseconds

### Examples
<br>
<pre class="cblock">
M300 S300 P1000
</pre>

### Notes

Play beep sound, use to notify events like the end of printing. If an LCD device is attached to RepRapFirmware, a sound is played via the add-on touch screen control panel. Else the web interface will play a beep sound.

If you intend to play multiple notes in a row, you will need to insert a G4 delay command between them at least equal to the length of the tone.

Example:
<br>
<pre class="cblock">
M300 S2000 P200
G4 P200
M300 S2500 P300
G4 P300
</pre>

See also [Macros, sounds section](/User_manual/Tuning/Macros#sounds){target=_blank}

## M301: Set PID parameters

### Parameters

* **Hnnn** heater number
* **Pnnn** proportional (Kp)
* **Innn** integral (Ki)
* **Dnnn** derivative (Kd)

### Examples
<br>
<pre class="cblock">
M301 H1 ; Report PID values
M301 H1 P20 I0.5 D100 ; Set PID values
</pre>

### Order dependency

* **RRF 3.x**: The M301 command must come later in config.g than the M950 command that created the heater number it refers to.

### Notes

Sets Proportional (P), Integral (I) and Derivative (D) values for hot end. See also M303

* H: Is the heater number, and is compulsory. H0 is the bed, H1 is the first hot end, H2 the second etc.
* P: Proportional value
* I: Integral value
* D: Derivative value

The P, I and D values must be provided scaled by a factor of 255, for compatibility with older firmwares.

Note: PID parameters are computed automatically when the M307 command is used to define the heater model, or from the default heater model if no M307 command is provided. You can use M301 to override those computed PID parameters, but this is not recommended because it prevents RepRapFirmware from using different PID parameters depending on the heating phase.

## M302: Allow cold extrudes

### Parameters

* *This command can be used without any additional parameters.*
* **Pnnn** Cold extrude allow state
* **Snnn** Minimum extrusion temperature (RepRapFirmware 2.02 and later)
* **Rnnn** Minimum retraction temperature (RepRapFirmware 2.02 and later)

### Examples
<br>
<pre class="cblock">
M302 ; Report current state
M302 P1 ; Allow cold extrusion
M302 S120 R110 ; Allow extrusion starting from 120°C and retractions already from 110°C
</pre>

### Notes

This tells the printer to only allow movement of the extruder motor above a certain temperature, or if disabled, to allow extruder movement when the hotend is below a safe printing temperature.

The minimum temperatures for extrusion can be set using the Snnn parameter with a default value of 160°C if unset. A minimum retraction temperature can be set with the Rnnn parameter. The default for this is 90°C.

M302 with no parameters it will report the current cold extrusion state.

One limitation of M302 is that it requires a thermistor to be present for the temperature to be monitored. If your system does not have a thermistor or heater to be monitored, you can define your tool in M563 without a heater to disable cold extrusion protection on that tool. Example: 
<br>
<pre class="cblock">
M563 P0 S"Pump" D0 F0
</pre>
 
Note the lack of H parameter.

## M303: Run heater tuning

### Parameters

* **Hnnn** heater number (in RRF 3.2 and later, this parameter is optional if the T parameter is given)
* **Pnnn** PWM to use, 0 to 1 (you should normally use 1 i.e. full power), default 1
* **Snnn** target temperature
* **Tnnn** (RRF 3.2 and later, optional) Tool whose primary heater is to be tuned
* **Annn** (RRF 3.2 and later, optional) ambient temperature - use this parameter if you want to tune a heater that has been on and has not cooled down to ambient temperature yet
* **Ynn** (RRF 3.3 and later, optional) Tuning cycle hysteresis, default 5C. When tuning bed heaters that are slow to cool down, tuning will be faster if you use a lower value, provided that there is no noise in the temperature readings.
* **F**nn (RRF 3.3 and later) Fan PWM to use when the print cooling fan is turned on (ignored if the T parameter is not present), default 0.7 in RRF 3.4.0. Use a lower value if your printer uses a powerful print cooling fan that you do not normally run at full PWM.
* **Qn** (RRF 3.5 and later) Q0 (default) = display M307 parameters and suggestion to edit config.g or run M500 when tuning completes, Q1 = Quiet mode (do not display those messages). Use Q1 if you run M303 as part of a macro that saves the tuning result.

### Examples
<br>
<pre class="cblock">
M303 H1 P1 S240 ; tune heater 1 using 100% PWM, target temperature 240C
M303 T0 S205 ; tune the primary heater of tool 0 (RRF 3.2beta3.2 and later)
</pre>

### Notes

PID Tuning refers to a control algorithm used to tune heating behaviour for hot ends and heated beds. This command computes the process model parameters (see [M307](/User_manual/Reference/Gcodes/M307){target=_blank}), which are in turn used to calculate the PID constants. Note that the tuning algorithm will overshoot the target temperature; the amount of overshoot depends on how powerful the heater is. See [Tuning heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning){target=_blank}.

Tuning is performed asynchronously. Run M303 with no parameters while a tuning is underway to see the current tuning state, or the last tuning result if the tuning process has already completed.

## M304: Set PID parameters - Bed

### Parameters

* *This command can be used without any additional parameters.*
* **Pnnn** proportional (Kp)
* **Innn** integral (Ki)
* **Dnnn** derivative (Kd)

### Examples
<br>
<pre class="cblock">
M304 P1 I2 D3
M304 ; Report parameters
</pre>

### Notes

Sets Proportional, Integral and Derivative values for bed. This command is identical to M301 except that the H parameter (heater number) defaults to zero.

## M305: Set temperature sensor parameters

RepRapFirmware 3: Use M308 instead (see Notes).

### Parameters

* **Pnnn** Heater number (0, 1, 2...) or virtual heater number (100, 101, 102...)
* **S"name"** Heater name (optional). Named virtual heaters are shown in Duet Web Control; anonymous virtual heaters are not.
* **Xnnn** Heater ADC channel, or thermocouple or PT100 adapter channel; defaults to the same value as the P parameter
* **Tnnn** (for thermistor sensors) The thermistor resistance at 25^o^C
* **T"c"** (for MAX31856-based thermocouple sensor daughter boards) The thermocouple type letter, default K
* **Bnnn** If the sensor is a thermistor, this is the Beta value. For the Steinhart-Hart thermistor model, this is the *reciprocal* of the B coefficient
* **Cnnn** If the sensor is a thermistor, this is the Steinhart-Hart C coefficient, default 0
* **Rnnn** If the sensor is a thermistor or PT1000 sensor, this is the Series resistor value, see here for more information: [M305 R parameter](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000#the-m305m308-r-parameter){target=_blank}.
* **Lnnn** If the sensor is a thermistor, this is the ADC low offset. If it is a current loop sensor, this is the temperature when the current is 4mA.
* **Hnnn** If the sensor is a thermistor, this is the ADC high offset. If it is a current loop sensor, this is the temperature when the current is 20mA.
* **Fnn** (where nn is 50 or 60) If the sensor interface uses a MAX31856 thermocouple chip or MAX31865 PT100 chip, this is the local mains frequency. Readings will be timed to optimise rejection of interference at this frequency.
* **Wn** Configure number of wires used to connect PT100 sensor. Should be [2..4].

### Examples
<br>
<pre class="cblock">
M305 P1 T100000 R1000 B4200
</pre>

### Description

Sets the parameters for temperature measurement. The example above tells the firmware that for heater 1 (P parameter: 0 = heated bed, 1 = first extruder) the thermistor 25C resistance (T parameter) is 100Kohms, the thermistor series resistance (R parameter) is 1Kohms, the thermistor beta (B parameter) is 4200. All parameters other than P are optional. If only the P parameter is given, the existing values are displayed. DuetWebControl 1.19.2 and newer support optional units to be set by the S parameter in the form of "Heater name [Unit]".

### Notes

**Notes - RepRapFirmware 3**

Prior to RRF3, every temperature sensor belongs to a heater. For sensors with no controllable heater (e.g. the MCU temperature sensor) you have to create a "virtual heater" in order to be able to use the sensor. In RRF3, sensors are created and configured independently from heaters, using the M308 command. When creating a heater using M950, you tell it which sensor to use. You must create the sensor before you refer to it in a M950 command. M305 is not used.

**Notes - RepRapFirmware 2.x and earlier**

RepRapFirmware also supports ADC gain and offset correction and a thermistor selection facility. Example:
<br>
<pre class="cblock">
M305 P1 T100000 R1000 B4200 H14 L-11 X2
</pre>

The H correction affects the reading at high ADC input voltages, so it has the greatest effect at low temperatures. The L correction affects the reading at low input voltages, which correspond to high temperatures. The ADC on the Duet 2 WiFi and Duet 2 Ethernet is self-calibrating, so you should not need to provide any corrections when using these controllers.

The X parameter tells the firmware which temperature sensor channel to use channel, as follows:

* Channels 0, 1... are the thermistor inputs for heaters 0, 1 etc.
* Channels 100, 101... are MAX31855 thermocouple channels using chip selects CS1, CS2... on the SPI bus, see [Connecting thermocouples](/User_manual/Connecting_hardware/Temperature_connecting_thermocouples){target=_blank}.
* Channels 150, 151... are MAX31856 thermocouple channels using chip selects CS1, CS2... on the SPI bus
* Channels 200, 201... are MAX31865 PT100 channels using chip selects CS1, CS2... on the SPI bus, see [Connecting PT100 temperature sensors](/User_manual/Connecting_hardware/Temperature_connecting_PT100){target=_blank}.
* Channels 300, 301... are current loop channels using chip selects CS1, CS2... on the SPI bus
* Channels 400, 401... are DHTxx temperature channels. The DATA line of the DHTxx must be connected to one of pins CS1, CS2... on the SPI bus. Specify the sensor type (11 for DHT11, 21 for DHT21 or 22 for DHT22) via the T-parameter. e.g. M305 P102 X401 T22 S"DHT temperature"
* Channels 450, 451... are as 400, 401... but specify the corresponding humidity sensor of the DHTxx
* Channels 500, 501... are the thermistor inputs, but configured for PT1000 sensors rather than thermistors. Only the R, H and L parameters are significant, see example below.
* Channel 1000 is the on-chip microcontroller temperature sensor
* Channel 1001 represents the temperature warning and overheat flags on the TMC2660, TMC2224 or other smart drivers on the Duet main board. It reads 0C when there is no warning, 100C if any driver reports over-temperature warning , and 150C if any driver reports over temperature shutdown.
* Channel 1002 is as channel 1001 but for drivers on the Duex 2 or Duex 5 expansion board.

If the M305 command for a real heater does not specify a sensor channel and the heater has not been configured yet, then it defaults to using the thermistor associated with that heater.

PT1000 Example:
<br>
<pre class="cblock">
M305 P1 X501 R2200 ; heater 1 uses a PT1000 connected to thermistor channel 1 which has a 2.2K series resistor (i.e a Duet 2 Maestro)
</pre>

Note: PT1000 sensors connected to thermistor inputs have lower resolution than PT100 sensors connected via the PT100 daughter board. The accuracy of PT1000 sensors should be very good on the Duet 2 Maestro and generally good on the Duet 2 Wifi and Duet 2 Ethernet. See the [PT1000 documentation](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000){target=_blank} for more details.

Virtual heaters 100, 101 and 102 are preconfigured to use temperature sensor channels 1000, 1001 and 1002 respectively. We suggest you use virtual heaters 103 upwards if you want to create additional virtual heaters.

If you send the following command:
<br>
<pre class="cblock">
M305 P101
</pre>

you should get the response "Heater 101 uses TMC2660 temperature warnings sensor channel 1001". But as this virtual heater has no name, it doesn't show up in DWC. You can fix that by sending:
<br>
<pre class="cblock">
M305 P101 S"Drivers"
</pre>

After that, if you go to the "Extra" tab in DWC (where is says Tools/Heaters/Extra), you will see "Drivers" as an entry.

## M307: Set or report heating process parameters

### {.tabset}

#### RRF 3.4 and later

##### Parameters

* **Hn** Heater number (0 is usually the bed heater)
* **Rnnn** Heating rate in degC/sec at full power when the heater temperature is close to ambient (RRF 3.2 and later)
* **Dnnn** Dead time in seconds
* **Ennn** Exponent of the cooling rate curve, default 1.35. Used in conjunction with the K parameter (RRF 3.4 and later) 
* **Knnn** or **Knnn:nnn** Cooling rate in degC/sec when the heater is 100C above ambient. If one value is given then the cooling rate is calculated as K/((Th-Ta)/100)^E where Th is the heater temperature and Ta is the ambient temperature. If two values are given then the cooling rate is calculated as K[0]/((Th-Ta)/100)^E + K[1]/((Th-Ta)/100)^E\*F where F is the fan PWM in the range 0 to 1. (RRF 3.4 and later)

**Additional parameters to help control the heating process:**

* **Bn** selects Bang-bang control instead of PID if non-zero. Default at power-up is 0 for extruder heaters, 1 for the bed heater.
* **Innn** Invert PWM signal (I0 = not inverted, I1 = invert PWM/bang-bang signal for inverted temperature control [e.g. with Peltier elements])
* **Snnn** maximum PWM to be used used with this heater on a scale of 0 to 1. Default 1.0.
* **Vnnn** VIN supply voltage at which the R parameter was calibrated. This allows the PID controller to compensate for changes in supply voltage. A value of zero disables compensation for changes in VIN voltage. Supply voltage compensation is applied to hot end heaters only, not to bed or chamber heaters.

##### Examples (RRF 3.4 and later)
<br>
<pre class="cblock">
M307 H0 ; report the process parameters for heater 0
M307 H1 R2.186 K0.17:0.11 D5.67 S1.00 V24.0 ; set the process parameters for heater 1
</pre>

##### Notes (RRF 3.4 and later)

* The K parameter is the rate of cooling in degC/sec when the heater is turned off and the temperature is falling through 100C above ambient temperature. The K parameter is calculated as:
  `K = ( temperature change / time in seconds ) / (( heater temperature - ambient temperature ) / 100 )^E parameter`
  The K parameter can take a second value to allow RRF to calculate the heater cooling rate with the cooling fan on.
  `K[fan] = ( temperature change / time in seconds ) / (( heater temperature - ambient temperature ) / 100)^E parameter * F (fan PWM in the range 0 to 1)`
  The second K value is the additional cooling rate due to the fan running at full PWM. 
* The C parameter is deprecated in RRF 3.4.0beta7 and later in favour of the K and E parameters, however existing M307 commands using C and/or A parameters will continue to work.
* See notes on previous RRF 3.x tabs for all changes since RRF 2.x.

#### RRF 3.3 and 3.2

##### Parameters

* **Hn** Heater number (0 is usually the bed heater)
* **Rnnn** Heating rate in degC/sec at full power when the heater temperature is close to ambient (RRF 3.2 and later)
* **Cnnn** or **Caaa:bbb** dominant time Constant of the heating process in seconds. If two values are provided (supported in RRF 3.2 and 3.3), the first value is with the fan off and the second is with the fan on at full PWM.
* **Dnnn** Dead time in seconds

**Additional parameters to help control the heating process:**

* **Bn** selects Bang-bang control instead of PID if non-zero. Default at power-up is 0 for extruder heaters, 1 for the bed heater.
* **Innn** Invert PWM signal (I0 = not inverted, I1 = invert PWM/bang-bang signal for inverted temperature control [e.g. with Peltier elements]
* **Snnn** maximum PWM to be used used with this heater on a scale of 0 to 1. Default 1.0.
* **Vnnn** VIN supply voltage at which the R parameter was calibrated (RepRapFirmware 1.20 and later). This allows the PID controller to compensate for changes in supply voltage. A value of zero disables compensation for changes in VIN voltage. Supply voltage compensation is applied to hot end heaters only, not to bed or chamber heaters.

##### Examples (RRF 3.2 and 3.3)
<br>
<pre class="cblock">
M307 H0 ; report the process parameters for heater 0
M307 H1 R2.186 C202.1:155.0 D5.67 S1.00 V24.0 ; set the process parameters for heater 1
</pre>

##### Notes (RRF 3.2 and 3.3)

* The A parameter is deprecated in RRF 3.2 and later in favour of the R parameter, however existing M307 commands using the A parameter will continue to work.
* See notes on previous RRF 3.x tabs for all changes since RRF 2.x.

#### RRF 3.1 and 3.0

##### Parameters

* **Hn** Heater number (0 is usually the bed heater)
* **Annn** gAin, expressed as ultimate temperature rise obtained in degC divided by the PWM fraction. For example, if G=180 then at 50% PWM the ultimate temperature rise would be 90C.
* **Cnnn** dominant time Constant of the heating process in seconds.
* **Dnnn** Dead time in seconds

**Additional parameters to help control the heating process:**

* **Bn** selects Bang-bang control instead of PID if non-zero. Default at power-up is 0 for extruder heaters, 1 for the bed heater.
* **Innn** Invert PWM signal (I0 = not inverted, I1 = invert PWM/bang-bang signal for inverted temperature control [e.g. with Peltier elements])
* **Snnn** maximum PWM to be used used with this heater on a scale of 0 to 1. Default 1.0.
* **Vnnn** VIN supply voltage at which the A parameter was calibrated (RepRapFirmware 1.20 and later). This allows the PID controller to compensate for changes in supply voltage. A value of zero disables compensation for changes in VIN voltage. Supply voltage compensation is applied to hot end heaters only, not to bed or chamber heaters.

##### Examples (RRF 3.1 and 3.0)
<br>
<pre class="cblock">
M307 H0 ; report the process parameters for heater 0
M307 H1 A346.2 C140 D5.3 B0 S0.8 V23.8; set process parameters for heater 1, use PID, and limit heater 1 PWM to 80%
</pre>

##### Notes

* The F parameter is no longer supported. Use M950 to set the frequency in the M950 command that you use to define the heater.
* The I2 parameter is no longer supported, and I3 does the same as I1. You can use M950 to invert the output.
* You can no longer disable a heater using M307 A-1 C-1 D-1. To use the pin for something else, don't create a heater on that pin. 

Example:
<br>
<pre class="cblock">
;RRF 2.x code
M307 H0 F100  ; change heater 0 PWM frequency to 100Hz
M307 H2 A-1 C-1 D-1 ; disable heater 2 so we can use its pin to drive a fan

;RRF 3.x code
M950 H0 C"bed_heat" Q100 T0 ; heater 0 uses the bed_heat pin, sensor 0, PWM frequency 100Hz
; No need to disable heater 2 because we didn't define it in the first place
</pre>

#### RRF 2.x and 1.20 and later 1.x

##### Parameters

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

##### Notes (RRF 2.x and 1.20 and later 1.x)

* RepRapFirmware 1.16 and later allow the PID controller for a heater to be disabled by setting the A, C and D parameters to -1. This frees up the corresponding heater control pin for use as a general purpose I/O pin to use with the M42 or M280 command. Example: `M307 H2 A-1 C-1 D-1`.

### Order dependency

* **RRF 3.x**: The M307 command must come later in config.g than the M950 command that created the heater number it refers to.

### Notes

Each heater and its corresponding load may be approximated as a first order process with dead time, which is characterised by the gain, time constant and dead time parameters. The model can used to calculate optimum PID parameters (including using different values for the heating or cooling phase and the steady state phase), to better detect heater faults, and to calculate feed-forward terms to better respond to changes in the load. Normally these model parameters are found by auto tuning - see [M303](/User_manual/Reference/Gcodes/M303){target=_blank} and [Tuning heater temperature control](/User_manual/Connecting_hardware/Heaters_tuning){target=_blank}.

For those platforms that provide power voltage monitoring, the calibration voltage setting allows the heating controller to adjust the power automatically in response to changes in the power supply voltage. For example, if a bed or chamber heater is turned on or off, this may cause the power supply voltage to change a little, which if not corrected for would change the extruder heater power.

## M308: Set or report sensor parameters

M308 is supported in RepRapFirmware 3. If running RRF2.x or earlier, use M305.

### Parameters

* **Sn** Sensor number
* **P"pin_name"** The name of the control board pin that this sensor uses. For thermistors it is the thermistor input pin name, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank}. For sensors connected to the SPI bus it is the name of the output pin used as the chip select.
* **Y"sensor_type"** The sensor and interface type, one of: "thermistor", "pt1000", "rtd-max31865", "thermocouple-max31855", "thermocouple-max31856", "linear-analog", "dht21", "dht22", "dht-humidity", "bme280", "bme-pressure", "bme-humidity", "current-loop-pyro", "drivers", "mcu-temp" (see note below regarding "mcu-temp" support on Duet 3 Mini 5+). Duet WiFi/Ethernet with an attached DueX2 or DueX5 also support "drivers-duex". "dht11" is supported in firmware up to RRF 3.3, but removed from RRF 3.4 onward. "bme280" is only supported in RRF 3.5 and later, and only on Duet 3 boards.
* **A"name"** Sensor name (optional), displayed in the web interface

**Additional parameters for thermistors**

* **Tnnn** (for thermistor sensors) Thermistor resistance at 25°C
* **Bnnn** Beta value, or the reciprocal of the Steinhart-Hart thermistor model B coefficient
* **Cnnn** Steinhart-Hart C coefficient, default 0
* **Rnnn** Series resistor value. Leave blank to use [the default for your board](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000#the-m305m308-r-parameter){target=_blank}.
* **Lnnn** ADC low offset correction, default 0
* **Hnnn** ADC high offset correction, default 0

**Additional parameters for PT1000 sensors**

* **Rnnn** Series resistor value. Leave blank to use [the default for your board](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000#the-m305m308-r-parameter){target=_blank}.
* **Lnnn** ADC low offset correction, default 0
* **Hnnn** ADC high offset correction, default 0

**Additional parameters for MAX31856-based thermocouple sensors**

* **K"c"** The thermistor type letter, default K
* **Fnn** (where nn is 50 or 60) The local mains frequency. Readings will be timed to optimise rejection of interference at this frequency.

**Additional parameters for MAX31865-based PT100 sensors**

* **Rnnn** Reference resistor value. Leave blank to use the default for your SPI daughterboard.
* **Wnnn** Number of wires used to connect the PT100 sensor (2, 3, or 4).
* **Fnn** (where nn is 50 or 60) The local mains frequency. Readings will be timed to optimise rejection of interference at this frequency.

**Additional parameters for linear analog sensors**

* **Fn** F0 = unfiltered (fast response), F1 = filtered (slower response, but noise reduced and ADC oversampling used to increase resolution). F1 is only available when using a port intended for thermistors, not when using a general input port.
* **Bnnn** The temperature or other value when the ADC output is zero
* **Cnnn** The temperature or other value when the ADC output is full scale

**P"nnn" parameter for "dht-humidity", "bme-pressure" and "bme-humidity**

DHT sensors provide a primary temperature output and an additional output providing humidity. To access the humidity output of a DHT sensor you must first configure the primary sensor of type "dht21" or "dht22". Then you can configure "dht-humidity" to be attached to the DHT sensor's secondary output, by specifying port P"Snnn.1" where nnn is the sensor number of the primary sensor.

Similarly, BME280 sensors provide a primary temperature output and two additional outputs providing pressure and humidity. To access the additional output of a BME280 sensor you must first configure the primary sensor of type "bme280". Then you can configure sensor "bme-pressure" to be attached to the BME280 sensor's secondary output, by specifying port P"Snnn.1" where nnn is the sensor number of the primary sensor; and you can configure sensor "bme-humidity" to be attached to the BME280 sensor's secondary output by specifying port P"Snnn.2".

### Examples
<br>
<pre class="cblock">
M308 S1 P"temp1" Y"thermistor" T100000 B4725 C7.06e-8 ; configure sensor 1 as thermistor on pin temp1
M308 S1 P"temp1" Y"pt1000"                            ; configure sensor 1 as PT1000 on pin temp1
M308 S1 P"spi.cs1" Y"thermocouple-max31856"           ; configure sensor 1 as K-type thermocouple via CS pin spi.cs1 
M308 S1 P"spi.cs1" Y"rtd-max31865"                    ; configure sensor 1 as PT100 on pin spi.cs1
M308 S10 Y"mcu-temp" A"MCU"                           ; defines sensor 10 as MCU temperature sensor
M308 S11 Y"drivers" A"Duet stepper drivers"           ; defines sensor 11 as stepper driver temperature sensor
M308 S12 Y"drivers-duex" A"Duex stepper drivers"      ; for Duet 2 WiFi/Ethernet with DueX2/5, defines sensor 12 as DueX2/5 stepper driver temps<br>
M308 S10 P"0.spi.cs1" Y"dht22" A"Filament Temp"       ; define DHT22 temperature sensor
M308 S11 P"S10.1" Y"dhthumidity" A"Filament Hum[%]"   ; Attach DHT22 humidity sensor to secondary output of temperature sensor
</pre>

To read mcu and  driver temperatures on an expansion board connected to a Duet 3 mainboard, set the CAN address in the P parameter. For example, a board at CAN address 1 would use:
<br>
<pre class="cblock">
M308 S12 Y"mcu-temp" P"1.dummy" A"3HC MCU"
M308 S13 Y"drivertemp" P"1.dummy" A"3HC Steppers"
</pre>

Note that from RRF 3.4.0 beta 8, "drivertemp" will be changed to "drivers" to match the main board.

### Notes

* This code replaces M305 in RepRapFirmware 3. In earlier versions of RepRapFirmware, sensors only existed in combination with heaters, which necessitated the concept of a "virtual heater" to represent a sensor with no associated heater (e.g. MCU temperature sensor). RepRapFirmware 3 allows sensors to be defined independently of heaters, up to a max of 32 sensors. The association between heaters and sensors is defined using M950.
* M308 can be used in the following ways:
  * **M308 Snn Y"type" [other parameters]**: delete sensor nn if it exists, create a new one with default settings, and configure it using the other parameters. At least the pin name must also be provided, unless the sensor doesn't use a pin (e.g. MCU temperature sensor).
  * **M308 Snn**: report the settings of sensor nn, this will also report the last error on that sensor if applicable
  * **M308 A"name"**: report the settings of the first sensor named "name"
  * **M308 Snn [any other parameters except Y]**: amend the settings of sensor nn
* Sensor type names obey the same rules as [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank}, i.e. case is not significant, neither are hyphen and underscore characters.
* All Duets have some degree of auto-calibration to measure and cancel gain and offset errors in the analog-to-digital converters (ADC). The L and H parameters override auto-calibration. For more information on tuning Duet ADCs, see [Connecting thermistors and PT1000 temperature sensors - When to calibrate](/User_manual/Connecting_hardware/Temperature_connecting_thermistors_PT1000#when-to-calibrate).
* The Trinamic drivers used on Duets do not report temperature, rather they report one of: temperature OK, temperature overheat warning, and temperature overheat error. RRF translates these three states into readings of 0C, 100C and 130C.
* mcu-temp on Duet 3 Mini 5+: The SAME54P20A chip used in the Duet 3 Mini 5+ does not have a functioning temperature sensor. In theory it does have an on-chip temperature sensor, but the errata document for the chip says it doesn't work. However, experimental support for the Duet 3 Mini 5+ on-chip MCU temperature sensor has been added in RepRapFirmware 3.3 beta 3. As the chip manufacturer advises that it is not supported and should not be used, we can't promise that it will give useful readings on all boards. It will be removed if it causes significant support issues. Please report any issues in the [Duet3D support forum](https://forum.duet3d.com/){target=_blank}.
* When converting from older versions of RRF to RRF3 you must replace each M305 command by a similar M308 command, which must be earlier in config.g than any M950 command that uses it. You must also use M950 to define each heater that you use, because there are no default heaters.
  Example - old code:
  <pre class="cblock">
  M305 P0 T100000 B3950 ; bed heater uses a B3950 thermistor
  M305 P1 T100000 B4725 C7.06e-8 ; E0 heater uses E3D thermistor
  </pre>
  New code:
  <pre class="cblock">
  M308 S0 P"bed_temp" Y"thermistor" T100000 B3950 ; define bed temperature sensor
  M308 S1 P"e0temp" Y"thermistor" T100000 B4725 C7.06e-8 ; define E0 temperature sensor
  M950 H0 C"bed_heat" T0 ; heater 0 uses the bed_heat pin, sensor 0
  M950 H1 C"e0heat" T1 ; heater 1 uses the e0heat pin and sensor 1
  </pre>

## M309: Set or report heater feedforward

Supported in RepRapFirmware v3.4 and later

### Usage

* M309 Pn Saaa:bbb

### Parameters

* **Pn** Tool number
* **Saaa:bbb:ccc...** Feedforward coefficients. The number of coefficients provided must equal the number of heaters configured for the tool when it was created (see M563).

### Notes

If the P parameter is not provided, the current tool is assumed. If the S parameter is not provided, the existing coefficients are reported.

The units of S are PWM fraction (on a scale of 0 to 1) per mm/sec of filament forward movement.

### Calibration

Heat the nozzle and let the temperature stabilise. Then commence extrusion at a fast rate (as fast as the extruder can reasonable manage without skipping) and watch the temperature. If there is an initial drop, then increase the feedforward. What you are looking for is either the temperature remaining steady, or rising by a small amount followed by a drop below target of a similar amount. When extrusion stops the reverse will happen, i.e. with no feedforward the temperature will rise and then gradually return to target.

## M350: Set microstepping mode

### Usage

* M350 Xnn Ynn Znn Enn Inn

### Parameters

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

### Order dependency

This command must be later in config.g than any M584 command.

### Examples
<br>
<pre class="cblock">
M350 Z1 ;set the Z-axis' driver to use full steps
M350 E4:4:4 ;set extruders 0-2 to use quarter steps)
</pre>

When M350 is processed, the steps/mm will be adjusted automatically to allow for any changes in microstepping. Therefore you can either:

a) Set Steps/mm correctly for the default 1/16 microstepping, then set the microstepping to the desired amount using M350:
<br>
<pre class="cblock">
M92 X80 Y80 Z400 ; set axis steps/mm
M92 E420:430 ; set extruder 0 and 1 steps/mm
M350 X128 Y128 Z128 E128:128 ; set microstepping
</pre>

or

b) Set the microstepping using M350 and then set the correct steps/mm for that microstepping amount:
<br>
<pre class="cblock">
M350 X128 Y128 Z128 E128:128 ; set microstepping
M92 X640 Y640 Z3200 ; set axis steps/mm @128 microstepping
M92 E3360:3440 ; set extruder 0 and 1 steps/mm
</pre>

Assuming that in the first example the microstepping was initially at the default x16, both the above examples result in the same steps/mm settings.

### Notes

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour.

Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M350 Z16, not M350 Z16,16

Microstep interpolation at all microstep settings is supported on all Duet 3 boards with onboard drivers (TMC5160, TMC2160 or TMC2209), and on Duet 2 Maestro (TMC2224 drivers). The TMC2660 drivers used on the Duet 2 WiFi and Duet 2 Ethernet support microstep interpolation, but only when microstepping is set to 16. In other configurations, specifying I1 has no effect.

## M374: Save height map

### Parameters

* **P"filename"** Name of the file to save to

### Usage

* M374
* M374 P"MyAlternateHeightMap.csv"

### Notes

This saves the grid parameters and height map into the specified file, or the default file **heightmap.csv** if no filename was specified. To load the height map automatically at startup, use command G29 S1 or M375 in the config.g file.

The G29 S0 command automatically saves the height map to file, therefore you do not need to use M374 after G29 S0.

## M375: Load height map

Loads the grid matrix file

### Parameters

* **P"filename"**

### Examples
<br>
<pre class="cblock">
M375
M375 P"MyAlternateHeightMap.csv"
</pre>

### Notes

Without parameters loads default grid (**sys/heightmap.csv**), and with specified filename attempts to load the specified grid. If not available will not modify the current grid.

In RepRapFirmware this command is equivalent to the G29 S1 command.

## M376: Set bed compensation taper

### Parameters

* **Hnnn** Height (mm) over which to taper off the bed compensation

### Examples
<br>
<pre class="cblock">
M376 H10
</pre>

### Notes

This command specifies that bed compensation should be tapered linearly over the specified height, so that full bed compensation is applied at Z=0 and no bed compensation is applied when Z is at or above that height. If H is zero or negative then no tapering is applied, so compensation is performed throughout the entire print.

RepRapFirmware does not adjust the extrusion factor to account for the layer height varying when tapered bed compensation is used. Therefore it is recommended that the taper height be set to at least 20x the maximum error in the height map, so that the maximum amount of the resulting over- or under- extrusion is limited to 5%.

## M400: Wait for current moves to finish

### Examples
<br>
<pre class="cblock">
M400
</pre>

### Notes

Finishes all current moves and and thus clears the buffer. That's identical to G4 P0.

## M401: Deploy z-probe

### Parameters

* **P** Probe number (RRF 3.01RC4 and later)

### Examples
<br>
<pre class="cblock">
M401
M401 P1
</pre>

This runs macro file **sys/deployprobe#.g** (where # is the probe number) if it exists, otherwise **sys/deployprobe.g** if it exists.

## M402: Retract z-probe

### Parameters

* **P** Probe number (RRF 3.01RC4 and later)

### Examples
<br>
<pre class="cblock">
M402
M402 P1
</pre>

### Notes

This runs macro file **sys/retractprobe#.g** (where # is the probe number) if it exists, otherwise **sys/retractprobe.g** if it exists.

## M404: Filament diameter

### Parameters

* **Nnnn** Filament diameter (in mm)
* **Dnnn** Nozzle diameter (in mm) (deprecated in 3.4-b1)

### Examples
<br>
<pre class="cblock">
M404 N1.75
M404 N3.0 D1.0 ; See note below about D parameter
</pre>

### Notes

Enter the nominal filament width (3mm, 1.75mm) or will display nominal filament width without parameters.

The 'D' parameter is used to properly detect the first layer height when files are parsed or a new print is being started. From RRF 3.4-b1 the D parameter is deprecated and no longer used in detecting the first layer height.

The values of this command are currently only used by the print monitor and only when the slicer reports the filament usage by volume instead of by length.

## M408: Report JSON-style response

**Deprecated in RRF 3.3 and later.** Use M409 instead to get response from Object Model, which provides more information.

### Parameters

* **Snnn** Response type (only used when R is zero or not present)
* **Rnnn** Response sequence number (see **seq** and **resp** in list below)

### Examples
<br>
<pre class="cblock">
M408 S0
</pre>

### Notes

This reports a JSON-style response by specifying the desired type using the 'S' parameter. The following response types are supported:

* Type 0 is a short-form response, similar to the response used by older versions of the web interface.
* Type 1 is like type 0 except that static values are also included.
* Type 2 is similar to the response provided by the web server for Duet Web Control.
* Type 3 is an extended version of type 2 which includes some additional parameters that aren't expected to change very frequently.
* Type 4 is an extended version of type 2 which may be used to poll for current printer statistics.
* Type 5 reports the current machine configuration.

Here is an example of a typical type 0 response:
<br>
<pre class="cblock">
{"status":"I","heaters":[25.0,29.0,28.3],"active":[-273.1,0.0,0.0],"standby":[-273.1,0.0,0.0],"hstat":[0,2,1],"pos":[-11.00,0.00,0.00],"extr":[0.0,0.0],"sfactor":100.00, "efactor":[100.00,100.00],"tool":1,"probe":"535","fanPercent":[75.0,0.0],"fanRPM":0,"homed":[0,0,0],"fraction_printed":0.572}
</pre>

The response is set as a single line with a newline character at the end. The meaning of the fields is:

**status**: I=idle, P=printing from SD card, S=stopped (i.e. needs a reset), C=running config file (i.e starting up), A=paused, D=pausing, R=resuming from a pause, B=busy (e.g. running a macro), F=performing firmware update
**heaters**: current heater temperatures, numbered as per the machine (typically, heater 0 is the bed)
**active**: active temperatures of the heaters
**standby**: standby temperatures of the heaters
**hstat**: status of the heaters, 0=off, 1=standby, 2=active, 3=heater fault. Heater 0 is normally the bed heater, heaters 1, 2.. are the extruder heaters.
**pos**: the X, Y and Z (and U, V, W if present) axis positions of the current tool (if a tool is selected), or of the print head reference point if no tool is selected
**extr**: the positions of the extruders
**sfactor**: the current speed factor (see M220 command)
**efactor**: the current extrusion factors (see M221 command), one value per extruder
**tool**: the selected tool number. A negative number typically means no tool selected.
**probe**: the Z-probe reading
**fanPercent**: the speeds of the controllable fans, in percent of maximum
**fanRPM**: the print cooling fan RPM
**homed**: the homed status of the X, Y and Z axes (and U, V, W if they exist), or towers on a delta. 0=axis has not been homed so position is not reliable, 1=axis has been homed so position is reliable.
**fraction_printed**: the fraction of the file currently being printed that has been read and at least partially processed.
**message**: the message to be displayed on the screen (only present if there is a message to display)
**timesLeft**: an array of the estimated remaining print times (in seconds) calculated by different methods. These are currently based on the proportion of the file read, the proportion of the total filament consumed, and the proportion of the total layers already printed. Only present if a print from SD card is in progress.
**seq**: the sequence number of the most recent non-trivial GCode response or error message. Only present if the R parameter was provided and the current sequence number is greater than that.
**resp**: the most recent non-trivial GCode response or error message. Only present if the R parameter was provided and the current sequence number is greater.

The type 1 response comprises these fields plus some additional ones that do not generally change and therefore do not need to be fetched as often. The extra fields include:

**myName**: the name of the printer
**firmwareName**: the name of the firmware, e.g. "RepRapFirmware" or "Smoothieware"
**geometry**: one of "cartesian", "delta", "corexy, "corexz" etc.
**axes**: the number of axes
**volumes**: the number of SD card slots available
**numTools**: the number of available tools numbered contiguously starting from 0

The fields may be in any order in the response. Other implementations may omit fields and/or add additional fields.

For a more detailed comparison of type 2 - 5, see [Status Responses](https://github.com/Duet3D/RepRapFirmware/wiki/JSON-responses){target=_blank}.

PanelDue currently uses only M408 S0 and M408 S1.

## M409: Query object model

### Parameters

* **K"key"** Key string, default empty
* **F"flags"** Flags string, default empty

### Examples
<br>
<pre class="cblock">
M409 K"move.axes" F"f"   ; report all frequently-changing properties of all axes
M409 K"move.axes[0]" F"v,n,d4"  ; report all properties of the first axis, including values not normally reported, to a maximum depth of 4
M409 K"move.axes[].homed"  ; for all axes, report whether it is homed
M409 K"#move.axes"     ; report the number of axes
M409 F"v"          ; report the whole object model to the default depth
</pre>

### Notes

The key string is just the path to the object model variables wanted, with the following extensions:

* An element that is an array may be followed by either [*number*] to select just one element, or by to select all elements and report the results as an array
* The path may be preceded by # in which case the path must refer to an array and just the number of array elements is returned

An empty key string selects the entire object model. Note, the entire object model may be very large, so there is typically insufficient buffer space to construct a JSON response that represents the whole object model. For this reason, RepRapFirmware sets a default maximum depth of 1 if the key string is empty or not present and the 'f' flag is not included in the flags string.

The flags string may include one or more of the following letters:

* f: return only those values in the object model that typically change frequently during a job
* v: Verbose: include values that are rarely needed and not normally returned
* n: include fields with null values
* o: include obsolete fields (v3.3 and newer)
* d: limit the depth of the reported tree to the specified number following the letter d. Objects at the maximum depth will be returned as {}.

The flags string may optionally use spaces or commas to separate the individual flags

The response is a JSON object of the following form:
{"key":"*key*","flag'":"*flags*","result":*object-value*}

If the key string is malformed or refers to a property that does not exist in the object model, the result field is **null**.

For details of the Object Model supported by RepRapFirmware, see [Object Model of RepRapFirmware](https://github.com/Duet3D/RepRapFirmware/wiki/Object-Model-Documentation){target=_blank}.

## M425: Configure backlash compensation

Support in RRF 3.5beta2 and later.

### Parameters

* **X,Y,Z,A,B...** Backlash in mm for the specified axis motor
* **Snn** (optional) Distance multiplier, default 10

This command tells RRF to insert additional steps when the specified motors reverse direction, to compensate for backlash. The additional steps are inserted over a distance of at least the distance multiplier times the amount of backlash. For example, a multiplier of 10 and a backlash of 0.5mm would mean that the backlash steps would be inserted over as many moves as needed to make up at least 5mm of movement. If the multipler is set too low then missed steps could result.

The X,Y,Z... parameters refer to individual motors or sets of motors, not Cartesian axes. For example, on a CoreXY machine the X parameter defines the backlash compensation applied to the X motor, and the Y parameter defines the backlash compensation applied to the Y motor.

M425 with no parameters reports the current backlash compensation settings. The settings can also be read from the object model in `move.axes[].backlash` and `move.backlashFactor`.

### Examples
<br>
<pre class="cblock">
M425 X0.15 Y0.23 S5
</pre>

### Limitations

* Backlash compensation isn't applied when Delta kinematics are used.
* If M584 is used to configure multiple drivers for an axis, the same backlash compensation is applied to all of them.
* Backlash compensation isn't applied to moves that level the bed after running G32
* Backlash compensation isn't a substitute for good mechanics. In particular, if the machine is a 3D printer then extrusion will continue while the backlash is taken up, which may result in a seam where the direction is changed.

## M450: Report Printer Mode

### Parameters

* none

### Examples
<br>
<pre class="cblock">
M450
</pre>

### Notes

Printers can be used for different task by exchanging the toolhead. Depending on the tool, a different behavior of some commands can be expected. This command reports the current working mode. Possible responses are:

PrinterMode:FFF

PrinterMode:Laser

PrinterMode:CNC

The default at power up is FFF.

## M451: Select FFF Printer Mode

### Parameters

* none

### Examples
<br>
<pre class="cblock">
M451
</pre>

### Notes

Switches to Fused Filament Fabrication mode for filament printing.

## M452: Select Laser Device Mode

### {.tabset}

#### RepRapFirmware 3.x

##### Parameters

* **C"name"** Pin name(s) and optional inversion status, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank}. A leading '!' character inverts the input or output.
* **Rnnn** The value of the S parameter in G1 commands that corresponds to full laser power, default 255
* **Sn** 1= laser power is sticky across G1 commands, 0 (default) = laser is off when executing G1 commands that have no S parameter
* **Fnnn** The PWM frequency used to drive the laser - default is 500Hz

##### Example
<br>
<pre class="cblock">
; old code (RRF 2.x):
M452 P3 I1 F100  ; laser uses heater3 pin, PWM frequency 100Hz<br>
; new code (RRF 3.x):
M452 C"!exp.heater3" F100 ; laser uses heater3 pin inverted, PWM frequency 100Hz
</pre>

##### Notes

* Switches to laser mode. This mode enables handling of a laser pin and makes sure that the laser is only activated during G1 moves if laser was enabled (using G1 Snn moves) or E is increasing (using [M571](/User_manual/Reference/Gcodes/M571){target=_blank}). G0 moves should never enable the laser.
* **Very important!** If you use M452 to put your machine into Laser mode and are upgrading from RepRapFirmware **v2.01 or earlier**, you must replace all S parameters in G0/G1 commands in homing files etc. with H parameters. This is because S is now used to control laser power.
* In laser mode, it is valid in a Gcode file to send G0 or G1 on one line, and then just send co-ordinates on the following lines.
* In RRF3, the P and I parameters are removed. Use the C parameter to select the laser control pin instead.
* M3 and M5 no longer turn the laser on and off; use G1 Snn moves to control laser power.
* See also [Configuring RepRapFirmware for a laser engraver/cutter](/User_manual/Machine_configuration/Configuration_laser){target=_blank}.

#### RepRapFirmware 2.x

##### Parameters

* **Pnnn** Logical pin number used to control the laser
* **In** Invert (I1) or don't invert (I0, default) the output polarity
* **Rnnn** The value of the S parameter in G1 and/or M3 commands that corresponds to full laser power, default 255
* **Sn** 1= laser power is sticky across G1 commands, 0 (default) = laser is off when executing G1 commands that have no S parameter
* **Fnnn** The PWM frequency used to drive the laser - default is 500Hz

##### Examples
<br>
<pre class="cblock">
M452 P2 R255 F200 ; switch to laser mode using the heater 2 (E1 heater) output pins to control the laser
</pre>

##### Notes

* Switches to laser mode. This mode enables handling of a laser pin and makes sure that the laser is only activated during G1 moves if laser was enabled (using M3 Snn or G1 Snn moves) or E is increasing (using [M571](/User_manual/Reference/Gcodes/M571){target=_blank}). G0 moves should never enable the laser. 
* M3/M5 can be used to enable/disable the laser for moves. 
* Logical pin numbers for the P parameter are as defined for the M42 and M208 commands. If a heater or fan output is used to control the laser, you must disable the corresponding heater (see M307) or fan (see M106) first.
* **Very important!** If you use M452 to put your machine into Laser mode and are upgrading from RepRapFirmware **v2.01 or earlier**, you must replace all S parameters in G0/G1 commands in homing files etc. with H parameters. This is because S is now used to control laser power.
* See also [Configuring RepRapFirmware for a laser engraver/cutter](/User_manual/Machine_configuration/Configuration_laser){target=_blank}.

## M453: Select CNC Device Mode

### {.tabset}

#### M453 in RepRapFirmware 3.3beta2 and later

Switches to CNC mode. All other parameters have been removed and moved into M950.

##### Parameters

none

##### Examples
<br>
<pre class="cblock">
; Old code
M453 S0 T1 C"!exp.heater3" R12000 ; Assign tool 1 to spindle index 0, with PWM pin on heater 3 and 12000 RPM achieved at full PWM<br>
; New code
M950 R0 C"!exp.heater3" L12000 ; Create spindle index 0, with PWM pin on heater 3 and 12000 RPM achieved at full PWM
M563 P1 S"Spindle 1" R0 ; Create tool 1 with spindle 0 and call it "Spindle 1"
M453
</pre>

<br>
<pre class="cblock">
; Old code
M453 S0 T1 C"exp.heater3+exp.heater4+exp.heater5" Q100 ; spindle PWM on heater 3 pin, on/off on heater 4, reverse/forward on heater 5, PWM frequency 100Hz<br>
; New code
M950 R0 C"exp.heater3+exp.heater4+exp.heater5" Q100
M563 P1 S"Spindle 1" R0 ; Create tool 1 with spindle 0 and call it "Spindle 1"
M453
</pre>

#### M453 in RepRapFirmware 3.2

##### Parameters

* **Snnn** (optional) Spindle index, defaults to 0. Duet 2 supports 4 spindles max
* **C"aaa+bbb+ccc"** Names of the ports used to drive the spindle motor. "aaa" is the PWM port used to control the speed. "bbb" (optional) is the digital port used to turn the spindle motor on. "ccc" (optional) is the name of the port used to command reverse instead of forward spindle rotation.
* **Rbbb** or **Raaa:bbb** (optional) RPM values that are achieved at zero PWM and at maximum RPM. Used to convert the S parameter in M3 and M4 commands to a PWM value.
* **Qnnn** (optional) The PWM frequency to use
* **Tnnn** (optional) Assign spindle to a tool allowing better control in DWC

##### Examples
<br>
<pre class="cblock">
M453 C"exp.heater3+exp.heater4+exp.heater5" Q100 T1 ; spindle PWM on heater 3 pin, on/off on heater 4, reverse/forward on heater 5, PWM frequency 100Hz
</pre>

#### M453 in RepRapFirmware 3.0 and 3.1.x

##### Parameters

* **Snnn** (optional) Spindle index, defaults to 0. Duet 2 supports 4 spindles max
* **C"fff+rrr"** Names of the ports used to drive the spindle motor in clockwise and counterclockwise directions. Omit the "+rrr" part if the spindle turns clockwise only.
* **Rnnn** Spindle RPM that is achieved at full PWM. Used to convert the S parameter in M3 and M4 commands to a PWM value.
* **Fnnn** (optional) The PWM frequency to use
* **Tnnn** (optional) Assign spindle to a tool allowing better control in DWC

##### Examples
<br>
<pre class="cblock">
M453 C"exp.heater3+exp.heater4" F100 T1 ; spindle forward/reverse on heater 3&4 pins, PWM frequency 100Hz
</pre>

#### M453 in RepRapFirmware 1.x and 2.x

##### Parameters

* **Snnn** (optional) Spindle index, defaults to 0. Duet 2 supports 4 spindles max
* **Pfff:rrr** Logical pin numbers used to drive the spindle motor in clockwise and counterclockwise directions. Omit the ":rrr" part if the spindle turns clockwise only. (Not supported in RRF3, see notes)
* **In** Invert (I1) or don't invert (I0, default) the output polarity. (Not supported in RRF3, see notes)
* **Rnnn** Spindle RPM that is achieved at full PWM. Used to convert the S parameter in M3 and M4 commands to a PWM value.
* **Fnnn** (optional) The PWM frequency to use
* **Tnnn** (optional) Assign spindle to a tool allowing better control in DWC

##### Examples
<br>
<pre class="cblock">
M453 P2 R5000 ; switch to CNC mode using heater 2 (E1 heater) pins to control the spindle motor
</pre>

Logical pin numbers for the P parameters are as defined for the M42 and M208 commands. If you wish to assign a heater or fan output to control the spindle motor as in the above example, you must first disable the corresponding heater (see M307) or fan (see M106).

### Notes

* Switches to CNC mode. In this mode M3/M4/M5 control the pins defined for the milling device. By default, no output is assigned to a spindle motor, so it must be configured.
* In CNC mode, it is valid in a Gcode file to send G0 or G1 on one line, and then just send co-ordinates on the following lines.
* In CNC mode, comments can be enclosed in a **single** pair of parentheses, e.g. `(comment)`. Comments cannot include double or nested parentheses, e.g. `(comment (a bit more comment))`, and they must start and end on the same line. This complies with NIST Gcode interpreter guidelines. e.g. `G28 (here come the axes to be homed) X Y`
* When using [Gcode meta commands](/User_manual/Reference/Gcode_meta_commands), sub-expressions may be enclosed in { } or in ( ). However, standard CNC GCode uses ( ) to enclose comments (see note above). So in CNC mode, RepRapFirmware treats ( ) as enclosing subexpressions when they appear inside { } and as enclosing comments when they do not. Therefore, when RepRapFirmware is running in CNC mode, any use of ( ) to enclose a subexpression or function parameter list must be within an expression enclosed in { }.
* See also [Configuring RepRapFirmware for a CNC machine](/User_manual/Machine_configuration/Configuration_CNC){target=_blank}.

## M470: Create Directory on SD-Card

### Parameters

* **P"name"** Name of directory to create

### Examples
<br>
<pre class="cblock">
M470 P"/sys/config.d"
</pre>

### Notes

This will create a new directory on the SD-Card. If not otherwise specified the default root should be the first/internal SD-Card.

Supported in RRF >= 2.03.

## M471: Rename File/Directory on SD-Card

Supported in RRF >= 2.03.

### Usage

* M471 S"source/name" T"dest/name" D1

### Parameters

* **S"name"** Name of existing file/directory
* **T"name"** New name of file/directory
* **Dnnn** Setting this to 1 will delete any existing file that matches the T parameter value

### Examples
<br>
<pre class="cblock">
M471 S"/sys/config-override.g" T"/sys/config-override.g.bak"
</pre>


### Notes

Rename or move a file or directory. Using the D parameter will delete any existing file with the target name. Renaming or moving across directories is possible though not from one SD-Card to another.

## M472: Delete File/Directory on SD-Card

Supported in RRF >= 3.5.

### Usage

* M472 P"filename"

### Parameters

* **P"name"** Name of file/directory
* **Rnnn** Delete directory recursively (defaults to `0`)

### Examples
<br>
<pre class="cblock">
M472 P"/sys/foobar" R1
</pre>

## M486: Object cancellation

Supported in RRF 3.1 and later.

### Parameters

* **Tnn** Tell the firmware how many objects there are
* **S[index]** Inserted in GCode to indicate which object is being printed
* **Ann** Used in conjunction with S parameter, names an object
* **Pnn** Cancel object nn. First object is 0.
* **Unn** Uncancel object nn. First object is 0.
* **C** Cancel current object

### Examples
<br>
<pre class="cblock">
M486 T12               ; Total of 12 objects (otherwise the firmware must count)
M486 S3                ; Indicate that the 4th object is starting now
M486 S3 A"cube copy 3" ; Indicate that the 4th object is starting now and name it
M486 S-1               ; Indicate a non-object, purge tower, or other global feature
M486 P10               ; Cancel object with index 10 (the 11th object)
M486 U2                ; Un-cancel object with index 2 (the 3rd object)
M486 C                 ; Cancel the current object (use with care!)
M486                   ; List the objects on the build plate
</pre>

### Descripton

This provides an interface to identify objects on the print bed and cancel them. Basic usage: Use **M486 Tnn** to tell the firmware how many objects there are, so it can provide an LCD interface. (Otherwise the firmware counts them up in the first layer.) In every layer of your GCode, preface each object's layer slice with **M486 S[index]** to indicate which object is being printed. The index should be zero-based. To cancel the first object, use **M486 P0**; to cancel the 5th object use **M486 P4**; and so on. The "current" object is canceled with **M486 C**.

GCodes associated with the canceled objects are no longer printed. Firmware supports this feature by ignoring G0-G3/G5 moves in XYZ while updating F and keeping the E coordinate up-to-date without extruding.

Slicers should number purge towers and other global features with a negative index (or other flag) to distinguish them from regular print objects, since it is important to preserve color changes, purge towers, and brims.

In RepRapFirmware, if the GCode file being printed contains object label comments (e.g. using the "Label objects" option in PrusaSlicer) then it is not necessary to use M486 S commands to indicate which object is being printed. Objects on the build plate will be numbered from 0 in the order in which their labels first appear in the file.

If you do use M486 S commands in the GCode file instead of object label comments, then RepRapFirmware provides an optional A parameter to the M486 S command to allow objects to be named. The name of each object need only be specified once.

For the benefit of Duet Web Control and other user interfaces, the list of objects on the build plate known to RRF may also be retrieved from the object model using M409, including their names (if available) and approximate locations.

## M500: Store parameters

Implemented in RepRapFirmware 1.17 and later.

### Parameters

* **Pnn** or **Pnn:nn** Stores additional parameters. P31 stores G31 Probe status parameters; P10 stores G10/M568 tool offsets.

### Examples
<br>
<pre class="cblock">
M500
M500 P31
M500 P10
M500 P10:31
</pre>

### Description

Save current parameters to the sys/config-override.g on the SD card, similar to other firmware's storing to EEPROM. The parameters stored are:

* M307 auto tune results
* PID parameters, if you used M301 to override the auto tune PID settings
* Delta printer M665 and M666 settings
* Any M208 axis limits that were determined using a G1 S3 move
* If the P31 parameter is used, the G31 trigger height, trigger value and X and Y offsets for each possible Z probe type (in older firmware versions the G31 parameters are stored even if the P31 parameter is not present)
* If the P10 parameter is present, the G10 tool offsets

Ensure that M501 is at the end of config.g in order for the values in config-override.g to be loaded on startup.

## M501: Read stored parameters

### Parameters

* None

### Examples
<br>
<pre class="cblock">
M501
</pre>

### Description

Set the active parameters to those stored in sys/config-override.g on the SD card. This command should be included near the end of the config.g file so that the stored parameters will be loaded at startup. It can also be used to revert parameters to the values stored in config-override.g after experimenting with them.

## M502: Revert stored parameters

### Examples
<br>
<pre class="cblock">
M502
</pre>

### Description

This sets all machine parameters to the values defined in config.g, ignoring the config-override.g file so that any changes that were saved by M500 are not applied. It does this by running config.g but ignoring any M501 commands that it contains.

M502 does not clear or reset the config-override.g file; so next time the machine is started, the values that were saved by M500 will once again be applied. If you want to cancel the changes saved by M500 permanently, you can run M502 and then M500.

## M503: Print settings

### Examples
<br>
<pre class="cblock">
M503
</pre>

### Description

This command asks the firmware to reply with the current print settings stored in sys/config.g. The reply output includes the GCode commands to produce each setting. For example, the Steps Per Unit values are displayed as an M92 command.

### Notes

The output may be truncated if it is too long. M503 does **not** include values stored in config-override.g.

## M505: Set configuration file folder

### Parameters

* P"name" ; name of folder, default path is the existing sys path if a relative path is given

### Examples
<br>
<pre class="cblock">
M505 P"experimental" ; change config file path from /sys/ to /sys/experimental/
</pre>

### Description

Following this command, files that would normally be fetched from /sys/ (for example, homing files and system macro files in RepRapFirmware) are fetched from the specified folder instead. Any such files that are already being executed will continue to run.

This command can be used to allow multiple configurations to be maintained easily. In RepRapFirmware the file /sys/config.g can contain just these two lines:
<br>
<pre class="cblock">
M505 P"config1"
M98 P"config.g"`
</pre>

The first line changes the config file folder to /sys/config1 and the second one executes file config.g in that folder. To select an alternative configuration, only the first line needs to be edited.

## M540: Set MAC address

### Parameters

* **Pnnn** The MAC address

### Examples
<br>
<pre class="cblock">
M540 PDE:AD:BE:EF:CA:FE
</pre>

### Description

Sets the [MAC address](http://en.wikipedia.org/wiki/MAC_address){target=_blank} of the printer. This should be done before any other network commands. The MAC address is six one-byte hexadecimal numbers separated by colons. Only works on Ethernet-equipped Duet mainboards, in standalone mode (i.e. not Duets with WiFi or Duet boards with SBC).

### Notes

* On WiFi-equipped Duet boards (Duet 3 Mini 5+ WiFi and Duet 2 WiFi) the MAC address is unique and set on the WiFi Module so this command has no effect. 
* The default MAC address on a Ethernet-equipped Duet boards is generated from the unique processor ID so there is normally no need to change it.
* All devices running on the same network should have different MAC addresses. For your printers, changing the last digit is sufficient.
* In SBC mode, this command should be in dsf-config.g, NOT config.g.

## M550: Set Name

### Parameters

* **P"nnn"** Machine name

### Examples
<br>
<pre class="cblock">
M550 P"Godzilla"
</pre>

### Description

Sets the name of the printer to (in the case of the above example) Godzilla. The name can be any string of printable characters except ';', which still means start comment. The name shows at the top of the DWC page.

The machine name is also used to allow local network discovery using **mDNS local network discovery**. Rather than remembering the ip address of the printer to connect to, or having to find it if assigned by DHCP (mDNS works both with fixed ip address and DHCP), you can use it's name. Using the example name above, in your browser connect to the DWC with `http://Godzilla.local`.

### Notes

* Quotation marks around the machine name are mandatory in RRF3, but discretionary in earlier firmware versions.
* In SBC mode, this command should be in dsf-config.g, NOT config.g.
* Using the machine name to access the machine on the network relies on mDNS. This needs to be supported on the device trying to connect. See a longer description about [mDNS support here](/User_manual/Machine_configuration/Networking#a-note-about-mdns-local-network-discovery){target=_blank}.
* The machine name is also used as the NetBIOS name, which can help to identify the Duet on a network. This is only supported on Duet 2 WiFi and legacy Duet 0.6/0.85.
* Both the mDNS and NetBIOS name are limited to 15 characters. If you use a longer name, the mDNS name will be the first 15 characters, eg if the Duet name is "3DPrinterWithVeryLongName", you should still be able to connect to "3DPrinterWithVe.local".

## M551: Set Password

### Parameters

* **P"nnn"** Password

### Examples
<br>
<pre class="cblock">
M551 P"my-very-secret-word"
</pre>

### Description

The code 'P' is not part of the password. Note that as this is sent in clear it does not (nor is it intended to) offer a security. But on machines that are (say) on a network, it might prevent idle messing about. The password can contain any printable characters except ';', which still means start comment.

### Notes

If the specified password differs from the default one (i.e. reprap), the user will be asked to enter it when a connection is established.

Quotation marks around the password are mandatory in RRF3, but discretionary in earlier firmware versions.

## M552: Set IP address, enable/disable network interface

### {.tabset}

#### WiFi interfaces (Duet 2/3 WiFi)

##### Parameters

* **Innn** (Optional) Number of the network interface to manage (defaults to 0). Only needed if the board supports more than one network interface, such as Duet 3 MB6HC revision 1.02 or later with the optional WiFi interface. On that board, I0 is the Ethernet interface and I1 is the WiFi interface.
* **P"ssid"** (optional, RepRapFirmware 1.20 and later) SSID of network to connect to. The SSID and password must already have been registered using M587. If this parameter is not present, the WiFi will try to connect to the strongest network that is broadcasting its SSID and whose SSID has been registered using M587.
* **Snnn** 0 = disable networking, 1 = enable networking as a client, 2 = enable networking as an access point , -1 = disable WiFi module

##### Examples
<br>
<pre class="cblock">
M552 S1 P"MyNetwork"
</pre>

Enables networking as a client, and joins the network with the SSID 'MyNetwork', using the parameters (password, IP/gateway address, netmask) configured in [M587](/User_manual/Reference/Gcodes/M587){target=_blank}.

##### Notes

* Also works with the WiFi interface on an attached SBC. See M587 for configuration limitation.
* On Duet boards with WiFi interfaces running firmware 1.19 and later, the IP address is set in the M587 command when you configure the access point details.
* In SBC mode, sending this command makes a persistent change. It does not need to be added to dsf-config.g. It should NOT be included in config.g.

#### Ethernet interfaces (Duet 2/3 Ethernet and 06/085)

##### Parameters

* **Innn** (Optional) Number of the network interface to manage (defaults to 0).
* **Pnnn** IP address, 0.0.0.0 means acquire an IP address using DHCP
* **Snnn** 0 = disable networking, 1 = enable networking
* **Rnnn** (Optional) HTTP port, default 80 (Deprecated, RepRapFirmware 1.17 and earlier only)

##### Examples

**Duet 2 Ethernet:**
<br>
<pre class="cblock">
M552 S1 P192.168.1.14
</pre>

Sets the IP address of the machine to (in this case) 192.168.1.14. If the S parameter is not present then the enable/disable state of the network interface is not changed.

**Duet 3 in SBC mode:**

* M552 I1 S1 P0.0.0.0 ; set the second interface on the SBC to use DHCP and enable it.

The I1 setting here specifies the second network interface on the SBC. This uses the [DuetPi Management Plugin](https://github.com/Duet3D/DuetSoftwareFramework/tree/v3.3-dev/src/DuetPiManagementPlugin#readme){target=_blank} (installed by default from RRF 3.3 onwards) to set the address on the SBC. To determine which interface is which on the SBC the object model explorer can be used to see the current settings of each interface.

##### Notes

* M552 with no parameters reports the current network state and IP address.
* In firmware 1.18 and later the HTTP port address is set using the M586 command, so the R parameter of this command is no longer supported.
* In SBC mode, sending this command makes a persistent change. It does not need to be added to dsf-config.g. It should NOT be included in config.g.

## M553: Set Netmask

### Parameters

* **Innn** (Optional) Number of the network interface to manage (defaults to 0)
* **Pnnn** Net mask

### Examples
<br>
<pre class="cblock">
M553 P255.255.255.0
</pre>

Sets the network mask of the RepRap machine to (in this case) 255.255.255.0. 

### Notes

* In SBC mode, sending this command makes a persistent change. It does not need to be added to dsf-config.g. It should NOT be included in config.g.
* A restart may be required before the new network mask is used. 
* If no 'P' field is specified, this echoes the existing network mask configured.
* DuetWifiFirmware versions 1.18 and earlier do not support setting the network mask manually.

## M554: Set Gateway and/or DNS server

### Parameters

* **Innn** (Optional) Number of the network interface to manage (defaults to 0)
* **Pnnn** Gateway
* **Snnn** (Optional) DNS server (only supported by DSF 3.3 with DuetPi system config plugin)

### Examples
<br>
<pre class="cblock">
M554 P192.168.1.1
</pre>

Sets the Gateway IP address of the RepRap machine to (in this case) 192.168.1.1. 

### Notes

* In SBC mode, sending this command makes a persistent change. It does not need to be added to dsf-config.g. It should NOT be included in config.g.
* A restart may be required before the new gateway IP address is used. 
* If no 'P' field is specified, this echoes the existing Gateway IP address configured.
* DuetWifiFirmware versions 1.18 and earlier do not support setting the gateway address.

## M555: Set compatibility

### Parameters

* **Pnnn** Emulation type

### Examples
<br>
<pre class="cblock">
M555 P1
</pre>

### Description

Set the firmware to a mode where its input and (especially) output behaves similar to other established firmware. The value of the 'P' argument is:

| value | Firmware |
|:---|:---|
| 0 | RepRap_Firmware |
| 1 | RepRap_Firmware |
| 2 | Marlin |
| 3 | Teacup |
| 4 | Sprinter |
| 5 | Repetier |
| 6 | nanoDLP (RRF 2.02RC6 and later) |

Currently, only RepRapFirmware, Marlin and nanoDLP emulations are supported.

## M556: Axis skew compensation

### Parameters

* **Snnn** Height of the measured distances
* **Xnnn** Deviation in X direction, or skew factor when S=1
* **Ynnn** Deviation in Y direction, or skew factor when S=1
* **Znnn** Deviation in Z direction, or skew factor when S=1
* **Pnnn** Apply XY compensation to Y axis instead of X (defaults to 0, requires RRF 3.2-b4 or newer)

### Examples
<br>
<pre class="cblock">
M556 S100 X0.7 Y-0.2 Z0.6
M556 ; reports the axis compensation in use
Axis compensations - XY: 0.00700, YZ: -0.00200, ZX: 0.00600
</pre>

### Description

This tells software the tangents of the angles between the axes of the machine obtained by printing then measuring a test part. When used without parameters, reports the axis skew compensation factors in use.

### Notes

* The S parameter is the length of a triangle along each axis in mm. 
* The X, Y and Z figures are the number of millimeters of the short side of the triangle that represents how out of true a pair of axes is. The X figure is the error between X and Y, the Y figure is the error between Y and Z, and the Z figure is the error between X and Z. Positive values indicate that the angle between the axis pair is obtuse, negative acute.
* If you have calcuated the skew factor by other means, use S1 in M556 and the XYZ parameters are the skew factors.
* Printable parts for calibrating the deviation from orthogonality can be found on the [RepRapPro Github repository](https://github.com/reprappro/RepRapFirmware/tree/master/STL){target=_blank}. 
* For an explanation of the measuring process and alternative methods, see [Orthogonal axis compensation with M556](/User_manual/Tuning/Orthogonal_axis_compensation){target=_blank}.

## M557: Set Z probe point or define probing grid

### {.tabset}

#### Define G29 probe grid 

##### Parameters 

* **Xaaa:bbb** Minimum and maximum X coordinates to probe
* **Yaaa:bbb** Minimum and maximum Y coordinates to probe
* **X,Y,U,V,W,A,B,C...aaa:bbb** Minimum and maximum coordinates of an arbitrary axis (except Z) to probe (RRF >=3.3beta2)
* **Raaa** Radius to probe
* **Saaa** Probe point spacing (RepRapFirmware 1.19beta10 and later also support **Saaa:bbb**)
* **Pnn** or **Pxx:yy** (RRF 2.02 and later) Number of points to probe in the X and Y axis directions (alternative to specifying the probe point spacing)

All values in mm.

##### Examples
<br>
<pre class="cblock">
M557 X0:200 Y0:220 S20
M557 X0:100 Y0:120 S50:60
M557 R150 S15
</pre>

##### Description

Defines the grid for [G29 Mesh Bed probing](/User_manual/Reference/Gcodes/G29){target=_blank}. For Cartesian printers, specify minimum and maximum X and Y values to probe and the probing interval. For Delta printers, specify the probing radius. If you define both, the probing area will be the intersection of the rectangular area and the circle. There is a firmware-dependent maximum number of probe points supported. Currently this is 441 for the Duet 2 and Duet 3 (enough for a 21x21 grid), and 121 on the Duet 06/085 (enough for a 11x11 grid).

##### Notes

In RRF >= 3.3beta2 it is possible to use an arbitrary axes pair for probing, e.g. X-A or U-C. When using **Raaa** to define a radius this will default to X-Y.

#### Define G32 probe points

##### Parameters

Deprecated and not supported in firmware 1.18 and later. Cartesian/CoreXY printers only,

* **Pnnn** Probe point number
* **Xnnn** X coordinate
* **Ynnn** Y coordinate

##### Examples
<br>
<pre class="cblock">
M557 P1 X30 Y40.5
</pre>

##### Description

Defines the points for for G32 bed probing. The P value is the index of the point (indices start at 0) and the X and Y values are the position to move extruder 0 to to probe the bed. An implementation should allow a minimum of three points (P0, P1 and P2). This just records the point coordinates; it does not actually do the probing. See [G32](/User_manual/Reference/Gcodes/G32){target=_blank}. Defining the probe points in this way is deprecated in RepRapFirmware, you should define them in a bed.g file instead.

## M558: Set Z probe type

### {.tabset}

#### RepRapFirmware 3.x

##### Parameters

* **Pnnn** Z probe type
* **C"name"** Specifies the input pin and the optional modulation pin. This parameter is mandatory, except for probe type 0 (manual probing) and 10 (Z motor stall detection).
* **Hnnn** Dive height (mm). The height above the trigger height from which probing starts.
* **Fnnn** or **Fnnn:nnn** Feed rate (i.e. probing speed, mm/min). Initial fast probe followed by probing at second speed supported in RRF 3.3 and later.
* **Tnnn** Travel speed to and between probe points (mm/min). This is also the Z lift speed after probing. The corresponding axis speed limits set by M203 will be used instead if they are lower.
* **Knnn** Sets/selects Z probe number. If there is no K parameter then 0 is used. You can ignore this parameter if you have only one Z probe.
* **Rnnn** Z probe recovery time before the probing move is started, default zero (seconds). This is to allow the probe to settle after executing a travel move to the coordinates to probe.
* **Annn** Maximum number of times to probe each point, default 1. Maximum, as of 2.03, is 31. Setting M558 A parameter to anything >31 set it to 0 instead of to 31
* **Snnn** Tolerance when probing multiple times, default 0.03mm
* **Bn** If 1, turn off all heaters while probing, default (B0) leaves heaters on.

##### Order dependency

M558 must come before G31.

##### Examples
<br>
<pre class="cblock">
M558 P5 C"e0stop" H5 F120 T3000  ; Z probe connected to Duet 2 E0 endstop input<br>
M574 Z0 P"nil" ; (RRF 3.0 on Duet 2 ONLY) no Z endstop switch, free up Z endstop input
M558 P5 C"zstop" H5 F120 T3000  ; Z probe connected to Duet 2 Z endstop input<br>
; BL Touch on Duet 3 Mini 5+
M950 S0 C"io3.out" ; servo/gpio 0 is io3.out pin
M558 P9 C"io3.in" H5 F500:120 T3000 ; BLTouch connected to io3.in pin
...
M280 P0 S10 ; send control signal to BLTouch through servo/gpio 0<br>
; BLTouch on Duet WiFi
M950 S0 C"exp.heater3" ; create servo/gpio 0 on heater 3 pin on expansion connector
M558 P9 C"^zprobe.in" H5 F120 T3000  ; BLTouch connected to Z probe IN pin
...
M280 P0 S10 ; send control signal to BLTouch through servo/gpio 0<br>
; For the Duet Smart Effector on Duet 2
M558 P8 C"zprobe.in+zprobe.mod" R0.4 F1200 ; zprobe.mod is the programming pin for M672
</pre>

##### Notes

A Z probe may be a switch, an IR proximity sensor, or some other device. The **P** parameter selects which to use:

* P0 indicates that no Z probe is present. Whenever Z probing is commanded, you will be prompted to jog the Z axis until the nozzle is just touching the bed and then signal completion.
* P1 specifies an unmodulated or smart IR probe, or any other probe type that emulates one (probe output is an analog signal that rises with decreasing nozzle height above the bed). If there is a control signal to the probe, it is driven high when the probe type is P1.
* P2 specifies a simple modulated IR probe, where the modulation is commanded directly by the main board firmware using the control signal to the probe.
* P3 is similar to P1 but drives the control signal to the probe low. This may be used to switch between different Z probes.
* P5 selects a switch by default (normally closed) for bed probing between the In and Gnd pins of the IO connector (Duet 3) or Z-probe connector (Duet 2).
* P8 is as P5 but is unfiltered, for faster response time.
* P9 is as P5 but for a BLTouch probe that needs to be retracted and redeployed between probe points.
* P10 means use Z motor stall detection as the Z probe trigger.
* P11 means a scanning Z probe with an analog output (supported from RRF 3.5.0-beta.4). Such probes must be calibrated before use (see M558.1).

Z probe types 4, 6 and 7 (used in RRF 2.x) are no longer supported. Instead, use type 5 (filtered digital) or 8 (unfiltered digital) and use the C parameter to specify the input. 

M558 with P parameter deletes the existing probe with that K number (if any) and creates a new Z probe. This resets the G31 values for that probe to default values.

If your Z probe is connected to the Z endstop input, **in RRF 3.0 on Duet 2 boards only** (not in RRF 3.01 and later, and not in RRF 3.0 on Duet 3), that input is by default pre-assigned to be used by the Z endstop, so you must free it up first with `M574 Z0 P"nil"`.

Only one Type 2 probe can be configured, and if using Duet 3 then it must be connected to the Duet 3 main board, not to a CAN-connected expansion or tool board.

The **C** parameter specifies the input pin and the optional modulation pin. See [Pin names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank} for a list of available pins and their names to use. Invert the input by prefixing the input pin with ! character, when using an NPN output inductive or capacitive sensor or using an NO switch (not recommended, use a NC switch instead). The pullup resistor on the Z probe input is disabled by default. Enable it by prefixing the input pin (C parameter) with the ^ character. Enable pullup resistor with ^ if using Duet 2, running RRF3, using the Z probe input pin, and the probe type is a switch or BLTouch.

The **H** parameter defines the Z probe dive height, which is the height above the trigger height from which probing starts. The default is 3mm or 5mm depending on firmware version. You may wish to increase it during initial calibration. When using mesh bed compensation or running G30 commands with specified XY coordinates (for example from the bed.g file), the firmware moves the Z probe to this height above where it expects the bed to be before commencing probing. The maximum depth of probing from this position is twice the dive height. A large dive height will tolerate a very uneven bed or poor calibration. A small dive height will make probing faster, because the Z probe has less distance to travel before reaching the bed. Default value if omitted is 5mm.

From RRF 3.3 you can provide two **F** parameters instead of one, where the second is lower than the first, for example F1000:500. When doing a plain G30 command, an additional probe will be done using the first speed to establish the approximate bed position, before one or more additional probes are done using the second speed. The first speed will not be used when probing at a defined point or when mesh bed probing.

The **A** and **S** parameters control multiple probing. Probing is repeated until two consecutive probe attempts produce results that differ by no more than the S parameter; then the average of those two results is used. For example, S-1 would force averaging. However, if the number of attempts specified by the A parameter is reached without getting two consecutive results within tolerance of each other, no further probe attempts are made and the average result of all the attempts is used.

Related commands: [G29](/User_manual/Reference/Gcodes/G29){target=_blank}, [G30](/User_manual/Reference/Gcodes/G30){target=_blank}, [G31](/User_manual/Reference/Gcodes/G31){target=_blank}, [G32](/User_manual/Reference/Gcodes/G32){target=_blank}, [M401](/User_manual/Reference/Gcodes/M401){target=_blank}, [M402](/User_manual/Reference/Gcodes/M402){target=_blank}.

See also: [Choosing a Z probe](/User_manual/Connecting_hardware/Z_probe_choosing){target=_blank}, [Connecting a Z probe](/User_manual/Connecting_hardware/Z_probe_connecting){target=_blank}

#### RepRapFirmware 2.x and earlier

##### Parameters

* **Pnnn** Z probe type
* **Fnnn** Feed rate (i.e. probing speed, mm/min)
* **Hnnn** Dive height (mm). When using mesh bed compensation or running G30 commands with specified XY coordinates (for example from the bed.g file), the firmware moves the Z probe to this height above where it expects the bed to be before commencing probing. The maximum depth of probing from this position is twice the dive height. A large dive height will tolerate a very uneven bed or poor calibration. A small dive height will make probing faster, because the Z probe has less distance to travel before reaching the bed. Default value if omitted is 5mm.
* **Innn** Invert (I1) or do not invert (I0, default) the Z probe reading (RepRapFirmware 1.16 and later)
* **Rnnn** Z probe recovery time before the probing move is started, default zero (seconds) (RepRapFirmware 1.17 and later). This is to allow the probe to settle after executing a travel move to the coordinates to probe.
* **Tnnn** Travel speed to and between probe points (mm/min). This is also the Z lift speed after probing. The corresponding axis speed limits set by M203 will be used instead if they are lower.
* **Annn** Maximum number of times to probe each point, default 1. Maximum, as of 2.03, is 31. Setting M558 A parameter to anything >31 set it to 0 instead of to 31
* **Snnn** Tolerance when probing multiple times, default 0.03mm
* **Bn** If 1, turn off all heaters while probing, default (B0) leaves heaters on. (RepRapFirmware 1.21 and later)
* **Cn** Endstop input number when the probe type is P4, default 3 (RepRapFirmware 2.02/1.23 and later)

**Obsolete parameters**

* **Xnnn** If nonzero, use probe for homing X axis (RRF 1.19 and earlier)
* **Ynnn** If nonzero, use probe for homing Y axis (RRF 1.19 and earlier)
* **Znnn** If nonzero, use probe for homing Z axis (RRF 1.19 and earlier)

##### Order dependency

M558 must come before G31.

##### Examples
<br>
<pre class="cblock">
M558 P1 X1 Y0 Z1 F500 T5000 H3 ; Z probe is used for homing X and Z axes (RRF 1.19 and earlier)<br>
M558 P4 H5 F120 T3000  ; Z probe connected to E0 endstop input<br>
M558 P7 H5 F120 T3000  ; Z probe connected to Z endstop input<br>
; BLTouch on Duet Maestro
M558 P9 H5 F120 T3000  ; BLTouch connected to Z probe IN pin
...
M280 P64 S10 ; send control signal to BLTouch through Z probe MOD pin<br>
; BLTouch on Duet WiFi
M558 P9 H5 F120 T3000  ; BLTouch connected to Z probe IN pin
M307 H3 A-1 C-1 D-1 ; free up heater 3 to use as BLTouch servo pin
...
M280 P3 S10 I1 ; send control signal to BLTouch through heater 3 pin
</pre>

##### Notes

A Z probe may be a switch, an IR proximity sensor, or some other device. The **P** selects which to use:

* P0 indicates that no Z probe is present. Whenever Z probing is commanded, you will be prompted to jog the Z axis until the nozzle is just touching the bed and then signal completion.
* P1 specifies an unmodulated or smart IR probe, or any other probe type that emulates one (probe output is an analog signal that rises with decreasing nozzle height above the bed). If there is a control signal to the probe, it is driven high when the probe type is P1.
* P2 specifies a simple modulated IR probe, where the modulation is commanded directly by the main board firmware using the control signal to the probe.
* P3 is similar to P1 but drives the control signal to the probe low. This may be used to switch between different Z probes.
* P4 selects a switch for bed probing. In recent firmware versions the C parameter specifies the endstop input number, default 3 (on the Duets this is the E0 endstop input).
* P5 (from RepRapFirmware 1.14) selects a switch by default (normally closed) for bed probing between the In and Gnd pins of the Z-probe connector (Duet 0.8.5 and Duet 2 WiFi).
* P6 is as P4 but the switch is connected to an alternative connector (on the Duet series, the E1 endstop connector). Deprecated in recent firmware versions, use P4 C4 instead.
* P7 (from RepRapFirmware 1.20) selects a switch (by default normally closed) connected to the Z endstop input. Deprecated in recent firmware versions, use P4 C2 instead.
* P8 (from RepRapFirmware 1.20) is as P5 but is unfiltered, for faster response time.
* P9 (from RepRapFirmware 1.21RC2) is as P5 but for a BLTouch probe that needs to be retracted and redeployed between probe points.
* P10 means use Z motor stall detection as the Z probe trigger.

The **H** parameter defines the Z probe dive height, which is the height above the trigger height from which probing starts. The default is 3mm or 5mm depending on firmware version. You may wish to increase it during initial calibration.

The **A** and **S** parameters control multiple probing. Probing is repeated until two consecutive probe attempts produce results that differ by no more than the S parameter; then the average of those two results is used. For example, S-1 would force averaging. However, if the number of attempts specified by the A parameter is reached without getting two consecutive results within tolerance of each other, no further probe attempts are made and the average result of all the attempts is used.

In RepRapFirmware versions 1.20beta4 and earlier, the **X**, **Y** and **Z** parameters specify whether each axis uses the Z probe as a substitute homing switch or not. If the parameter is nonzero, the Z probe is used for homing that axis. If the parameter is zero, the endstop switch for that axis is used for homing instead. In firmware 1.20beta6 and later, use the S parameter in the [M574](/User_manual/Reference/Gcodes/M574){target=_blank} command instead to indicate whether you are using a homing switch or a Z probe for homing X and Y.

Related commands: [G29](/User_manual/Reference/Gcodes/G29){target=_blank}, [G30](/User_manual/Reference/Gcodes/G30){target=_blank}, [G31](/User_manual/Reference/Gcodes/G31){target=_blank}, [G32](/User_manual/Reference/Gcodes/G32){target=_blank}, [M401](/User_manual/Reference/Gcodes/M401){target=_blank}, [M402](/User_manual/Reference/Gcodes/M402){target=_blank}.

See also: [Choosing a Z probe](/User_manual/Connecting_hardware/Z_probe_choosing){target=_blank}, [Connecting a Z probe](/User_manual/Connecting_hardware/Z_probe_connecting){target=_blank}

## M558.1: Calibrate height vs reading of scanning Z probe

Supported from RRF 3.5.0-rc.1

### Parameters

* **Knn** (optional) Z probe number, default 0. The probe must be of a scanning type (see M558).
* **Sn.n** Height to scan above and below the trigger height, in mm
* **Ann.n** (optional) Linear coefficient of the output, in mm per count
* **Bnn.n** (optional, ignored unless A parameter is also present, default 0.0) Quadratic coefficient of the output, in mm^2 per count
* **Cnn.n** (optional, ignored unless A parameter is also present, default 0.0) Cubic coefficient of the output, in mm^3 per count
If the A parameter is present then the equation to calculate the actual height of the Z probe is set to this:

<br>
<pre class="cblock">
height = trigger_height + A * (probe_reading - probe_threshold) + B * (probe_reading - probe_threshold)^2 + C * (probe_reading - probe_threshold)^3
</pre>

where trigger_height and probe_threshold are as set by G31.

If the A parameter is not present but the S parameter is present then the probe is raised or lowered to (trigger_height + S_parameter) at the current XY position, then readings are taken as the probe is gradually lowered to (trigger_height - S_parameter). The readings are used to compute, store and report new values of A, B, C and the trigger threshold.

If neither the A nor the S parameter is present, the current A, B and C values are reported.

##### Order dependency

Before M558.1 is used the probe must be defined as a scanning Z probe using M558, the probe trigger height must be set using G31, the axes must have been homed, the sensor must be over the bed surface and not too close to the edges of the bed, and the sensor drive current should have been set using M558.2 if necessary.

## M558.2: Calibrate or set drive level and reading offset for scanning Z probe

Supported from RRF 3.5.0-rc.1

### Parameters

* **Knn** (optional) Z probe number, default 0. The probe must be of a scanning type (see M558).
* **Snn** Drive level to set, or -1 to determine drive level and reading offset automatically. For LDC1612-based probes, when setting the current this should be in the range 0 to 31.
* **Rnnnn** (optional, default zero) Offset to subtract from the raw sensor reading. Only used if the S parameter is present and >= 0.

This command is used to set the drive current of scanning Z probes that use the LDC1612 chip. If the drive current is set too low, the sensor wil not work when it is close to the bed. If the drive current is set too high, it will not work when the sensor is distant from the bed. Use M122 B# (where # is the CAN address of the board that carries the sensor) to determine whether the sensor is working normally.

When using this command with S-1 to determine the optimum drive current automatically, the sensor should first be placed at the lower distance limit (closest distance from the metal bed surface) of the intended operating range.

The R parameter (reading offset) will be subtracted from the raw reading from the sensor in order to make the displayed Z probe reading more readable. You can use a value of zero (the default), but then you will see large readings with only the last few digits changing.

If M558 is used with no S parameter then the current drive level and offset are reported.

##### Order dependency

Before M558.2 is used the probe must be defined as a scanning Z probe using M558.
After M558.2 is used to change the drive level and/or offset, M558.1 should be used to recalibrate the probe.

## M559: Upload file

### Parameters

* **P**"filename". File name to upload to. 
* **S**nnn File size for binary transfer. If not present, the transfer is terminated by a M29 command
* **C**nnn CRC-32 of the file (optional)

### Examples
<br>
<pre class="cblock">
M559 P"config.g"
</pre>

### Notes

In RRF 3.1 and earlier, the default filename is config.g and the default path is /sys. In RRF 3.2 and later there is no default filename.

Quotation marks around the filename are mandatory in RRF 3.2 and later.

Was used by the original web interface to upload a configuration file. Can now be used to upload any file.

## M560: Upload file

### Parameters

* **P"filename"** File name to upload to. 
* **Snnn** File size for binary transfer. If not present, the transfer is terminated by a special string, described below.
* **Cnnn** CRC-32 of the file (optional)

### Examples
<br>
<pre class="cblock">
M560 P"index.html"
</pre>

### Notes

In RRF 3.1 and earlier, the default filename is reprap.htm and the default path is /www.

After sending M560 the file should be sent, terminated by the string `<!-- **EoF** -->`. Clearly that string cannot exist in the body of the file, but can be put on the end to facilitate this process.

## M561: Set Identity Transform

### Examples
<br>
<pre class="cblock">
M561
</pre>

### Description

This cancels any bed-plane fitting as the result of probing (or anything else) and returns the machine to moving in the user's coordinate system.

## M562: Reset temperature fault

### Parameters

* **Pnnn** Heater number

### Examples
<br>
<pre class="cblock">
M562 P2 ; Reset a temperature fault on heater 2
</pre>

### Notes

If the heater has switched off and locked because it has detected a fault, this will reset the fault condition and allow you to use the heater again. Obviously to be used with caution. If the fault persists it will lock out again after you have issued this command. P0 is heater H0, P1 is heater H1, and so on.

In firmware 1.20 and later, M562 with no parameters will clear a heater fault on all heaters

## M563: Define or remove a tool

### Parameters

* **Pnnn** Tool number (0 to 49 in RRF 3.x)
* **S"name"** Tool name (optional)
* **Dnnn** Extruder drive(s)
* **Hnnn** Heater(s)
* **Fnnn** Fan number(s) to use as print cooling fans for this tool (RepRapFirmware 1.16 and later)
* **Xnnn** Axis or axes to map X movement to (RepRapFirmware 1.16 and later)
* **Ynnn** Axis or axes to map Y movement to (RepRapFirmware 1.19 and later)
* **Lnnn** Drive to use for filament mapping. By default RRF will use the first and only extruder drive if this parameter is not specified (supported by RRF >= 2.02)
* **Rnn** Spindle number (RRF >= 3.3)

### Examples
<br>
<pre class="cblock">
M563 P0 D0:2:3 H1:3 ; create a tool using extruder drives 0, 2 and 3 and heaters 1 and 3
M563 P1 D1 H2 X3 ; create a tool using extruder drive 1 and heater 2 with X movement mapped to the U axis
M563 P2 D0:1 H1:2 X0:3 F0:2 ; create a tool using extruder drives 0 and 1, heaters 1 and 2, with X movement mapped to both X and U axes and fan 0 mapped to fans 0 and 2
M563 P3 D0 H1 S"Chocolate extruder" ; create a named tool using extruder drive 0 and heater 1
M563 P1 D-1 H-1 ; Delete tool 1
</pre>

### Description

Tools are usually (though not necessarily) extruders. Normally an M563 command to define a tool is immediately followed by a G10 command to set the tool's offsets and temperatures (temperatures can also be set with M568).

**P** The 'P' field specifies the tool number. In RRF3, tool numbers may be between 0 and 49. In RRF2 they may be between 0 and 65535. If you use the M563 command with a P value for a tool that has already been defined, and you provide any other parameters, that tool is redefined using the new values you provide.

**D** The 'D' field specifies the drive(s) used by the tool - in the first example drives 0, 2 and 3. The 'D' field number corresponds to the 'E' parameter defined in the M584 command. '0' means first 'E' driver in M584 and so on. Drive 0 is the first drive in the machine after the movement drives (usually X, Y and Z). If there is no 'D' field the tool has no drives. Tools are driven using multiple values in the 'E' field of G1 commands, each controlling the corresponding drive in the 'D' field above, as follows:
<br>
<pre class="cblock">
G1 X90.6 Y13.8 E2.24:2.24:15.89
G1 X70.6 E0:0:42.4
</pre>

The first line moves straight to the point (90.6, 13.8) extruding a total of 2.24mm of filament from both drives 0 and 2 and 15.98mm of filament from drive 3. The second line moves back 20mm in X extruding 42.4mm of filament from drive 3.

Alternatively, if the slicer does not support generating G1 commands with multiple values for the extrusion amount, the M567 command can be used to define a tool mix ratio.

**H** The 'H' field specifies the tool's heaters - in the first example heaters 1 and 3. Heater 0 is usually the hot bed (if any) so the first extruder heater is usually 1. If there is no H field the tool has no heaters. It is permissible for different tools to share some (or all) of their drives and heaters. So, for example, you can define two tools with identical hardware, but that just operate at different temperatures.

**F** The print cooling fan number(s) of the tool, default 0. Use this parameter if you are not using fan 0 as the print cooling fan for the tool you are defining. **You do not need to, and must not, list the fan numbers of thermostatic hot end fans here**. To use more than one print cooling fan for the tool, the definition would typically look like this:
<br>
<pre class="cblock">
M563 P0 D0 H1 F0:1 ; tool 0 uses extruder drive 0 and heater 1. Fan 0 and Fan 1 are use by tool 0 as print cooling fans.
</pre>

**R** The spindle number mapped to this tool. (RRF >= 3.3beta2)
<br>
<pre class="cblock">
M563 P0 R0 ; assign spindle 0 to tool 0
</pre>

**X, Y** The X and Y mapping option is used to create tools on machines with multiple independent X and/or Y carriages. The additional carriages are set up as axes U, V etc. (see M584) and the X mapping option in M563 defines which carriage or carriages are used. Axes are mapped in the order XYZUVWABC, where X=0, Y=1, Z=2, U=3 etc, not by driver number.

**S** As shown in the example above the S parameter can be used to give a tool a name. RepRapFirmware supports an additional form of the M563 command. The command:
<br>
<pre class="cblock">
M563 S1
</pre>

means add 1 (the value of the S parameter) to all tool numbers found in the remainder of the current input stream (e.g. the current file if the command is read from a file on the SD card), or until a new M563 command of this form is executed. The purpose of this is to provide compatibility between systems in which tool numbers start at 1, and programs such as slic3r that assume tools are numbered from zero.

RepRapFirmware maps the loaded filament on a per-extruder basis so if you have a mixing tool (one with more than one extruder), the **L** parameter tells the web interface which filament to display. If there is more than one extruder and the L parameter is omitted, no filament is displayed at all.

### Notes

In **RepRapFirmware 3.x**, in order to avoid the serialised object model getting very large, the P parameter (tool number) may not exceed 49.

M563 with just a P parameter just reports the existing configuration of the tool. Therefore, if you want to create a tool with no heaters and no extruders, you must provide at least one other parameter. For example, you can use the S parameter to name the tool.

RepRapFirmware allows the deletion of existing tools if M563 is called in this way:
<br>
<pre class="cblock">
M563 P1 D-1 H-1
</pre>

## M564: Limit axes

### Parameters

* **Hnnn** H1 = forbid movement of axes that have not been homed, H0 = allow movement of axes that have not been homed (firmware 1.21 and later)
* **Snnn** S1 = limit movement within axis boundaries, S0 = allow movement outside boundaries

### Examples
<br>
<pre class="cblock">
M564 S0 H0
</pre>

### Description

Allow moves outside the print volume and before homing, or not. If the S parameter is 0, then you can send G codes to drive the RepRap outside its normal working volume, and it will attempt to do so. Likewise if the H parameter is zero you can move the head or bed along axes that have not been homed. The default behaviour is S1 H1. On some types of printer (e.g. Delta and SCARA), movement before homing may be prohibited regardless of the H parameter.

## M566: Set allowable instantaneous speed change

### Parameters

* **Xnnn** Maximum instantaneous speed change of the X axis (mm/min)
* **Ynnn** Maximum instantaneous speed change of the Y axis
* **Znnn** Maximum instantaneous speed change of the Z axis
* **Ennn** Maximum instantaneous speed change of the extruder drives
* **Pn** Jerk policy (RepRapFirmware 2.03 and later)

### Examples
<br>
<pre class="cblock">
M566 X600 Y600 Z50 E600
</pre>

### Description

Sets the maximum allowable speed change (sometimes called 'jerk speed') of each motor when changing direction.

The model files and GCode files used by repraps generally render circles and other curves shapes as a sequence of straight line segments. If the motors were not allowed any instantaneous speed change, they would have to come to a stop at the junction between each pair of line segments. By allowing a certain amount of instantaneous speed change, printing speed can be maintained when the angle between the two line segments is small enough.

**X** and **Y** parameter: If you set the X and Y values too low, then the printer will be slow at printing curves. If they are too high then the printer may be noisy when cornering and you may suffer ringing and other print artefacts, or even missed steps.

**Z** parameter: When mesh bed compensation is used, movement may be jerky if the allowed Z jerk is too low, because the Z speed needs to change abruptly as the head moves between squares in the mesh.

**P** parameter: The default jerk policy is 0, which replicates the behaviour of earlier versions of RRF (jerk is only applied between two printing moves, or between two travel moves, and only if they both involve XY movement or neither does). Changing the jerk policy to 1 allows jerk to be applied between any pair of moves.

### Notes

The minimum jerk speed supported in as at firmware version 2.02RC3 is 0.1mm/sec.

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour.

Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M566 Z50, not M566 Z50:50

## M567: Set tool mix ratios

### Parameters

* **Pnnn** Tool number. If this parameter is not provided, the current tool is used.
* **Ennn** Mix ratios

### Examples
<br>
<pre class="cblock">
M567 P2 E0.1:0.2:0.1:0.6
</pre>

This example sets the mix ratio for tool 2 (the P value). When a G1 or G0 command contains only a single E value but the current tool uses multiple extruders, each extruder will extrude the amount specified in the E parameter times the mix value for that tool. For example:
<br>
<pre class="cblock">
G1 X20 E1.3
</pre>

This will move to X=20 extruding a total length of filament of 1.3mm. The first drive of tool 2 will extrude 0.1\*1.3mm, the second 0.2\*1.3mm and so on. The ratios don't have to add up to 1.0 - the calculation done is as just described. But when using a mixing hot end it is normally sensible if they do, otherwise under or over extrusion would occur. When printing concurrently with multiple print heads (e.g. ditto printing on an IDEX machine), it is normal for each print head to have a [total] extrusion factor of 1.0.

### Notes

**WARNING**: Currently the firmware will allow to set a mixing ratio where every extruder will drive at 100% - in case of a setup where several extruders feed the same hotend (like Diamond Hotends) this can lead to dangerously high pressures that could damage the printer.

The default is for the first driver to be set to 1, and all others to be set to 0. (ie for 2 drivers the default is E1:0)

## M568: Set Tool Settings

*Available in RepRapFirmware 3.3beta2 and later. The R and S parameters are alternatives to the temperature-setting functions of G10, which may be deprecated in the future.*

### Usage

* M568 Pnnn Rnnn Snnn Fnnn An

### Parameters

* **Pnnn** Tool number. If this parameter is not provided, the current tool is used.
* **Rnnn** Standby temperature(s)
* **Snnn** Active temperature(s)
* **Fnnn** Spindle RPM
* **An** Required heater state: 0 = off, 1 = standby temperature(s), 2 = active temperature(s). Supported in RRF 3.3 and later. If there is a current tool and the P parameter specifies a different tool, then any heaters used by the current tool are not affected.

### Examples
<br>
<pre class="cblock">
M568 P1 R140 S205 ; set standby and active temperatures for tool 1
M568 P0 F5200 ; set spindle RPM for tool 0
M568 P2 A1  ; set tool 2 heaters to their standby temperatures
</pre>

### Description


The **R** value is the standby temperature in °C that will be used for the tool, and the **S** value is its operating temperature. If you don't want the tool to be at a different temperature when not in use, set both values the same.

The **F** value will be used to set the configured spindle RPM for this tool's spindle. This value is direction-independent and needs to be given as a positive number. If the spindle is already running it will apply the new speed to the current direction (selected by M3/M4) immediately.

### Notes

Remember that any parameter that you don't specify will automatically be set to the last value for that parameter.

RepRapFirmware will report the tool parameters if only the tool number is specified.

Temperatures set with M568 do not wait for the heaters to reach temp before proceeding. In order to wait for the temp use a M116 command after the M568 to wait for temps to be reached.

See the [T code (select tool)](/User_manual/Reference/Gcodes/T){target=_blank} below. In tools with multiple heaters the temperatures for them all are specified thus: R100.0:90.0:20.0 S185.0:200.0:150.0 .

See also [M585](/User_manual/Reference/Gcodes/M585){target=_blank}.

## M568: Turn off/on tool mix ratios (deprecated)

***Deprecated:** from firmware 1.19 onwards, this command is no longer required or supported.* 

### Parameters

* **Pnnn** Tool number
* **Snnn** Whether mix ratios should be activated. 0 (default) mixing is turned off; non-zero it is turned on.

### Examples
<br>
<pre class="cblock">
M568 P2 S0
</pre>

Turn on/off automatic mix ratios for tool 2. 

### Notes

If a G1 command for the tool provides just one E value, then the mix ratio defined by M567 will always be used.

After turning off command G1 instructions must send as many E values as the tool has drives: G1 X20 E0.2:0.4:0.166:0.3

## M569: Set motor driver direction, enable polarity, mode and step pulse timing

### Parameters

* **Pnnn** Motor driver number
* **Snnn** Direction of movement of the motor(s) attached to this driver: 0 = backwards, 1 = forwards (default 1)
* **Rnnn** Driver enable polarity: 0 = active low, 1 = active high, -1 = driver is always disabled and is not monitored (default 0)
* **Tnnn** (firmware 1.14 and later) Minimum driver step pulse width and interval in microseconds
* **Taa:bb:cc:dd** (firmware 1.21 and later) Minimum driver step pulse width, step pulse interval, direction setup time and direction hold time, in microseconds (only applies to Duet 2, Duet 3 Mainboards and the Duet 3 Expansion 1XD)
* **Dnn** (firmware 2.0 and later, only applies to TMC2660, TMC22xx, TMC2160 and TMC5160 stepper drivers) Driver mode: 0=constant off time, 1=random off time (TMC2660 drivers only), 2=spread cycle, 3=stealthChop or stealthChop2 (mode 3 for TMC22xx/TMC2160/TMC5160 only), 4=Closed Loop, 5=Assisted open loop (modes 4 and 5 are only for [Duet 3 Expansion 1HCL boards](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL){target=_blank}). The default is spreadCycle for all drivers from RRF 3.4, and stealthChop2 for TMC22xx in RRF 3.3 and earlier. In stealthChop mode the drivers will switch over to spreadCycle automatically at high speeds, see the V parameter.
* **Fnn** (firmware 2.02 and later) Off-time in the chopper control register, 1 to 15
* **Cnnnn** (firmware 2.0 and later, only applies to TMC2660, TMC22xx, TMC2160 and TMC5160 stepper drivers) Lowest 17 bits of the chopper control register value.
* **Bnn** (firmware 2.02 and later) Blanking time (*tbl*) in the chopper control register, 0 to 3. See the TMC driver datasheet.
* **Hnn** (firmware 2.02 and later) *thigh* parameter for those stepper driver chips that support it, e.g. TMC5160 and TMC2160. Send M569 P# (where # is the driver number) with no additional parameters to see how this translates into mm/sec. See also the V parameter.
* **Yaa:bb** or **Yaa:bb:cc** (firmware 2.02 and later) Hysteresis start, end and decrement values in the chopper control register. See the TMC driver datasheet for the meaning.
* **Vnnn** (firmware 2.02 and later) *tpwmthrs* parameter for those stepper driver chips that support it. This is the interval in clock cycles between 1/256 microsteps below which the drivers will switch from stealthChop to to spreadCycle mode. Only applies when the driver is configured in stealthChop mode. Typical value are from 100 (high speed) to 4000 (low speed). Send M569 P# (where # is the driver number) with no additional parameters to see how this translates into mm/sec.

### Examples
<br>
<pre class="cblock">
M569 P0 S0        ; reverse the direction of the motor attached to driver 0
M569 P5 R1 T2.5:2.5:5:0  ; driver 5 requires an active high enable, 2.5us minimum step pulse, 2.5us minimum step interval, 5us DIR setup time and no hold time
</pre>

### Notes

* If no T parameter is given, then on boards having internal drivers the step pulse width and interval are guaranteed to be suitable for the on-board drivers only, and will generally be too fast for external drivers. On the EXP1XD board the default is T2.7:2.7:2.7:2.7. On the MB6XD board the default is T2.5:2.5:2.5:2.5.
* The T values get rounded up to the next highest value supported by the firmware. So the values reported back may be a little higher than you requested.
* **RepRapFirmware takes the highest T parameters seen in any M569 command, and applies those values to all drivers for which any nonzero T parameter was specified.** So if you want to reduce the T parameters, you will need to do that on all drivers that already have nonzero T parameters. On the MB6XD this means that if you want to reduce the T values below the defaults, you need to do this on all six drivers even if you are not using all of them. Additionally, on all main boards except for the MB6XD, if you reduce the T parameters then you must restart the firmware for the change to take effect. On Duet 3 systems with CAN-connected expansion boards this note applies separately to each board. It does not apply to the EXP1XD because that board has only one driver.
* Some versions of RepRapFirmware prior to 1.14 also provided XYZ and E parameters to allow the mapping from axes and extruders to stepper driver numbers to be changed. From 1.14 onward, this functionality is provided by M584 instead.

## M569.1: Stepper driver closed loop configuration

### Parameters

* **Pnn** Motor driver number
* **Tn** Encoder type: 0=none (default), 1=linear quadrature encoder plus Duet3D magnetic shaft encoder (RRF 3.5 only), 2=quadrature motor shaft encoder, 3=Duet3D magnetic motor shaft encoder using AS5047D (RRF 3.5 only)
* **Cn.n** In RRF 3.4, for a quadrature motor shaft encoder (T2) this is the number of counts per full step. In RRF 3.5, for a quadrature shaft encoder (T2) or linear composite encoder (T1) it is the number of quadrature encoder pulses per revolution. Not required for a magnetic shaft encoder.
* **En.n:m.m** Error thresholds. If m.m is nonzero then whenever the actual position is more than m.m full motor steps of the desired position, this will be reported as a driver error. If n.n is nonzero and n.n < m.m then whenever the actual position is more than n.n full steps of the desired position but is less than m.m full steps, this will be reported as a pre-stall. (Default: m.m=2.0, n.n=1.0) The action that is taken on a stall/pre-stall can be configured using the [event system](/User_manual/RepRapFirmware/Events)
* **Sn.n** (optional, RRF 3.5 only) Motor full steps per revolution, default 200.
* **Rn.n** Proportional constant
* **In.n** (optional) Integral constant
* **Dn.n** (optional) Derivative constant
* **Vn.n** (optional) Velocity feedforward constant (RRF 3.5beta2 and later only)
* **An.n** (optional) Acceleration feedforward constant (RRF 3.5beta4 and later only)
* **Hn.n** (optional) Minimum holding current as a percentage of the configured motor current when operating in closed loop mode
* **Qn.n** (optional) Motor torque in newton-metres per amp of peak motor current (RRF 3.5 post beta 4 only)

### Description

Sets the configuration parameters of a closed loop driver. See the [M569](/User_manual/Reference/Gcodes/M569){target=_blank} D parameter for switching a driver to closed loop after it has been configured.

For RRF 3.4, if you are using a quadrature encoder on the motor shaft,  the encoder counts per full step (Cn.n) can be found from the datasheet of the encoder being used. If the value is stated as counts per revolution (CPR), divide by the steps per revolution of the stepper motor to get the count per step. For example, a 1000 CPR encoder attached to a 200 step/rev (1.8 deg/step) motor will have a count per step of 1000 ÷ 200 = 5.

### Notes

* Supported for drivers attached to:
  * [Duet 3 Expansion 1HCL boards](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL){target=_blank}
* The E parameter defaults to 0.0:0.0 in RRF 3.4.x. **If you do not override this default, then failure to maintain position will not be reported.**
* See [Tuning the Duet 3 Expansion 1HCL](/User_manual/Tuning/Duet_3_1HCL_tuning){target=_blank} for further details on setting the proportional/integral/derivative constants.
* The Q parameter is relevant, and required, only when the driver is put into torque mode, see M569.4.

## M569.2: Read or write stepper driver register

*Supported in RRF 3.3 and later on main boards with TMC22xx or TMC51xx stepper drivers.*

### Parameters

* **Pnn** Motor driver number
* **Rnn** Register number, 0-127
* **Vnnnn** Value to write (optional)

### Examples
<br>
<pre class="cblock">
M569.2 P1 R0
</pre>

### Description

If the V parameter is not provided, this command reads the specified register and returns the value of that register. If the V parameter is provided, that value is written to the specified register.

**WARNING!** Use of M569.2 to write stepper driver registers may result in damage to the stepper drivers, for example from excessive motor current or insufficient blanking time.

## M569.3: Read Motor Driver Encoder via secondary CAN bus

*Supported on Duet 3 MB6HC and MB6XD boards with ODrives connected to the second CAN bus and Hangprinter kinematics configured*

Report a current motor encoder positions to the host in units of arc degrees (1/360'ths of turns), relative to some reference position that you set with the **S** parameter.

### Parameters

* **Pnn** Motor driver number, or board address and driver number. Several (remote) drivers may be specified, separated by colon. No more than number of visible axes, as specified by M584 P parameter, are allowed.
* **S** Sets an encoder reference point. Current and subsequent M569.3 Pn.n calls returns numbers that are relative to the M569.3 Pn.n S call.

### Examples
<br>
<pre class="cblock">
M569.3
Error: M569: missing parameter 'P'
</pre>

<br>
<pre class="cblock">
M569.3 P54.0
Error: M569.3: Message not received
</pre>

<br>
<pre class="cblock">
M569.3 P50.0:51.0:52.0:53.0
[-155.28, -4089.60, 6842.04, 0.00, ],
</pre>

<br>
<pre class="cblock">
M569.3 P53.0:51.0:52.0:50.0
[0.00, -4089.60, 6842.04, -155.28, ],
</pre>

<br>
<pre class="cblock">
M569.3 P53.0:51.0:52.0:50.0 S
[0.00, 0.00, 0.00, 0.00, ],
</pre>

<br>
<pre class="cblock">
M569.3 P49.0
Error: M569.3: Max CAN addresses we can reference is 4. Can't reference board 49.
</pre>

### Notes

Before the first call with the **S** parameter, the reference is unknown and arbitrary.

If **P** is not supplied, an error is returned.

A maximum of four CAN-connected drivers can be reached with M569.3 counting from machine boot. CAN addresses that fail to respond don't count towards this maximum.

## M569.4: Set Motor Driver Torque Mode

*Supported in firmware 3.5 post beta4 on Expansion 1HCL and M23CL devices when in closed loop mode. Also supported in firmware 3.4 and later on Duet 3 MB6HC and MB6XD boards with ODrives connected to the second CAN bus and Hangprinter kinematics configured.*

Tell one or more motor drivers to apply a specified torque regardless of position.

### Parameters

* **Pn.n** Motor CAN address and driver number. Can also be a colon separated list of driver numbers.
* **Tn.n** The torque to apply in units of Nm, or zero to leave torque mode. Applying the requested torque is dependnt on on the M569.1 Q parameter having been specified correctly (not applicable to Hangprnter/ODrive).
* **Vn.n** (optional) Maximum speed to move at in full steps per second. A zero (the default) or negative value means no limit. Not supported on Hangprinter/ODrive.

### Examples
<br>
<pre class="cblock">
M569.4
Error: M569: missing parameter 'P'
</pre>

<br>
<pre class="cblock">
M569.4 P50.0:51.0
Error: M569: missing parameter 'T'
</pre>

<br>
<pre class="cblock">
M569.4 P50.0 T0.001
0.001000 Nm
</pre>

<br>
<pre class="cblock">
M569.4 P50.0:51.0 T0
pos_mode, pos_mode
</pre>

### Notes

* If **P** or **T** parameter is missing, then no action is taken.
* Use the Q parameter of the M569.1 command to specify the torque constant of the motor.
* The driver is put back into position mode by requesting a torque smaller than 0.0001 Nm
* Sending a regular motion command (e.g. G1) that reques movement by the motor also puts the motor back into position mode.
* Hangprinter's "torque mode" is implemented as a RepRapFirmware macro that depends on M569.4.

## M569.5: Closed loop data collection

Collect performance data from a drive whilst in closed loop mode. Can be used alongside the Duet [Closed Loop plugin](https://github.com/Duet3D/Closed-Loop-Plugin){target=_blank} for visualisation. Records back to a CSV file located in the /sys/closed-loop directory, which will be created if it does not exist.

### Parameters

* **Pnn** Motor driver number
* **F"filename.csv"** Filename to write to, optional, defaults to *driveraddr_datetime.csv*. All files are stored in the *sys/closed-loop* directory.
* **An** Mode: 0=record immediately, 1=record when the target position of the motor is next changed (i.e. on next move)
* **Rnn** Sample rate. Measured in samples per second. If R0 is sent, the board will record as fast as possible.
* **Dnn** Variable filter. Determines which variables are recorded. See below for a list of available variables and how they are represented.
* **Vnn** Perform a tuning manoeuvre. Available manoeuvres described below.
* **Snn** Number of samples to record. When recording with rate R0, this sample rate is limited depending on the number of variables being recorded. The command will report back the maximum allowable samples if the maximum is exceeded. The maximum allowable samples may differ between board types since it is dependant on the available RAM.
* **Tnn** If performing a step tuning manoeuvre, this is the time in milliseconds over which to complete the step change, default 2.0

### Description

The following variables are available to record:

| Variable Name | Description | Variable ID |
|:---|:---|
| Raw Encoder Reading | The raw reading that the expansion board has read from the encoder. For a quadrature encoder, this represents the number of pulses received, so may be 4 times the expected CPR. | 1 |
| Measured Motor Steps | The current position of the motor measured in the standard unit of 'steps'. Similar to raw encoder reading, but will be standardised across different types of encoders with different CPRs. | 2 |
| Target Motor Steps | The number of steps that the motor has been commanded to take. This becomes the target signal fed into the PID controller. | 4 |
| Current Error | The difference between the current motor steps and the target motor steps. Directly used as an input to the PID controller. | 8 |
| PID Control Signal | The control signal (normalised between -255 and 255 coming from the PID controller. | 16 |
| PID P Term | The value of the PID controller's P term. | 32 |
| PID I Term | The value of the PID controller's I term | 64 |
| PID D Term | The value of the PID controller's D term. | 128 |
| Measured Step Phase | The current position of the motor's stator, expressed as the proportion of the way through the motor's 4 step cycle. Normalised between 0-4095 | 256 |
| Desired Step Phase | The angle for which current will be applied. For maximal torque, this value will be 25% ahead of step phase, for minimal torque, this value will equal step phase. | 512 |
| Phase Shift | The difference between the desired step phase and the step phase. Roughly proportional to torque applied. | 1024 |
| Coil A Current | The current running through coil A, expressed as a proportion of the motor's maximum current normalised between -255 to 255. | 2048 |
| Coil B Current | The current running through coil B, expressed as a proportion of the motor's maximum current normalised between -255 to 255. | 4096 |
| PID V Term | The value of the PID controller's P term. | 8192 |
| PID A Term | The value of the PID controller's I term | 16384 |
| Motor current fraction | The fraction of the configured motor current that is set | 32768 |

To record multiple variables, sum the variable IDs and pass the resulting value as the D parameter. For example, to record coil A current (2048) and coil B current (4096), use D6144.

The following manoeuvres are available:

| Manoeuvre Name | Description | Manoeuvre ID |
|:---|:---|
| Polarity Detection and Zeroing | Detects in which orientation the stepper motor coils are connected, this will also detect if a motor's wiring is faulty or it is not plugged in. Ensures that a feedback reading of 0 corresponds to the position the encoder assumes when only coil A is energised. Note this move would normally be carried out using M569.6 prior to PID loop tuning. | 1 |
| Absolute SPI Encoder Calibration | Calibrates the encoder positions to the motor. Note this move would normally be carried out using M569.6 prior to PID loop tuning and only needs to be done once per Motor,encoder and 1HCL combination. | 2 |
| Step Manoeuvre | Applies a sudden change of 4 full steps to the PID target to view the step response of the PID controller. | 64 |

### Examples
<br>
<pre class="cblock">
M569.5 P50.0 S500 A0 R0 D6 V64
</pre>

Record 500 samples (S500) of the current motor steps and target motor steps (D6) of driver 0 attached to board 50 (P50.0) immediately (A0) and as fast as possible (R0) whilst performing a step manoeuvre (V64)

### Notes

**The Duet [Closed Loop plugin](https://github.com/Duet3D/Closed-Loop-Plugin){target=_blank} can be used to generate and run M569.5 commands.**

Supported for drivers attached to:

* [Duet 3 Expansion 1HCL boards](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL){target=_blank}

Note: The driver must be configured in closed loop mode (See [M569](/User_manual/Reference/Gcodes/M569){target=_blank} D parameter).

## M569.6: Execute closed loop tuning move

Perform a [runtime tuning manoeuvre](/User_manual/Tuning/Duet_3_1HCL_tuning#runtime-tuning){target=_blank} with a closed loop drive.

### Parameters

* **Pnn** Motor driver number
* **Vnn** Tuning move to perform

### Description

**Warning: Duet firmware currently only supports tuning one driver at a time. This means that when tuning a multi-driver axis, one driver will move and the other(s) will not. If attempting to tune a multi-driver axis, please take appropriate mitigation to ensure the axis doesn't become stressed/misaligned when only one one driver moves.**

The [closed-loop tuning page](/User_manual/Tuning/Duet_3_1HCL_tuning){target=_blank} lists the available tuning moves.

### Examples
<br>
<pre class="cblock">
M569.6 P50.0 V1 ; conduct polarity detection and zeroing move on closed loop driver on HCL board at address 50.
M569.6 P51.0 V2 ; conduct magnetic encoder calibration on move on closed loop driver on HCL board with magnetic encoder at address 50.
</pre>

### Notes

Supported for drivers attached to:
* [Duet 3 Expansion 1HCL boards](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL){target=_blank}


## M569.7: Configure motor brake port

### Parameters

* **Pn.n** Motor CAN board address (if applicable) and driver number
* **C"port"** Port name of the brake control port. **The port must be on the same CAN board as the driver.** The CAN address does not need to be specified in the port name, but if it is then it must be the same as the driver CAN address. In RRF3.5 and later, when this parameter is present the delay between brake on and motor off is set to the default value unless the S parameter is present.
* **Snnn** (optional, RRF 3.5 and later only) Delay in milliseconds between de-energising the brake solenoid (to activate the brake) and disabling the driver when M18 or M84 is used to turn off the motor. This is to allow the brake to engage fully before the motor current is turned off.
* **Vnn** (optional, supported in RRF 3.5 and later on boards with variants that accept 48V VIN) Brake voltage. If the VIN voltage is or rises above this value, PWM will be used to reduce the average brake voltge to this value. Caution: use this option only in conjunction with a port that supports PWM! All Duet 3 OUT ports support PWM.

### Examples
<br>
<pre class="cblock">
M569.7 P40.0 C"out1"   ; driver 0 on board 40 uses port out1 on board 40 to control the brake
</pre>

### Notes

When the motor driver is enabled, the specified output port will be turned on at the same time to release the brake. When the motor driver is disabled, the output port will be turned off. Idle current mode does not count as disabled.

Note: after M569.7 is executed, the port will be initially off. Therefore, M569.7 should be executed before the motor is first enabled.

## M569.8: Read motor force via secondary CAN bus

*Supported only on Duet 3 MB6HC and MB6XD boards with ODrives connected to the second CAN bus and Hangprinter kinematics configured*

### Parameters

* **Pn.n** Motor CAN board address and driver number

## M569.9: Configure driver sense resistor and maximum current

*Supported by the STM port of RRF only*

### Parameters

* **Pn.n** Motor CAN board address (if applicable) and driver number
* **Rnnnn** Driver current sense resistor value in ohms
* **Snnnn** Driver maximum current limit in amperes

### Examples
<br>
<pre class="cblock">
M569.9 P0.1 R0.075 S4.4   ; driver 1 on the main board has a 0.075 Ohm sense resistor and will have the maximum current setting limited to 4.4A
</pre>

### Notes

This command is available on boards running the STM32 port of RepRapFirmware (version 3.4.2_102 and later), it can be used to inform the firmware of the TMC driver sense resistor and maximum current limit used by driver modules that have non standard values.

## M570: Configure heater fault detection

### {.tabset}

#### RRF 3.5 and later

* **Hnnn** Heater number
* **Pnnn** Time in seconds for which a temperature anomaly must persist on this heater before raising a heater fault (default 5 seconds)
* **Tnnn** Permitted temperature excursion from the setpoint for this heater (default 15C)
* **Rn** (RRF 3.5 and later only) Maximum number of consecutive temperature reading failures before a heater fault is raised. The default is 3 which guarantees that a fault will be raised within one second of a sensor becoming disconnected or shorted. Using R0 will result in a heater fault being raised immediately when a sensor fails to deliver a sensible reading, but will make the system more likely to report spurious failures if the sensor or its wiring is subjected to electrical interference or ESD.

The actions taken on a heater fault, after the heater is shutdown, are now handled by the [event system](/User_manual/RepRapFirmware/Events). 

#### RRF 3.4.0 to 3.4.3 

* **Hnnn** Heater number
* **Pnnn** Time in seconds for which a temperature anomaly must persist on this heater before raising a heater fault (default 5 seconds)
* **Tnnn** Permitted temperature excursion from the setpoint for this heater (default 15C)

The actions taken on a heater fault, after the heater is shutdown, are now handled by the [event system](/User_manual/RepRapFirmware/Events). 

#### RRF 3.3 and earlier 
**Parameters for RepRapFirmware 1.15e to 3.3**

* **Hnnn** Heater number
* **Pnnn** Time in seconds for which a temperature anomaly must persist on this heater before raising a heater fault (default 5 seconds)
* **Tnnn** Permitted temperature excursion from the setpoint for this heater (default 15C)
* **Snnn** (RRF versions between 1.20 and 3.3 inclusive only) Integer timeout in minutes (can be set to 0) for print to be cancelled after heater fault. If the S parameter timeout occurs (which only happens if a SD print is in progress), RRF will also try to turn off power via the PS_ON pin.


**Parameters for RepRapFirmware 1.14 and earlier**

* **Snnn** Heater timeout (in seconds)

### Order dependency

When using RepRapFirmware 3 the M570 command must come later in config,g than the M950 command that creates the heater specified in the H parameter.

### Examples
<br>
<pre class="cblock">
M570 H1 P4 T15 ; An anomaly on heater 1 must persist for 4 seconds, and must be greater or less than 15C from the setpoint, to raise a heater fault.
</pre>

### Notes

**Warning!** Heating fault detection is provided to reduce the risk of starting a fire if a dangerous fault occurs, for example if the heater cartridge or thermistor falls out of the heater block. You should carefully consider sensible values for the detection time or permitted temperature excursion, setting them incorrectly will reduce the protection. Also note that this protection should not be relied upon exclusively. Protection against fire should be provided external to the operation of the firmware as well (fuses, fire detection, do not print unattended etc)

For further details about heater fault handling see [Heater faults and how to avoid them](/User_manual/Troubleshooting/Heater_faults)

## M571: Set output on extrude

### Parameters

* **Snnn** Output value
* **Fnnn** Output PWM frequency (RepRapFirmware 1.17 and later; deprecated in 3.2beta2 and later)
* **Qnnn** Output PWM frequency (RepRapFirmware 3.2beta2 and later)
* **Pnnn** (RepRapFirmware 1 and 2) Logical pin number (RepRapFirmware 1.17), defaults to the FAN0 output in firmware 1.19 and earlier until M571 with a P parameter has been seen
* **P"pin-name"** (RepRapFirmware 3) Name of the pin to use

### Examples
<br>
<pre class="cblock">
M571 P3 F200 S1  ; turn on logical pin 3 while extrusion is commanded (RRF 2)
M571 P"heater3" S0.5 ; turn on heater 3 output at 50% PWM while extrusion is commanded (RRF 3)
</pre>

### Description

This turns the controlled pin output on whenever extrusion is being done, and turns it off when the extrusion is finished. The output could control a fan or a stirrer or anything else that needs to work just when extrusion is happening. It also can be used to control a laser beam. The S parameter sets the value of the PWM to the output. 0.0 is off; 1.0 is fully on.

### Notes

In RepRapFirmware 3 you specify the pin name using the P parameter.

For RepRapFirmware 1.x and 2.x, pin numbers are the same as in the M42 and M280 commands. The pin you specify must not be in use for anything else, so if it is normally used as a heater you must disable the heater first using M307, or if it is used for a fan you must disable the fan using M106 with the I-1 parameter. 

In RepRapFirmware 1.17 and later you can use the P parameter to change the pin used and set the PWM frequency. Defaults to using the FAN0 output.

RepRapFirmware 1.20 and later do not default to using the FAN0 output, so you must send M571 with a P parameter at least once to define the pin that you wish to use.

## M572: Set or report extruder pressure advance

### Parameters

* **Dnnn** Extruder number(s)
* **Snnn** Pressure advance amount (in seconds) to use for that extruder or extruders

### Examples
<br>
<pre class="cblock">
M572 D0 S0.1 ; set extruder 0 pressure advance to 0.1 seconds
M572 D0:1:2 S0.2 ; set extruder 0, 1 and 2 pressure advance to 0.2 seconds (RepRapFirmware 1.20 and later)
</pre>

### Description

This sets the pressure advance coefficient (S parameter) for the specified extruder (D parameter). Only one S value is allowed. If you wish to set different pressure advance for different extruders, use multiple M572 commands.

Pressure advance causes the extruder drive position to be advanced or retarded during printing moves by an additional amount proportional to the rate of extrusion. At the end of a move when the extrusion rate is decreasing, this may result in the extruder drive moving backwards (i.e. retracting). Therefore, if you enable this feature, you may need to reduce the amount of retraction you use in your slicing program to avoid over-retraction.

If you configure Input Shaping, you will need to retune your Pressure Advance. It is recommend to first tune Input Shaping, then Pressure Advance, then Retraction.

For more details such as tuning the value see [Pressure advance](/User_manual/Tuning/Pressure_advance){target=_blank}.

## M573: Report heater PWM

*Not supported in RRF 3.4 and later.*

### Parameters

* **Pnnn** Heater number

### Examples
<br>
<pre class="cblock">
M573 P1
</pre>

### Description

This gives a running average (usually taken over about five seconds) of the PWM to the heater specified by the P field. If you know the voltage of the supply and the resistance of the heater this allows you to work out the power going to the heater. Scale: 0 to 1.

In RRF 3.4 and later, if you need to find the average heater PWM, you can query the object model instead. The recommended replacement for M573 P1 is:
`echo heat.heaters[1].avgPwm`

## M574: Set endstop configuration

### {.tabset}

#### M574 - RepRapFirmware 3

##### Parameters

* **Xnnn** Position of X endstop: 0 = none, 1 = low end, 2 = high end.
* **Ynnn** Position of Y endstop: 0 = none, 1 = low end, 2 = high end.
* **Znnn** Position of Z endstop: 0 = none, 1 = low end, 2 = high end.
* **P"pin_name"** Defines the pin name(s) that the endstop(s) for the specified axis are connected to, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank}. Needed when S=1. May need ! before pin name to invert signal, or ^ to enable the pullup resistor, for example on the Duet 2 expansion header if using the pins directly without a duex5.
* **Snnn** 1 = switch-type (eg microswitch) endstop input, 2 = Z probe (when used to home an axis other than Z), 3 = single motor load detection, 4 = multiple motor load detection (see Notes).
* **Knnn** Optional Z probe number (3.5beta3 or later, only for S2, defaults to 0)

##### Order dependency

This command must be later in config.g than the M584 command that creates additional axes, or axes that have multiple motors and endstops.

##### Examples

<br>
<pre class="cblock">
M574 X1 S1 P"io1.in"  ; configure active high endstop switch for low end on X (Duet 3)
M574 Y2 S1 P"!io2.in" ; configure active low endstop switch for high end on Y (Duet 3)
M574 Z1 S1 P"e0stop"  ; configure active high endstop switch for low end on Z (Duet 2)
M574 U1 S2            ; configure Z-probe endstop for low end on U
M574 V2 S3            ; configure sensorless endstop for high end on V
</pre>

To use two Z motors using independent homing switches, declare two Z motors in M584, then declare two pins for Z endstops in a single M574 command. Example
<br>
<pre class="cblock">
M584 X0 Y1 Z2:3 E4
M574 Z1 S1 P"io2.in+io3.in" ; Z axis with two motors, individual min endstops, active high
</pre>

The order of endstop switch pin names in M574 must match the order of Z motor driver numbers in M584. When homing Z, RRF3 homes the motors of the axis at the same time, independently to their defined endstops. See [Axis levelling using endstops](/User_manual/Connecting_hardware/Z_probe_auto_levelling#axis-levelling-using-endstops){target=_blank}.

##### Notes

* In RRF3, the M574 command allows for more flexibility than in RRF2. This includes supporting axes defined with multiple motors and multiple endstops (one per motor), use of non-default endstop inputs, and re-assigning endstop inputs.
* Use a separate M574 command for each axis.
* Parameter **P** gives the pin name(s) for the endstop(s) for the specified axis. If the number of pins matches the number of motors assigned to that axis, motors will be stopped individually when their endstop switches trigger.
* For active low endstops, use type S1 and invert the input by prefixing the pin name with '!', for example `M574 X1 S1 P"!xstop"`. Invert the input when using an NPN output inductive or capacitive sensor, or using a NO switch (not recommended, use a NC switch instead).
* The S2 option of M574 is intended for use only when axes other than Z are using the Z probe for homing. The only printers known that do this using Duet electronics are the RepRapPro Ormerod, Huxley Duo, and Mendel Tricolour machines. When using the Z probe to home Z, M574 Z has no bearing on the probe setup or usage. A Z probe and a Z endstop can both be configured at the same time. G30 calling the probe setup with M558, and G1 H1 Z moves calling the endstop configured with M574 Z.
* Endstop type S4 means use motor stall detection (like S3) but if there are multiple motors, stop each one individually as it stalls. S3 means use motor stall detection but stop all relevant motors when the first one stalls.
* Pull up resistors on Duet 2/Duex5 inputs should be configured for connecting a digital inputs (like a switch, BLtouch, etc) only on inputs not labelled "n"Stop (xstop, ystop etc).

#### M574 - RepRapFirmware 2.x and earlier

##### Parameters

* **Xnnn** Position of X endstop: 0 = none, 1 = low end, 2 = high end.
* **Ynnn** Position of Y endstop: 0 = none, 1 = low end, 2 = high end.
* **Znnn** Position of Z endstop: 0 = none, 1 = low end, 2 = high end.
* **E** Select extruder endstops to define active high or low (RepRapFirmware 1.16 and earlier only)
* **Snnn** Endstop type: 0 = active low endstop input, 1 = active high endstop input, 2 = Z probe, 3 = motor load detection

##### Order dependency

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

##### Examples
<br>
<pre class="cblock">
M574 X1 Y2 Z0 S1 ; X endstop at low end, Y endstop at high end, no Z endstop, all active high
</pre>

##### Notes

This defines the type of endstop switch or opto sensor that the printer has for each axis: 0 = none, 1 = low end, 2 = high end. The optional S parameter defines whether the endstop input is active high (S1, the default) or low (S0). A normally-closed endstop switch wired in the usual way produces an active high output (S1).

The S2 and S3 options are supported in firmware 1.20 and later.

This is intended for use with boards that provide a single endstop input for each axis that may be used for either a high or a low end endstop, such as the Duet. On delta printers, the XYZ parameters refer to the towers and the endstops should normally all be high end (i.e. at the top of the towers).

If you have more than one homing switch for an axis because you want to home multiple motors driving that axis individually, you will need to split them into separate axes during homing, and home those axes together. See [Axis levelling using endstops](/User_manual/Connecting_hardware/Z_probe_auto_levelling#axis-levelling-using-endstops){target=_blank}.

In RepRapFirmware 1.16 and earlier, the M574 command with E parameter was used to specify whether a Z probe connected to the E0 endstop input produces an active high (S1) or active low (S0) output. In RepRapFirmware 1.17 and later, use the I parameter of the M558 command instead.

## M575: Set serial comms parameters

This sets the communications parameters of the serial comms channel specified by the P parameter.

### Parameters

* **Pnnn** Serial channel number
* **Bnnn** Baud rate, default 57600 (same as the default PanelDue baud rate)
* **Snnn** Mode: 0 = PanelDue; 1 (default) = PanelDue mode, checksum or CRC required; 2 = raw mode; 3 = raw mode with checksum or CRC required; 4 = PanelDue mode, CRC required; 6 = raw mode, CRC required. Modes 2 and 3 are only supported in RRF 3.01 and later. Modes 4 and 6 are only supported in RRF 3.4 and later.

### Examples
<br>
<pre class="cblock">
M575 P1 B57600 S1
</pre>

### Description

P0 specifies the main serial interface (typically a USB port, or serial-over-USB), while P1 specifies an auxiliary serial port (for example, the port used to connect a PanelDue) and P2 specifies a second auxiliary port if there is one. The B parameter is the required baud rate (this parameter is typically ignored if the port is a true USB port).

### Notes

In RRF 3.1 and later for Duet 3, the auxiliary serial port remains disabled until a M575 P1 command is received. This is to allow the IO_0 port to be used for other purposes. In RRF 3.2 and later on all boards, the auxilliary serial port(s) remain disabled until enabled using M575.

## M576: Set SPI comms parameters

*Supported in RRF 3.4 and later in SBC mode.*
This sets the communications parameters of the SPI channel. 

### Parameters

* **Snnn** Maximum delay between full SPI transfers (in ms, defaults to 25ms)
* **Fnnn** Maximum delay between full SPI transfers when a file is open (in ms, defaults to 5ms)
* **Pnnn** Number of events required to skip the delay (defaults to 4)


## M577: Wait until endstop is triggered

Wait for an endstop switch to be triggered or an input to become active.

### {.tabset}

#### RepRapFirmware 3.01RC2 and later

##### Parameters

* **Sn** Desired endstop or input level: 1 = triggered/active (default), 0 =not triggered/inactive
* **X, Y, Z, U, V, W, A, B, C, D** Axis endstop to wait for
* **Pnnn** Input pin number to wait for (see [M950](/User_manual/Reference/Gcodes/M950){target=_blank} with J parameter)

##### Order dependency

If M577 uses an input pin number, M577 must come after the [M950](/User_manual/Reference/Gcodes/M950){target=_blank} command that defines the input pin number.

##### Examples
<br>
<pre class="cblock">
M577 X S0  ; wait until X endstop is not triggered<br>
M950 J2 P"!e0stop" ; define input pin number 2
M577 P2 ; wait until tE0 endstop input is low
</pre>

#### RepRapFirmware 3.0 to 3.01RC1

##### Parameters

* **P"nnn"** Specifies one or more pin names, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank}

##### Examples
<br>
<pre class="cblock">
M577 P"!xstop+!e0stop" ; wait until the X and E0 endstop inputs are both low
</pre>

##### Notes

Use the P parameter to specify one or more pin names. The pin(s) do not need to be exclusively used by M577; for example, it is permitted to specify the name of a pin that has already been declared as used by an endstop switch in a M574 command.

The command waits for a high input level. To wait for a low input level, invert the pin name by prefixing "!".

#### RepRapFirmware 2.x and earlier

##### Parameters

* **Snnn** Desired endstop level
* **Xnnn** Select X axis endstop
* **Ynnn** Select Y axis endstop
* **Znnn** Select Z axis endstop
* **Ennn** Select extruder drive endstop

##### Examples
<br>
<pre class="cblock">
M577 E0 S1
</pre>

Wait for an endstop switch to be pressed. The example above will wait until the first extruder endstop is triggered.

The following trigger types may be used using the 'S' parameter:

0: Endstop not hit
1: Low endstop hit
2: High endstop hit
3: Near endstop (only Z probe)

## M578: Fire inkjet bits

*This command is not enabled unless the SUPPORT_INKJET feature is enabled when the firmware is built.*

### Parameters

* **Pnnn** Inkjet head number
* **Snnn** Bit pattern

### Examples
<br>
<pre class="cblock">
M578 P3 S5
</pre>

This fires inkjet head 3 (the P field) using the bit pattern specified by the S field, in the example shown would fire bits 101. 

### Notes

If the P parameter is ommitted inkjet 0 is assumed.

This is a version of the M700 command used by [Inkshield](http://reprap.org/wiki/Inkshield){target=_blank}.

An alternative way of controlling inkjets would be to use the P parameter on the [G1](/User_manual/Reference/Gcodes/G1){target=_blank} command, in conjunction with the [M670](/User_manual/Reference/Gcodes/M670){target=_blank} command.

## M579: Scale Cartesian axes

On a Cartesian RepRap you can get prints exactly the right size by tweaking the axis steps/mm using the [M92](/User_manual/Reference/Gcodes/M92){target=_blank} GCode. But this does not work so easily for Delta and other RepRaps for which there is cross-talk between the axes. This command allows you to adjust the X, Y, and Z axis scales directly. So, if you print a part for which the Y length should be 100mm and measure it and find that it is 100.3mm long then you set Y0.997 (= 100/100.3).

### Parameters

* **Xnnn** Scale factor for X axis
* **Ynnn** Scale factor for Y axis
* **Znnn** Scale factor for Z axis
* **U, V, W, A, B, C** Scale factors for additional axes

### Examples
<br>
<pre class="cblock">
M579 X1.0127 Y0.998
</pre>

### Notes

On a suitable-configured IDEX printer, a scaling factor of -1 for the U axis can be used to turn a ditto print into a mirror image print.

## M580: Select Roland

*This M-code is not available by default. To enable it change the value of SUPPORT_ROLAND in the Pins_\*.h file from 0 to 1 and recompile the firmware.*

The [Modela MDX-20](https://www.rolanddga.com/support/products/milling/modela-mdx-20-3d-milling-machine.htm){target=_blank} and similar milling machines are very widely available in hackerspaces and maker groups, but annoyingly they don't speak GCodes. As all RepRap firmware includes a GCode interpreter, it is often easy to add functions to convert GCodes to RML.  

### Parameters

* **Rnnn** Whether Roland mode should be activated
* **Pnnn** Initial text to send to the Roland controller

### Examples
<br>
<pre class="cblock">
M580 R1 PVS4;!VZ2;!MC1;
</pre>

### Description

M580 selects a Roland device for output if the R field is 1, and returns to native mode if the R field is 0.

The optional P string is sent to the Roland if R is 1. It is permissible to call this repeatedly with R set to 1 and different strings in the P field to communicate directly with a Roland.

## M581: Configure external trigger

### Tabs {.tabset}

#### RepRapFirmware 3.01 and later

##### Parameters

* **P** Specify one or more input pin numbers that you created using M950 with the J parameter, or -1 to delete the trigger
* **Tnn** Logical trigger number to associate the input(s) with, from zero up to a firmware-specific maximum
* **S** Whether trigger occurs on an inactive-to-active edge of that input (S1, default), active-to-inactive edge (S0), or ignores that input (S-1).
* **R** Condition: whether to trigger at any time (R0, default), only when printing a file from SD card (R1), or only when not printing a file from SD card (R2, supported in RRF 3.2 and later). R-1 temporarily disables the trigger.
* **X**, **Y**, **Z** or any other axis letter: axis or axes whose endstop switches are to cause the trigger

##### Examples
<br>
<pre class="cblock">
M581 T2 P0:3 S1 R1 ; invoke trigger 2 when an inactive-to-active edge is detected on input 0 or input 3 and a file is being printed from SD card
M581 T3 X Y S1  ; invoke trigger 3 when the X or Y endstop switch is triggered
M581 T2 P-1 ; don't invoke trigger 2 on any input change any more
</pre>

##### Notes

* When M581 is executed, if the T parameter is present but the other parameters are omitted, the trigger inputs and edge polarities for that trigger number are reported. Otherwise, the specified inputs and their polarities are added to the conditions that cause that trigger.
* Trigger number 0 causes an emergency stop as if M112 had been received. Trigger number 1 causes the print to be paused as if M25 had been received. Any trigger number # greater than 1 causes the macro file sys/trigger#.g to be executed. Polling for further trigger conditions is suspended until the trigger macro file has been completed. RepRapFirmware does not wait for all queued moves to be completed before executing the macro, so you may wish to use the M400 command at the start of your macro file. If several triggers are pending, the one with the lowest trigger number takes priority.
* A maximum of 32 triggers can be configured on Duet 3, a maximum of 16 on Duet 2.
* **Warning**: if executed during a job, and more than one line long the GCode within the trigger file may be executed between later commands from the job. Bounding the trigger file with M25 and M24 may help, but this will cause warnings if the trigger happens outside of a job. The use of M25/M24 will cause the execution of pause and resume system macros.

#### RepRapFirmware 3.0

##### Parameters

* **P** Specify one or more pin names, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank}
* **Tnn** Logical trigger number to associate the endstop input(s) with, from zero up to a firmware-specific maximum
* **C** Condition: whether to trigger at any time (C0, default) or only when printing a file from SD card (C1)

##### Examples
<br>
<pre class="cblock">
M581 P"e0stop+e1stop" T2 C1 ; invoke trigger 2 when a rising edge is detected on the E1 or E2 endstop input and a file is being printed from SD card
M581 P"nil" T2 ; don't invoke trigger 2 on any input change any more
</pre>

##### Notes

* Use the P parameter to specify one or more pin names. Use P"nil" to disable that trigger number.
* The pin(s) do not need to be exclusively used by M581; for example, it is permitted to specify the name of a pin that has already been declared as used by an endstop switch in a M574 command.
* The S parameter used in RRF2.x is removed. The command waits for a low-to-high input transition. To wait for a high-to-low transition, invert the pin name using '!'.
* When M581 is executed, if the T parameter is present but the other parameters are omitted, the trigger inputs and edge polarities for that trigger number are reported. Otherwise, the specified inputs and their polarities are added to the conditions that cause that trigger.
* Trigger number 0 causes an emergency stop as if M112 had been received. Trigger number 1 causes the print to be paused as if M25 had been received. Any trigger number # greater than 1 causes the macro file sys/trigger#.g to be executed. Polling for further trigger conditions is suspended until the trigger macro file has been completed. RepRapFirmware does not wait for all queued moves to be completed before executing the macro, so you may wish to use the M400 command at the start of your macro file. If several triggers are pending, the one with the lowest trigger number takes priority.
* **Warning**: if executed during a build process, and more than one line long the GCode within the trigger file may be executed between later commands from the build file. Bounding the trigger file with M25 and M24 may help, but this will cause error warnings if the trigger happens outside of a build process. The use of M25/M24 will cause the execution of pause and resume system macros.

#### RepRapFirmware 2.x and earlier

##### Parameters

* **Tnn** Logical trigger number to associate the endstop input(s) with, from zero up to a firmware-specific maximum (e.g. 9 for RepRapFirmware)
* **X, Y, Z, E** Selects endstop input(s) to monitor
* **P** Reserved, may be used in future to allow general I/O pins to cause triggers
* **S** Whether trigger occurs on a rising edge of that input (S1, default), falling edge (S0), or ignores that input (S-1). By default, all triggers ignore all inputs.
* **C** Condition: whether to trigger at any time (C0, default) or only when printing a file from SD card (C1)

##### Examples
<br>
<pre class="cblock">
M581 E1:2 S1 T2 C1 ; invoke trigger 2 when a rising edge is detected on the E1 or E2 endstop input and a file is being printed from SD card
</pre>

##### Notes

* When M581 is executed, if the T parameter is present but the other parameters are omitted, the trigger inputs and edge polarities for that trigger number are reported. Otherwise, the specified inputs and their polarities are added to the conditions that cause that trigger. Using S-1 with no X Y Z or E parameters sets the trigger back to ignoring all inputs.
* Trigger number 0 causes an emergency stop as if M112 had been received. Trigger number 1 causes the print to be paused as if M25 had been received. Any trigger number # greater than 1 causes the macro file sys/trigger#.g to be executed. Polling for further trigger conditions is suspended until the trigger macro file has been completed. RepRapFirmware does not wait for all queued moves to be completed before executing the macro, so you may wish to use the M400 command at the start of your macro file. If several triggers are pending, the one with the lowest trigger number takes priority.
* A maximum of 16 triggers can be configured on Duet 2.
* **Warning**: if executed during a build process, and more than one line long the GCode within the trigger file may be executed between later commands from the build file. Bounding the trigger file with M25 and M24 may help, but this will cause error warnings if the trigger happens outside of a build process. The use of M25/M24 will cause the execution of pause and resume system macros.

## M582: Check external trigger

### Parameters

* **Tnn** Trigger number to poll
* **Sn** (optional, RRF 3.5 and later only) 0 = only trigger if the input states are at the correct level (default), 1 = trigger unconditionally

### Examples
<br>
<pre class="cblock">
M582 T2    ; check levels of inputs that give rise to trigger #2
M582 T3 S1 ; set trigger #3 pending unconditionally
</pre>

### Notes

Triggers set up by the M581 command are normally activated only when the specified inputs change state. This command provides a way of causing the trigger to be executed if the input is at a certain level. For each of the inputs associated with the trigger, the trigger condition will be checked as if the input had just changed from the opposite state to the current state. If the S1 parameter is used then the trigger will be activated unconditionally (RRF 3.5 and later only).

For example, if you use M581 to support an out-of-filament sensor, then M582 allows you to check for out-of-filament just before starting a print.

## M584: Set drive mapping

### Parameters

* **Xnnn** Driver number(s) for X motor(s)
* **Ynnn** Driver number(s) for Y motor(s)
* **Znnn** Driver number(s) for Z motor(s)
* **Ennn** Driver number(s) for E motor(s)
* **U, V, W, A, B, Cnnn** Driver number(s) for additional axes U, V, W, A, B and C (UVW available in RepRapFirmware 1.16 and later; UVWABC available in RepRapFirmware 1.19 and later; UVWABCD available in RepRapFirmware 3.0 and later; UVWABCDabcdefghijkl available in RepRapFirmware 3.3 and 3.4, Duet 3 MB6HC and MB6XD only; UVWABCDabcdef available in RepRapFirmware 3.5 and later, Duet 3 MB6HC and MB6XD only).
* **Rn** (optional, supported in RRF 3.2beta1 and later) 0 = axes creates in this command are linear, 1 = axes created are rotational. If not present, then RRF 3.2beta3 and later assume UVW are linear and ABCD are rotational.
* **Sn** (optional, supported in RRF 3.2beta3 and later) 0 = axes created in this command are treated as linear in feedrate calculations, 1 = axes created are treated as rotational in feedrate calculations. See section 2.1.2.5 of the NIST GCode standard for how the feedrate is interpreted. Default is S0 for linear axes and S1 for rotational axes (see the R parameter).
* **Pnnn** Number of visible axes, defaults to the total number of axes configured, excluding extruder drives.

### Order dependency

* M584 must come earlier in config.g than any M350 and M906 commands. If it creates new axes, it must also be earlier than any M92, M201, M203, M208, M350, M566, M574, M667 and M669 commands.

### Examples
<br>
<pre class="cblock">
M584 X0 Y1 Z2:3 E4:5:6 ; Driver 0 controls the X motor, 1 controls Y, 2 and 3 control Z motors, 4 5 and 6 control E motors
</pre>

### Notes

* **VERY IMPORTANT!** From **RRF 3.3**, X (driver 0), Y (driver 1) and Z (driver 2) are assigned by default. There are no default extruder drives; all extruder drives must be declared explicitly using M584. Changing an existing drive (i.e. X, Y or Z) to a different driver with an **existing** assignment will result in two axes using the same driver, e.g. M584 X1 results in 'Driver assignments: X1 Y1 Z2'. Changing a drive to an unassigned driver results in the drive moving to the new driver, e.g. M584 Z3 results in 'Driver assignments: X1 Y1 Z3'. This may result in unexpected behaviour. It is best practice to define all drives explicitly as in the above example, if you are not using the default drive/driver assignments.
* In **RRF 3.2.2 and earlier**, X (driver 0), Y (driver 1), Z (driver 2) and one extruder (driver 3) are assigned by default. Changing an existing drive (i.e. X, Y, Z or E) to a different driver with an **existing** assignment will result in two axes using the same driver. This may result in unexpected behaviour. It is best practice to define all drives explicitly as in the above example, if you are not using the default drive/driver assignments.
* You can use M584 to create additional axes - for example, to represent additional carriages on a machine with multiple independent X carriages. In 1.20 and later firmware you can create new axes in any order. In earlier firmware versions, additional axes must be created in the order UVWABC. 
* You can hide axes, starting with the last axis created, using the P parameter. Hidden axes have no homing buttons or jog controls in the user interface.
* If you create more than one axis in a M584 command, the axes are created in the order UVWABCD regardless of the order of the parameters in the M584 command. This affects which axes will be hidden if you use the M584 P parameter to hide axes. For example, M584 C5 U6 creates axes U and C in that order, so M584 P4 would hide the C axis, not the U axis. If you want to create the axes in the order C then U (so that M584 P4 hides the U axis), use two M584 commands: M584 C5 followed by M584 U6.
* RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour. Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M92 Z80, not M92 Z80:80
* Every driver that is assigned must have its current set using M906. Not setting a current will default a low current (approx 1/32 of the driver max current), however M906 will report 0 until a current is assigned. Disable the driver explicitly if you do not want any current sent to a driver that is assigned.
* The maximum number of axes supported on Duet 3 MB6HC is increased to 15 in RRF 3.3 and later. Axis letters abcdefghijkl may be used in addition to XYZUVWABCD. Because GCode is normally case insensitive, these must be prefixed with a single quote character in GCode commands. For example, M584 'A1.2 would assign axis 'a' to driver 1.2, and G1 'A10 would move the 'a' axis to the 10mm or 10 degree position (or by 10mm or 10 degrees if in relative mode).
* Using M584 to map drivers to axes does not affect endstop inputs. In RRF 3, endstops inputs for each axis need to be defined. In RRF 2, endstop inputs XYZ are pre-allocated, after that they are allocated in the order in which axes are created. So if you create just one extra axes (e.g. U), it will use the E0 endstop input. If more than one axis is created in a single M584 command, endstop inputs are allocated to the new axes in axis creation order (see previous item). For example, M584 C5 U6 would allocate endstop input E0 to the U axis and E1 to the C axis.
* On the Duet 2 WiFi and Duet 2 Ethernet, if you configure multiple drivers for an axis, either all of them must be TMC2660 drivers on the Duet or a Duet expansion board, or none of them must be. This is to facilitate dynamic microstepping and other features of the TMC2660.
* In RRF3, M584 works the same way as in RRF2, with exception that on Duet 3 the driver on expansion boards are assigned with \<board address>.\<driver number>. Example:
<br>
<pre class="cblock">
M584 X0 Y1 Z2 E3:4:1.0:1.1
</pre>

The "0" index for the main board is implicit, this is equivalent to the previous example:
<br>
<pre class="cblock">
M584 X0.0 Y0.1 Z0.2 E0.3:0.4:1.0:1.1
</pre>

* If you assign an axis or extruder to one or more drivers, and later you want to reassign it to a different driver, then to ensure correct operation you must disable those axes/extruders using M18 before using M584 to reassign them. After using M584, you must set the motor current using M906 and the microstepping using M350. Example:
<br>
<pre class="cblock">
; Here axis B and extruder E0 have already been assigned and possibly moved, but we now want to reassign them
M18 B E0      ; disable the axes/extruder that we are going to reassign
M584 B3 E4     ; reassign them
M906 B1000 E1000  ; set the motor currents
M350 B16 E16 I1  ; set the microstepping
</pre>

## M585: Probe Tool

*Supported in RRF 1.20 and later.*

In machines with at least one tool probe this code allows to update the current tool's offset by driving it into a given endstop. This code works similarly to G1 .. H1 (machine homing; G1 .. S1 is RRF 2.02 and earlier) except that it sets the offset of the current tool instead of the machine position, and that a custom endstop number (RRF 2.x) or custom Z probe (RRF 3.x) can be used.

### Tabs {.tabset}

#### RepRapFirmware 3.x

##### Parameters

* **Xnnn** - Probe tool in X direction where nnn specifies the expected distance between the trigger point of your endstop switch and the starting point
* **Ynnn** - Probe tool in Y direction where nnn specifies the expected distance between the trigger point of your endstop switch and the starting point
* **Znnn** - Probe tool in Z direction where nnn specifies the expected distance between the trigger point of your endstop switch and the starting point
* **U,V,W,A,B,Cnnn** - As for X,Y,Z above
* **Pn** - Z probe number to use (optional)
* **Fnnn** - Requested feedrate of the probing move. If this parameter is omitted, the last set feedrate is used
* **Snnn** - Direction of the probing move. S=0 (default) means travel forwards (towards the axis maximum), S=1 means go backwards (towards the axis minimum)
* **Rnnn** - Probing radius, i.e. the relative movement amount from the current position (optional, if used the S parameter is ignored)

##### Examples
<br>
<pre class="cblock">
M558 K1 P5 C"!e0stop" ; create probe #1 for use with M585, active low
...
M585 X100 F600 P1 S0 ; probe X until probe #1 is triggered
</pre>

##### Notes

* You can only specify one axis per M585 call and that XYZ are not the only possible axes for this code (UVWABC would be valid as well).
* The values of the XYZ parameters are the absolute distances between the position at which the endstop is actually triggered and your own start position. It is mandatory to measure this distance once before M585 can be used reliably. An example: Say you wish to probe the tool offset on the X axis. If the trigger position of your endstop is at X=210 and you want to drive your tool from X=190 into the endstop switch, you need to specify -20 as your X parameter because you expect to travel 20mm towards the endstop switch and need to correct this factor. If you drive the tool backwards (e.g. from X=210 to X=190), the correction factor should be 20.
* You can use M585 to probe until a regular axis endstop is triggered.
* If you want to probe until a custom input is triggered, use M558 to configure an additional probe that uses that pin, then refer to that probe in your M585 command. See example above.
* In principle the following workflow should be performed for each axis using a macro file. You may wish to enhance this workflow depending on your own requirements and endstop configuration.
  * Reset the axis tool offset (G10 Pxx X0 Y0 Z0)
  * Select your tool (Txx)
  * Move the tool to your starting position (G1 X?? Y?? Z?? F3000)
  * Drive the tool into the endstop or custom input, stop there and apply the new tool offset with the given correction factor (M585 XYZ?? F1000 P??)
  * Call G10 Pxx with your tool number to get the corrected tool offset or call M500 (supported in RRF 1.20beta3 and later) to store the probed tool offsets on the SD card

#### RepRapFirmware 2.x and earlier

##### Parameters

* **Xnnn** - Probe tool in X direction where nnn specifies the expected distance between the trigger point of your endstop switch and the starting point
* **Ynnn** - Probe tool in Y direction where nnn specifies the expected distance between the trigger point of your endstop switch and the starting point
* **Znnn** - Probe tool in Z direction where nnn specifies the expected distance between the trigger point of your endstop switch and the starting point
* **U,V,W,A,B,Cnnn** - As for X,Y,Z above
* **Ennn** - Custom endstop number to use (optional). This must be the drive number of the according endstop (i.e. X=0, Y=1, Z=2, E0=3 etc.)
* **Lnnn** - Trigger level of the custom endstop (optional, 0 = active-low, 1 = active-high). This requires the 'E' parameter to be present [RRF 2.04 and later]
* **Fnnn** - Requested feedrate of the probing move. If this parameter is omitted, the last set feedrate is used
* **Snnn** - Direction of the probing move. S=0 (default) means travel forwards (towards the axis maximum), S=1 means go backwards (towards the axis minimum)
* **Rnnn** - Probing radius, i.e. the relative movement amount from the current position (optional, if used the S parameter is ignored) [requires RRF 1.20beta8 or later]

##### Examples
<br>
<pre class="cblock">
M585 X100 F600 E3 L0 S0 ; probe X until E0 endstop goes low
</pre>

##### Notes

* You can only specify one axis per M585 call and that XYZ are not the only possible axes for this code (UVWABC would be valid as well).
* The values of the XYZ parameters are the absolute distances between the position at which the endstop is actually triggered and your own start position. It is mandatory to measure this distance once before M585 can be used reliably. An example: Say you wish to probe the tool offset on the X axis. If the trigger position of your endstop is at X=210 and you want to drive your tool from X=190 into the endstop switch, you need to specify -20 as your X parameter because you expect to travel 20mm towards the endstop switch and need to correct this factor. If you drive the tool backwards (e.g. from X=210 to X=190), the correction factor should be 20.
* In case you are using different switches for tool probing, RepRapFirmware allows you to use a custom endstop. If a different endstop than the axis endstop is used, the drive number of the matching endstop can be specified via the optional E parameter (e.g. E4 for the E1 endstop).
* In principle the following workflow should be performed for each axis using a macro file. You may wish to enhance this workflow depending on your own requirements and endstop configuration.

* Reset the axis tool offset (G10/M568 Pxx X0 Y0 Z0)
  * Select your tool (Txx)
  * Move the tool to your starting position (G1 X?? Y?? Z?? F3000)
  * Drive the tool into the endstop, stop there and apply the new tool offset with the given correction factor (M585 XYZ?? F1000 E??)
  * Call G10 Pxx with your tool number to get the corrected tool offset or call M500 (supported in RRF 1.20beta3 and later) to store the probed tool offsets on the SD card

## M586: Configure network protocols

### Parameters

* **Inn** Interface number (RRF 3 and later, defaults to 0, only supported in standalone mode)
* **Pnn** Protocol: 0 = HTTP or HTTPS, 1 = FTP or SFTP, 2 = Telnet or SSH (which of the two choices depends on the T parameter), 3 = multicast discovery (OEM-specific), 4 = MQTT (see M586.4 below)
* **Snn** 0 = disable this protocol, 1 = enable this protocol
* **Hnn** Remote server IP address (only applicable for MQTT, see also M586.4)
* **Rnn** TCP port number to use for the specified protocol. Ignored unless S = 1. If this parameter is not provided then the default port for that protocol and TLS setting is used. When S=0 the default port numbers are 80, 21 and 23 respectively.
* **Tnn** 0 = don't use TLS, 1 = use TLS. Ignored unless S = 1. If this parameter is not provided, then TLS will be used if the firmware supports it and a security certificate has been configured. If T1 is given but the firmware does not support TLS or no certificate is available, then the protocol will not be enabled and an error message will be returned.
* **C"\<site>"** (RRF 3.2 and later only) Set or reset allowed site for cross-origin HTTP requests

**Note**: TLS has not yet been implemented in RepRapFirmware, therefore T1 will not work.

### Examples
<br>
<pre class="cblock">
M586 P0 S1 ; enable HTTP
M586 P1 S0 ; disable FTP
M586 P2 S1 ; enable Telnet 
</pre>

### Notes

* In SBC mode, sending this command makes a persistent change. It does not need to be added to dsf-config.g. It should NOT be included in config.g.
* In SBC mode, `M586 I` is not supported. Configure ufw or another firewall to restrict protocol access per adapter if required.
* M586 with no S parameter reports the current support for the available protocols.
* RepRapFirmware 1.18 and later enable only HTTP (or HTTPS if supported) protocol by default. If you wish to enable FTP and/or Telnet, enable them using this command once or twice in config.g.

## M586.4: Configure MQTT Client

*Supported from firmware version 3.5, on Duet WiFi boards in standalone mode. Requires the WiFi interface to be running WiFi server version 2.1 or later.*

### Parameters

* **U"username"** Username for authenticating with MQTT broker that does not allow anonymous login (not needed if broker supports anonymous login)
* **K"password"** Password for authenticating with MQTT broker that does not allow anonymous login (only processed if the `U` parameter is also present; not needed if broker supports anonymous login).
* **C"client-id"** Set fixed MQTT client ID, used with persistent sessions (not yet supported).
* **W"will-message"** Set message to send to subscribers when MQTT client does not disconnect from the broker gracefully (such as sudden shutdown of the board, network loss, etc). If not set, no message is sent to subscribers.
* **T"will-topic"** The topic to publish the will message under (only processed if the `W` parameter is also present).
* **Q"will-qos"** QOS level of the will message, from `0` to `2` (only processed if the `W` parameter is also present, optional). Defaults to `0` if not specified.
* **R"will-retain"** Set retain flag of the will message, `1` or `0` (only processed if the `W` parameter is also present, optional). Defaults to `0` if not specified.
* **S"subscribe-topic"** Add topic to subscribe to.
* **On** Max QOS level of the subscription, from `0` to `2` (only processed if the `S` parameter is also present).


### Example

<br>
<pre class="cblock">
M586.4 C"duet"                      ; Set client ID
M586.4 U"username" K"password"      ; Set authentication credentials
M586.4 S"subscription" O2           ; Subscribe to topic
M586 P4 R1884 H192.168.10.244 S1    ; Enable MQTT protocol/client
M118 P6 S"message" T"topic"         ; Publish message (See M118 for more details)
M586 P4 S0                          ; Disable MQTT protocol/client; disconnects from broker gracefully.
</pre>

For a full demonstration, see [this GitHub repository](https://github.com/Duet3D/MQTT-WPA2-Enterprise-Demo).

## M587: Add WiFi host network to remembered list, or list remembered networks

**This command must not be used in the config.g file.**

### Parameters

* **S"ccc"** Network SSID (case sensitive)
* **P"ccc"** Network password (case sensitive)
* **Inn.nn.nn.nn** (optional) IP address to use when connected to this network. If zero or not specified then an IP address will be acquired via DHCP.
* **Jnn.nn.nn.nn** (optional) Gateway IP address to use when connected to this network.
* **Knn.nn.nn.nn** (optional) Netmask to use when connected to this network
* **Lnn.nn.nn.nn** (optional, supported only by DuetPi + DSF v3.3 or newer) DNS server to use
* **Cnnn** (supported only by DuetPi + DSF v3.3 or newer) Country code for the WiFi adapter, only required if not set before
* **Fn** (optional) Format of the response when M587 is used to report the remembered list: 0=plain text (default), 1=JSON
* **Xn** (optional, RRF 3.5beta4 and later only, standalone mode only) Authentication mode, default 0 (WPA2-PSK)

The SSID and password must always be enclosed in double quotation marks.

RepRapFirmware 3.5beta4 and later with WiFiServer 2.1beta3 or later support WiFi Enterprise Authentication experimentally. The modes supported are:

* EAP-TLS (only on ESP32-C3) - certificates-based authentication; needs a client certificate, private key and an optional private key password
* EAP-TTLS-MSCHAPv2 - username and password authentication
* EAP-PEAP-MSCHAPv2 - username and password authentication

All three protocols have an option to specify an anonymous identity and CA validation certificate.

When using one of these modes there are additional parameters and the meaning of the P parameter may be changed, as follows:

* **Xn** Authentication mode: 0=WPA2-PSK, 1=EAP-TLS (supported on ESP32-S3 WiFi modules only), 1=EAP-PEAP-MSCHAPv2, 3=EAP-TTLS-MSCHAPv2
* **E"eee"** Filename of CA validation certificate in /sys directory of SD card
* **A"aaa"** Anonymous identity

### Protocol-specific parameters:

**EAP-TLS**

* **U"uuu"** Filename of client/user certficate in /sys directory
* **P"ppp"** Filename of private key file in /sys directory
* **Q"qqq"** Private key password

**EAP-TTLS-MSCHAPv2 and EAP-PEAP-MSCHAPv2**

* **U"uuu"** Username
* **P"ppp"** Password

### Examples
<br>
<pre class="cblock">
M587 S"Network-ssid-123" P"Password123" I192.128.1.200  ; connect to access point "Network-ssid-123" using WPA2-PSK authentication
M587 X2 E"ca.pem" S"test-ap" U"bob" P"hello"  ; Connect to AP "test-ap" with EAP-PEAP-MSCHAPv2, username "bob" and password "hello". Perform CA validation with certificate sys/ca.pem
</pre>

### Notes

* In SBC mode (v3.3 and later) it is not possible to configure different IP addresses per SSID.
* In SBC mode, sending this command makes a persistent change. It does not need to be added to dsf-config.g. It should NOT be included in config.g.
* Many programs used to send GCodes convert all characters to uppercase. In firmware 1.19.2 and later, within any quoted string you can use a single-quote character to indicate that the following character should be changed to lowercase. For example, M587 S"ABC" P"P'A'S'SW'O'R'D" would specify that the password is "PassWord". Use two single quote characters to represent one actual single quote character in the password or in the SSID. For example, if your SSID is "Pete's network" then enter "Pete''s network".
* The use of special characters in the SSID cannot be guaranteed to work. In general it's best to avoid most special characters. Spaces, periods, dashes, underscores, and other punctuation is likely ok, but special characters on the number keys likely are not safe. (@#$%^&\*). If you are having troubles adding your SSID, try a simplified version with only letters and numbers.
* M587 with no parameters lists all the remembered SSIDs, but not the remembered passwords.
* The M587 command will fail if the WiFi module has not yet been taken out of reset. So if the WiFi module has not been started, send M552 S0 to put it in idle mode first.
* When connecting to an open network with no password, M587 still requires a password in the P parameter. However, it doesn't matter what password you provide as long as it meets the minimum length requirement for M587.

**Important!** Do not use M587 within config.g. As well as being a security hazard, writing the access point parameters to WiFi chip every time you start the Duet may eventually wear out the flash memory. Also, the wifi module does not get enabled until the end of running config.g (see [this forum thread](https://forum.duet3d.com/post/42798){target=_blank} for explanation). It is better to use a macro to send M587.

## M587.1: Start network scan

**This command must not be used in the config.g file.**

Supported only by RRF 3.5 or later with WiFi firmware 2.0 or later.

### Parameters

None

This command causes the WiFi module to start a network scan. During the scan, any existing WiFi connection will be lost until the scan is complete. If starting ths command from Duet Web Control connected over WiFi, wait for the reconnection before sending M587.2.

## M587.2: Return network scan results

**This command must not be used in the config.g file.**

Supported only by RRF 3.5 or later with WiFi firmware 2.0 or later.

### Parameters

* **Fn** 0 = report in plain text (default), 1 = report in JSON format

This command waits for any active wifi scan to compete, then returns the result. The JSON format is intended for use by Duet Web Control, PanelDue and other user interfaces.

## M588: Forget WiFi host network

**This command must not be used in the config.g file**

### Parameters

* **S"ccc"** Network SSID to remove from the remembered list

### Examples 
<br>
<pre class="cblock">
M588 S"Network-ssid-123"
M588 S"*"
</pre>

### Notes

* The specified SSID will be removed from the remembered list and the associated password cleared out of EEPROM. If the SSID is given as "\*" then all remembered networks will be forgotten.
* Removing the SSID of the access point that the Duet is connected to will not disconnect it from the access point (but future attempts to connect to it will fail unless it is added back).
* The M588 command will fail if the WiFi module has not yet been taken out of reset. So if the WiFi module has not been started, send M552 S0 to put it in idle mode first. M588 does not work from within config.g at startup.
* In SBC mode, sending this command makes a persistent change. It does not need to be added to dsf-config.g. It should NOT be included in config.g.

## M589: Configure access point parameters

**This command must not be used in the config,g file**

### Parameters

* **S"ccc"** The SSID that the WiFi interface should use when it is commanded to run as an access point
* **P"ccc"** The WiFi password (must be at least 8 characters long)
* **Inn.nn.nn.nn** The IP address to use
* **Cnn** The WiFi channel to use (optional)

### Examples 
<br>
<pre class="cblock">
M589 S"DuetSSID" P"password" I192.168.0.1 C1
</pre>

### Usage

**To use AP mode:**
* Send a M589 command once from the console, or via macro to set the access point name, IP address etc. These parameters will be saved within the WiFi module.
* The password must be at least 8 characters long. See notes of [M587](/User_manual/Reference/Gcodes/M587) for valid characters.
* The M589 command will fail if the WiFi module has not yet been taken out of reset. So if the WiFi module has not been started, send M552 S0 to put it in idle mode first.
* M589 does not work from within config.g at startup.
* Use M552 S2 in config.g to start the wifi module.
* WPA2 security will be used by default.
* Look for the wireless network name you specified on your device and connect to it using the password you set.

### Notes

* In SBC mode, sending this command makes a persistent change. It does not need to be added to dsf-config.g. It should NOT be included in config.g.

## M591: Configure filament sensing

This command configures a given pin to read a filament sensor and configures filament monitoring for its corresponding extruder. The filament sensor may be a simple switch that detects the presence of filament, or a sensor that measures filament motion (e.g. laser, magnetic or pulsed filament monitor), or both.

In **RRF 3.4 and later** the action on a filament error is to raise a filament-error event. See [Events](/User_manual/RepRapFirmware/Events){target=_blank}.

In **RRF 3.3 and 3.2** the action on a filament error is to.

* pause the print
* run filament-error#.g if available, where # is the extruder number
* failing that run filament-error.g if available
* failing that show a message on all available targets with the type of filament error and invoke system macro pause.g. The job is paused and will need manual intervention to resume the print.

**RRF 1.19 to 3.1.1** does not support filament-error macros. The action on a filament error is to enter the Pausing state, show a message on all available targets with the type of filament error, and invoke system macro pause.g. The job is paused and will need manual intervention to resume the print.
Note that filament monitoring in RRF is only active when printing from SD card.

### Tabs {.tabset}

#### M591 - RepRapFirmware 3.x

##### Parameters

* **Dnn** Extruder drive number (0, 1, 2...)
* **Pnn** Type of sensor:
  * 0 = none
  * 1 = simple sensor (high signal when filament present)
  * 2 = simple sensor (low signal when filament present)
  * 3 = Duet3D rotating magnet sensor
  * 4 = Duet3D rotating magnet sensor with microswitch
  * 5 = Duet3D laser sensor
  * 6 = Duet3D laser sensor with microswitch
  * 7 = pulse-generating sensor
* **C"name"** Pin name the filament sensor is connected to (RRF3 only), see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank}. DueX2/5 users, see Notes below.
* **Sn** 0 = disable filament monitoring (default), 1 = enable filament monitoring when printing from SD card, 2 = enable filament monitoring all the time (S2 is supported RRF 3.5.0-rc.1 and later only). Filament monitors accumulate calibration data (where applicable) even when filament monitoring is disabled.

**Additional parameters for Duet3D laser filament monitor**

* **Raa:bb** Allow the filament movement reported by the sensor to be between aa% and bb% of the commanded values; if it is outside these values and filament monitoring is enabled, the print will be paused
* **Enn** minimum extrusion length before a commanded/measured comparison is done, default 3mm
* **An** 1 = check All extruder motion, 0 (default) = only check extruder motion of printing moves (moves with both movement and forward extrusion)
* **Lnn** (firmware 3.2 and later) Calibration factor, default 1.0. The filament movement reported by the laser sensor is multiplied by this value before being compared with the commanded extrusion. Intended for use with sensors that use the laser to read movement of a wheel that is turned by the filament.

**Additional parameters for Duet3D rotating magnet filament monitor**

* **Lnn** Filament movement per complete rotation of the sense wheel, in mm
* **R, E, A** As for Duet3D laser filament monitor

**Additional parameters for a pulse generating filament monitor**

* **Lnn** Filament movement per pulse in mm
* **R, E** As for Duet3D laser filament monitor

##### Examples
<br>
<pre class="cblock">
M591 P3 C"e0stop" S1 D0 ; filament monitor connected to E0 endstop
M591 D0 ; display filament sensor parameters for extruder drive 0
</pre>

#### M591 - RepRapFirmware 1.21 to 2.x

##### Parameters

As RRF3, except 'C' parameter is the endstop number.

* **Cnn** Which input the filament sensor is connected to. On Duet electronics: 0=X endstop input, 1=Y endstop input, 2=Z endstop input, 3=E0 endstop input etc. DueX2/5 users, see Notes below.

##### Examples
<br>
<pre class="cblock">
M591 D0 P3 C3 S1 R70:130 L24.8 E3.0 ; Duet3D rotating magnet sensor for extruder drive 0 is connected to E0 endstop input, enabled, sensitivity 24.8mm.rev, 70% to 130% tolerance, 3mm detection length
M591 D0 ; display filament sensor parameters for extruder drive 0
</pre>

#### M591 - RepRapFirmware 1.20 and earlier

##### Parameters

* **Dnn** Extruder drive number (0, 1, 2...)
* **Pnn** Type of sensor, where:
  * 0 = none
  * 1 = simple sensor (high signal when filament present)
  * 2 = simple sensor (low signal when filament present)
  * 3 = Duet3D rotating magnet sensor
  * 4 = Duet3D rotating magnet sensor with microswitch
* **Cn** Which input the filament sensor is connected to. On Duet electronics: 0 = X endstop input, 1 = Y endstop input, 2 = Z endstop input, 3 = E0 endstop input etc. DueX2/5 users, see Notes below.

**Additional parameters for Duet3D rotating magnet filament monitor**

* **Snn** Sensitivity, in mm of filament movement per complete rotation of the sense wheel.
* **Rnn** Tolerance as a percentage of the commanded extrusion amount. A negative value puts the firmware in calibration mode.
* **Enn** minimum extrusion length before a commanded/measured comparison is done, default 3mm

##### Examples
<br>
<pre class="cblock">
M591 D0 P5 C3 R70:140 E3.0 S1 ; Duet3D rotating magnet sensor for extruder drive 0 is connected to E0 endstop input, sensitivity 1.05, tolerance 70% to 140%, 3mm detection length
M591 D1 ; display filament sensor parameters for extruder drive 1
</pre>

### Notes

* DueX2/5: Endstop inputs on the DueX expansion board (duex.e[2-6]stop in RRF 3.x, C5 thru C9 in RRF 2.x) can only be used for simple filament presence sensors (e.g. microswitch), not for sensors that detect motion (e.g. rotation or laser sensor). However, the endstop inputs on the Duet 2 WiFi/Ethernet CONN_LCD connector (connlcd.4 and connlcd.3 in RRF3.x, C10 and C11 in RRF 2.x) support any filament sensor.
* To free a filament sensor's GPIO pin, run M591 D# P0, where # is the corresponding extruder.

### Documentation

* [Duet3d Rotating Magnet Filament Monitor](/Duet3D_hardware/Accessories/Rotating_Magnet_Filament_Monitor){target=_blank}
* [Duet3d Laser Filament Monitor](/Duet3D_hardware/Accessories/Laser_Filament_Monitor){target=_blank}
* [Connecting and configuring filament-out sensors](/User_manual/Connecting_hardware/Sensors_filament){target=_blank}

## M592: Configure nonlinear extrusion

### Parameters

* **Dnn** Extruder drive number (0, 1, 2...)
* **A**nnn A coefficient in the extrusion formula, default zero
* **B**nnn B coefficient in the extrusion formula, default zero
* **L**nnn Upper limit of the nonlinear extrusion compensation, default 0.2
* **T** nnn Reserved for future use, for the temperature at which these values are valid

### Examples
<br>
<pre class="cblock">
M592 D0 A0.01 B0.0005 ; set parameters for extruder drive 0<br>
M592 D0 ; report parameters for drive 0
</pre>

### Notes

Most extruder drives use toothed shafts to grip the filament and drive it through the hot end. As the extrusion speed increases, so does the back pressure from the hot end, and the increased back pressure causes the amount of filament extruded per step taken by the extruder stepper motor to reduce. This may be because at high back pressures, each tooth compresses and skates over the surface of the filament for longer before it manages to bite. See [RepRap forum post here](http://forums.reprap.org/read.php?262,802277){target=_blank} and the [graph here](http://forums.reprap.org/file.php?262,file=100851,filename=graph.JPG){target=_blank} for an example.

Nonlinear extrusion is an experimental feature in RepRapFirmware to compensate for this effect. The amount of extrusion requested is multiplied by (1 + min(L, A*v + B*v^2)) where v is the requested extrusion speed (calculated from the actual speed at which the move will take place) in mm/sec.

Nonlinear extrusion is not applied to extruder-only movements such as retractions and filament loading.

## M593: Configure Input Shaping

The purpose of input shaping is to reduce ringing (also called ghosting).

### Tabs {.tabset}

#### RepRapFirmware 3.3 and later

##### Parameters

* **P"type"** Type of input shaping to use, not case sensitive. 
RRF 3.4 supports "none", "zvd", "zvdd", "zvddd", "mzv", "ei2", "ei3" and "custom".
RRF 3.3 supports "none" or "daa", and if no P parameter is given but the F parameter is given then "daa" is assumed, for compatibility with previous releases. 
* **Fnnn** Frequency of ringing to cancel in Hz
* **Snnn** (optional) Damping factor of ringing to be cancelled, default 0.1.
* **Lnnn** (optional) Minimum acceleration allowed, default 10mm/sec^2. Input shaping will not be applied if it requires the average acceleration to be reduced below this value.
* **Hnn:nn...** Amplitudes of each impulse except the last, normally below 1.0. Only used with P"custom" parameter.
* **Tnn:nn** Durations of each impulse except the last. Only used with P"custom" parameter.

##### Examples

**RRF 3.4 and later**
<br>
<pre class="cblock">
M593 P"zvd" F40.5 ; use ZVD input shaping to cancel ringing at 40.5Hz
M593 P"none"      ; disable input shaping
M593 P"custom" H0.4:0.7 T0.0135:0.0135 ; use custom input shaping
</pre>

**RRF 3.3**
<br>
<pre class="cblock">
M593 P"daa" F40.5 ; use DAA to cancel ringing at 40.5Hz
M593 P"none"      ; disable DAA
</pre>

##### Information about the Input Shapers

| Input Shaper | Shaper Duration | Vibration reduction with default damping factor (0.1) |
|:---|:---|
| ZVD | 1 / Frequency | ± 15% Frequency |
| ZVDD | 1.5 / Frequency | |
| ZVDDD | 2 / Frequency | |
| MZV | 1 / Frequency | ± 4% Frequency |
| EI2 | 1.5 / Frequency | ± 35% Frequency |
| EI3 | 3 / Frequency | ± -45%...+50% Frequency |

#### RepRapFirmware 3.2 and earlier

##### Parameters

* **Fnnn** Frequency of ringing to cancel by DAA, in Hz. Zero or negative values disable DAA.
* **Lnnn** Minimum acceleration allowed, default 10mm/sec^2. DAA will not be applied if it requires the average acceleration to be reduced below this value.

**Example (RRF 3.2 and earlier)**
<br>
<pre class="cblock">
M593 F40.5  ; use DAA to cancel ringing at 40.5Hz
</pre>

Note: In firmware 2.02 up to 3.3 the only form of input shaping supported is Dynamic Acceleration Adjustment (DAA). By default, DAA is disabled. If it is enabled, then acceleration and deceleration rates will be adjusted per-move to reduce ringing at the specified frequency. Acceleration limits set by M201 and M204 will still be honoured when DAA is enabled, so DAA will only ever reduce acceleration. Therefore your M201 and M204 limits must be high enough so that DAA can reduce the acceleration to the optimum value. Where possible DAA reduces the acceleration or deceleration so that the time for that phase is the period of the ringing. If that is not possible because of the acceleration limits, it tries for 2 times the period of the ringing.

### Notes

Input shaping not working for your printer? Check this:
* High X and Y jerk values are an issue for all types of Input Shaping because the theory behind IS assumes no jerk. Therefore you should set the X and Y jerk limits only as high as necessary to allow curves to be printed smoothly. Users report jerk values of 5mm/s (300mm/min) seem to allow for IS to work, and curves to print smoothly, though test it works for you.
* Another cause of IS not working is mesh compensation with a fine mesh and low acceleration. This splits the acceleration and deceleration parts of a move across multiple segments, which makes it difficult for RRF to apply IS.
* A third cause is short accelerate/decelerate moves. This is being addressed in RRF 3.5.0-rc.1.

Input shaping is most useful to avoid exciting low-frequency ringing, for which S-curve acceleration is ineffective and may make the ringing worse. High-frequency ringing would be better countered by using S-curve acceleration; however, low-frequency ringing is more of a problem in most 3D printers.

The ringing frequencies are best measured using an accelerometer, for which support is provided in RRF 3.3 and later. 

If you don't have an accelerometer, take a print that exhibits ringing on the perimeters (for example a cube), preferably printed single-wall or external-perimeters-first. Divide the speed at which the outer perimeter was printed (in mm/sec) by the distance between adjacent ringing peaks (in mm). When measuring the distance between peaks, ignore peaks close to the corner where the ringing started (these peaks will be spaced more closely because the print head will have been accelerating in that area).

Cartesian and CoreXY printers will typically have different frequencies of ringing for the X and Y axes. In this case it is is usually best to aim to cancel the lower ringing frequency. If the frequencies are not much different, in a moving-bed Cartesian printer you can reduce the higher ringing frequency by adding mass to that axis or reducing belt tension on that axis. Note that X axis ringing causes artefacts predominantly on the Y face of the test cube, and vice versa.

Keep in mind that you have to retune Pressure Advance after you have configured Input Shaping. The Pressure Advance will differ from shaper to shaper and from frequency to frequency.

See also: [Connecting an accelerometer](/User_manual/Connecting_hardware/Sensors_Accelerometer){target=_blank} and [Input shaping](/User_manual/Tuning/Input_shaping){target=_blank}

## M594: Enter/Leave Height Following mode

Height following mode allows the Z position of the tool to be controlled by a PID controller using feedback from a sensor. See also [M951](/User_manual/Reference/Gcodes/M951){target=_blank} for configuration.

### Parameters

* **Pn** P1 = enter height following mode, P0 = leave height following mode

### Notes

If a movement command (e.g. G1) explicitly mentions the Z axis while height following mode is active, existing moves in the pipeline will be allowed to complete and the machine allowed to come to a standstill. Then height following mode will be terminated and the new move executed.

## M595: Set movement queue length

*Supported in RRF 3.2 and later.*

Different features of motion control firmware may have competing demands on microcontroller RAM. In particular, operations that use many short segments (e.g. laser rastering) need longer movement queues than typical 3D printing, but have fewer motors to control. This command allows the movement queue parameters to be adjusted so that the queue can be lengthened if necessary, or kept short if a long movement queue is not needed and there are other demands on RAM.

### Parameters

* **Pnn** Maximum number of moves held in the movement queue. RepRapFirmware uses this value to determine how many DDA objects to allocate.
* **Snn** (optional) Number of pre-allocated per-motor movement objects. If the number of pre-allocated objects is insufficient, RepRapFirmware will attempt to allocate additional ones dynamically when they are needed.
* **Rnnn** Grace period in milliseconds (supported in RRF 3.3 and later). When filling the movement queue from empty, the system waits for this amount of time after the last movement command was received before starting movement. This is to allow the movement queue to fill more before movement is started when commands are received from USB, Telnet or another serial channel. It should not be needed when processing a GCode file from the SD card.
* **Qn** (optional, RRF3.3 and later) Movement queue number, default 0. Some builds of RRF have a secondary movement queue. You can configure the length of that queue by specifying Q1.

### Notes

M595 without any parameters reports the length of the movement queue and the number of per-motor movement objects allocated.

## M596: Select movement queue number

*Supported from firmware version 3.5 on Duet 3 main boards*

### Parameters
* **Pnn** Movement queue number. Queues are numbered 0 (the default queue), 1, ...

### Notes
This command is supported in RepRapFirmware 3.5 and later builds that can execute moves on different axis systems asynchronously, for example for concurrent processing of two or more actions. It specifies that subsequent GCode commands from this input channel should be routed to the specified movement queue and the tool associated with that queue.

The number of available queues is firmware-dependent but will typically be 2. Before using a movement queue other than queue 0 it may be necessary to use M595 to increase the length of that queue, because the default length of movement queues other than the primary one may be quite short.

At the start of a file print, queue 0 is selected automatically.

M596 without the P parameter reports the current movement queue number for the input channel that the command was received on.

## M597: Collision avoidance

*Supported from firmware version 3.5 on Duet 3 main boards*

### Parameters

* X,Y...aaa First axis identifier and value
* U,V...bbb Second axis identifier and value

### Description

This configuration command is intended for use in systems having multiple tool heads that can be moved independently and asynchronously. The axis letters must be different from each other, so must the values of aaa and bbb. Normally, aaa will be zero and bbb will be positive. The command specifies that the machine position of the axis with the higher value must always be at least the difference in values greater than the position of the other axis. If this is not the case, the job will be aborted prior to starting the first move that would cause the conflict.

### Example

<br>
<pre class="cblock">
M597 V0 Y23.5
</pre>

In the above example, the position of the Y axis must always be at least 23.5mm greater than the position of the V axis.

When Y and V are driven by independent motion systems and executing moves independently, in any block of GCode between synchronisation points, using this example the minimum of all Y coordinates inside the block (including the initial Y coordinate) must be at least 23.5mm greater than the maximum of the all V coordinates inside the block. If this is not the case, the job will be aborted prior to starting the first move that would cause the conflict.

## M598: Synchronise motion systems

*Supported from firmware version 3.5 on Duet 3 main boards*

### Parameters

None

### Description

This command is used in print files when multiple movement systems are being used (see the M596 command). It causes all motion systems to complete all queued movements and execute no further instructions until they have all reached this command and are ready to proceed.

## M599: Define keepout zone

*Supported from firmware version 3.5b2 on Duet 3 main boards*

### Parameters

* **Pn** Keepout zone number, default 0
* **Sn** (optional) S1 = activate keepout zone (default if any axes are specified), S0 = deactivate keepout zone
* **X,Y...aaa:bbb** Axis identifier and limits for that axis

### Example

<br>
<pre class="cblock">
M599 X10:25 Y0:20
</pre>

### Description

This command establishes a "no entry" zone for the toolhead reference point. If any G0/G1/G2/G3 move attempts to move the toolhead reference point inside the no entry zone, the job will be aborted with an error message. In other words, in 3.5b2, the X, Y etc coordinates are in machine coordinates.

### Notes

* You may specify any number of axes, up to the number that the machine has. 
* If no axes are specified and the S parameter is not provided then the parameters and enabled/disabled state of the existing keepout zone will be reported.
* Movement commands (G0, G1, G2 and G3) will normally be checked before starting the move.
* The number of keepout zones supported is implementation dependent. In RRF 3.5b2 only one is supported.

## M600: Filament change pause

*Supported in firmware 2.02 and later.*

This command behaves like [M226](/User_manual/Reference/Gcodes/M226){target=_blank} except that if macro file filament-change.g exists in /sys on the SD card, it is run in preference to pause.g. 

## M650: Set peel move parameters

This command is sent by nanoDLP to set the parameters for the peel move used after curing a layer. RepRapFirmware 2.02 ignores this command. If you use RepRapFirmware 2.03 with nanoDLP, create an empty M650.g file in the /sys folder of the SD card so that RRF will ignore it without emitting an error message.

## M651: Execute peel move

This command is sent by nanoDLP to execute a peel move after exposing a layer. RepRapFirmware 2.02 executes macro /sys/peel-move.g in response to this command. To use RepRapFirmware 2.03 or later with nanoDLP, create a macro file M651.g in the /sys folder of the SD card and populate it with the commands needed to execute the peel move.

## M665: Set delta configuration

Set the delta calibration variables

### Parameters

* **Lnnn** or **Lnnn:nnn:...** Diagonal rod length
* **Rnnn** Delta radius
* **Bnnn** Safe printing radius
* **Hnnn** Nozzle height above the bed when homed after allowing for endstop corrections
* **Xnnn** X tower position correction
* **Ynnn** Y tower position correction
* **Znnn** Z tower position correction

### Examples
<br>
<pre class="cblock">
M665 L250 R160 B80 H240 X0 Y0 Z0
</pre>

### Notes

The **X**, **Y** and **Z** parameters are the X, Y and Z tower angular offsets from the ideal (i.e. equilateral triangle) positions, in degrees, measured anti-clockwise looking down on the printer.

In RRF 2.03 and later, multiple **L** values can be provided, for example:
<br>
<pre class="cblock">
L260.1:260.2:260.0
</pre>
The values are the lengths of the rods to the X, Y and Z towers respectively. If more than 3 values are provided, the firmware assumes that there are as many towers as L values up to the maximum supported (currently 6). The XY coordinates of the additional towers must be defined subsequently using the M669 command. If only one L value is provided, the machine is assumed to have 3 towers with all rods having the same length.

## M666: Set delta endstop adjustment

Sets delta endstops adjustments.

### Parameters

* **Xnnn** X axis endstop adjustment
* **Ynnn** Y axis endstop adjustment
* **Znnn** Z axis endstop adjustment
* **Annn** X bed tilt in percent (RRF 1.16 and later)
* **Bnnn** Y bed tilt in percent (RRF 1.16 and later)

### Examples
<br>
<pre class="cblock">
M666 X-0.1 Y+0.2 Z0
</pre>

### Notes

Positive endstop adjustments move the head closer to the bed when it is near the corresponding tower. Endstop corrections are expressed in mm.

## M667: Select CoreXY or related mode

*This command is deprecated from RRF 2.03, and removed from RRF 3.5 and later.* Use [M669](/User_manual/Reference/Gcodes/M669){target=_blank} instead.

### Parameters

* **Snnn** CoreXY mode
* **Xnnn** X axis scale factor (RRF 2.02 and earlier)
* **Ynnn** Y axis scale factor (RRF 2.02 and earlier)
* **Znnn** Z axis scale factor (RRF 2.02 and earlier)

### Order dependency

* M667 must come earlier in config.g than any M671 command.

### Examples
<br>
<pre class="cblock">
M667 S1
</pre>

### Notes

M667 S0 selects Cartesian mode (unless the printer is configured as a delta using the M665 command). Forward motion of the X motor moves the head in the +X direction. Similarly for the Y motor and Y axis, and the Z motor and Z axis. This is the default state of the firmware on power up.

M667 S1 selects CoreXY mode. Forward movement of the X motor moves the head in the +X and +Y directions. In firmware 1.19 and later, forward movement of the Y motor moves the head in the +X and -Y directions.

M667 S2 selects CoreXZ mode. Forward movement of the X motor moves the head in the +X and +Z directions. In firmware 1.19 and later, forward movement of the Z motor moves the head in the -X and +Z directions.

In versions of RRF prior to 2.03, additional parameters X, Y and Z may be given to specify factors to scale the motor movements by for the corresponding axes. For example, to specify a CoreXZ machine in which the Z axis moves 1/3 of the distance of the X axis for the same motor movement, use M667 S2 Z3. The default scaling factor after power up is 1.0 for all axes.

In RRF 2.03 and later the XYZ parameters are no longer supported. Use the M669 XYZ parameters to alter the movement matrix instead.

To change the motor directions, see the M569 command.

## M669: Set kinematics type and kinematics parameters

Selects the specified kinematics, then uses the other parameters to configure it. If the K parameter is missing then the other parameters are used to update the configuration data for the current kinematics. If no parameters are given then the current kinematics and configuration parameters will be reported.

RepRapFirmware 2.03 and later can support any kinematics for which the movement of each axis is a linear combination of the movement of the motors. The relationship between axis movement and motor movement is defined by a matrix. So K0, K1, K2, K5, K8 and K11 all select the same kinematics, but with different default matrices.

### Parameters

* **Knnn** Kinematics type: 0 = Cartesian, 1 = CoreXY, 2 = CoreXZ, 3 = linear delta, 4 = serial SCARA, 5 = CoreXYU, 6 = Hangprinter, 7 = polar, 8 = CoreXYUV, 9 = five-bar parallel SCARA (in RRF 3.01, experimental), 10 = rotary delta, 11 = Markforge, 12 = reserved for Collinear Tripteron, 13 = reserved for 5-axis robot arm

### Tabs {.tabset}

#### Cartesian, CoreXY/XZ/XYU/XYUV, Markforge

*RRF 2.03 and later only*

##### Parameters 

* **Xnn:nn:nn...** Motor movement coefficients required to move X axis (first row of matrix)
* **Ynn:nn:nn...** Motor movement coefficients required to move Y axis (second row of matrix)
* **Znn:nn:nn...** Motor movement coefficients required to move Z axis (third row of matrix)
* **Unn:nn:nn..., Vnn:nn:nn... etc.** Motor movement coefficients required to move U, V... axes (fourth and subsequent rows of matrix)
* **Snnn** Segments per second (RRF 3.3 and later)
* **Tnnn** Minimum segment length (mm) (RRF 3.3 and later)

##### Order dependency

M669 must come earlier in config.g than any [M671](/User_manual/Reference/Gcodes/M671){target=_blank} command.

##### Notes

All these parameters are optional. The movement coefficient matrices are initialised to suitable value for the kinematics type you selected in the M667 or M669 command, but you can modify them using these parameters. If you send M669 with no parameters, the existing matrix will be reported.

Note: when CoreXZ kinematics is selected, the default matrix assumes there is a 3:1 reduction on the Z axis, as in the original CoreXZ design described at on the [RepRap forums here](https://reprap.org/forum/read.php?2,377858){target=_blank}. If your CoreXZ printer has a different reduction or no reduction then you will need to use the Z parameter to change the Z line of the matrix. For example, if there is no Z reduction then use Z1:0:-1.

In RRF 3, segmentation is not used unless the S and/or T parameter is given. Segmenting moves is useful when faster pause response is wanted.

##### Examples

Response to M669 (no parameters) on simple Cartesian machine:
<br>
<pre class="cblock">
M669
Kinematics is Cartesian, matrix:
1.00 0 0
0 1.00 0
0 0 1.00
</pre>

CoreXY with extra Markforge U axis (see [this forum post](https://forum.duet3d.com/post/136554){target=_blank} for an example):
<br>
<pre class="cblock">
M669 K1 X1:1:0:0 Y1:-1:0:-1 Z0:0:1:0 U0:0:0:1
</pre>

Note U motor values in X, Y, Z and U parameters come after the Z motor values. M669 reports:
<br>
<pre class="cblock">
M669
Kinematics is modified CoreXY, matrix:
1.00 1.00 0 0
1.00 -1.00 0 -1.00
0 0 1.00 0
0 0 0 1.00
</pre>

#### Linear Delta

*RRF 2.03 and later only*

##### Parameters

* **Xnn:nn:nn...** Extruder offset from nozzle in X
* **Ynn:nn:nn...** Extruder offset from nozzle in X
* **Snnn** Segments per second (RRF 3.3 and later)
* **Tnnn** Minimum segment length (mm) (RRF 3.3 and later)

##### Notes

This is used when a 4th axis is added to a linear Delta, to carry the extruder and follow in Z. It specifies the XY offsets of the extruder outputs on additional towers, relative to machine centre in the M669 command. See [Adding additional towers to carry flying extruders](https://docs.duet3d.com/User_manual/Machine_configuration/Configuration_linear_delta#adding-additional-towers-to-carry-flying-extruders){target=_blank}.

In RRF 3, segmentation is not used unless the S and/or T parameter is given. Segmenting moves is useful when faster pause response is wanted.

#### Serial SCARA

##### Parameters

* **Pnnn** Proximal arm length (mm)
* **Dnnn** Distal arm length (mm)
* **Annn:nnn** Proximal arm joint movement minimum and maximum angles, in degrees anticlockwise seen from above relative to the X axis
* **Bnnn:nnn** Proximal-to-distal arm joint movement minimum and maximum angles, in degrees anticlockwise seen from above relative to both arms in line
* **Cnnn:nnn:nnn** Crosstalk factors. The first component is the proximal motor steps to equivalent distal steps factor, the second is the proximal motor steps to equivalent Z motor steps factor, and the third component is the distal motor steps to equivalent Z motor steps factor.
* **Rnnn** (optional, RRF 2.03 and later only) Minimum permitted printing radius from the proximal axis. If not specified, it will be calculated to be slightly larger than the distance between nozzle and proximal axis when the distal axis is homed.
* **Snnn** Segments per second (because smooth XY motion is approximated by means of segmentation)
* **Tnnn** Minimum segment length (mm) (because smooth XY motion is approximated by means of segmentation)
* **Xnnn** X offset of bed origin from proximal joint
* **Ynnn** Y offset of bed origin from proximal joint

##### Examples
<br>
<pre class="cblock">
M669 K4 P300 D250 A-90:90 B-135:135 C0:0:0 S100 X300 Y0
</pre>

##### Notes

The minimum and maximum arm angles are also the arm angles assumed by the firmware when the homing switches are triggered. The P, D, A and B parameters are mandatory. The C, X and Y parameters default to zero, and the segmentation parameters default to firmware-dependent values.

#### Polar

##### Parameters

* **Annn** Maximum turntable acceleration in degrees per second^2
* **Fnnn** Maximum turntable speed in degrees per second
* **Hnnn** Radius of the nozzle from the centre of the turntable when the radius arm homing switch is triggered
* **Raaa:bbb** Minimum (aaa) and maximum (bbb) radius on the turntable reachable by the nozzle.
* **Snnn** Segments per second (because smooth XY motion is approximated by means of segmentation)
* **Tnnn** Minimum segment length (mm) (because smooth XY motion is approximated by means of segmentation)
* **Xnnn** X offset of bed origin from turntable centre (not yet implemented)
* **Ynnn** Y offset of bed origin from proximal joint (not yet implemented)

##### Notes

The **A** and **F** parameters only apply to normal moves not to G1 H2 (individual motor) moves. The intention is that when printing well away from the centre, the normal X and Y limits set by M201 and M203 are sufficient. When printing at a small radius, movement may need to be slowed down to limit the turntable speed and acceleration.

There is currently no facility for offsetting the radius arm sideways from the centre of rotation of the turntable, or for moving the origin.

## M670: Set IO port bit mapping

### Parameters

* **Pnn:nn:nn...** - List of logical port numbers that bits 0, 1, 2... control (supported in RRF2.x and earlier)
* **Cnnn** - Used to specify the pin name(s) to be controlled (supported in RRF3 and later, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank})
* **Tnnn** - Port switching time advance in milliseconds

### Examples
<br>
<pre class="cblock">
M670 T5 C"sx1509b.0+sx1509b.1+sx1509b.2" ; RRF 3.x
M670 T5 P220:221:222 ; RRF 2.x
</pre>

### Notes

* In RRF3, the P parameter is removed. Use the C parameter to specify the pin names to be used.
* RepRapFirmware 1.19 and later provides an optional P parameter on the G1 command to allow I/O ports to be set to specified states for the duration of the move. The argument to the P parameter is a bitmap giving the required state of each port. The M670 command specifies the mapping between the bits of that argument and logical port numbers. Optionally, the T parameter can be used to advance the I/O port switching a short time before the corresponding move begins.

## M671: Define positions of Z pivot points or bed levelling screws

Informs the firmware of the positions of the pivot points, where the bed or gantry connect to the Z axis.

### Parameters

* **Xnn:nn:nn...** List of between 2 and 4 X coordinates of the pivot points 
* **Ynn:nn:nn...** List of between 2 and 4 Y coordinates of the pivot points 
* **Snn** Maximum correction allowed for each pivot point in mm (optional, default 1.0)
* **Pnnn** Pitch of the bed levelling screws (not used when bed levelling using multiple independently-driven Z motors). Defaults to 0.5mm which is correct for M3 bed levelling screws.
* **Fnn** Fudge factor, default 1.0

### Order dependency

M671 must come later in config.g than any command that changes the kinematics, e.g. [M667](/User_manual/Reference/Gcodes/M667){target=_blank} or [M669](/User_manual/Reference/Gcodes/M669){target=_blank}.

### Examples
<br>
<pre class="cblock">
M671 X-15.0:100.0:215.0 Y220.0:-20.0:220.0 ; Z pivot points are at (-15,220), (100,-20) and (215,220)
</pre>

### Notes

* M671 is used to define the **pivot points** of the bed or gantry where it connects to the Z axis. These pivot points are often at each leadscrew, but may also be offset from the leadscrews, if the bed/gantry rests on a carriage extending out from the leadscrew, for example on a kinematic mount.

* When this command is used to define the pivot point positions, the numbers of X and Y coordinates must both be equal to the number of drivers used for the Z axis (see the [M584](/User_manual/Reference/Gcodes/M584){target=_blank} command). This allows the firmware to perform bed levelling by adjusting the Z motors individually after bed probing. See the [G32](/User_manual/Reference/Gcodes/G32){target=_blank} command.

* The X and Y coordinates in M671 are measured from the origin X0,Y0 set by [M208](/User_manual/Reference/Gcodes/M208){target=_blank}. 

* The order of the X and Y coordinates is important; they relate to the order the motor drivers are defined in the [M584](/User_manual/Reference/Gcodes/M584){target=_blank} command. The first defined motor in M584 should be the first defined coordinates for X and Y in M671, and so on. For example, if you have M584 Z3:4:5 and M671 X[a]:[b]:[c] Y[a]:[b]:[c], the positions of X and Y for the motor on Z3 are defined by X[a],Y[a], Z4 by X[b],Y[b], and Z5 by X[c],Y[c].

* The firmware algorithm assumes perfect gimbal joints at the pivot point, so that the bed is completely free to adopt the plane (or the twisted plane if there are 4 points) defined by the pivot points. In real printers this is rarely the case and the corrections are insufficient to level the bed, so multiple G32 commands need to be sent if the bed is a long way off level. The F parameter allows for the corrections calculated by the firmware to be multiplied by a factor so as to achieve faster convergence in this situation.

* For machines without multiple independently-driven Z axes, this command can also be used to define the positions of the bed levelling screws instead. Then bed probing can be used to calculate and display the adjustment required to each screw to level the bed. The thread pitch (P parameter) is used to translate the height adjustment needed to the number of turns of the levelling screws. See [Manual Bed Levelling Assistant](/User_manual/Connecting_hardware/Z_probe_manual_levelling){target=_blank}.

* For printers that print directly onto a desktop and have levelling feet, this command can be used to define the coordinates of the levelling feet, so that bed probing can be used to calculate and display the adjustments needed to the feet. In this case the displayed corrections must be reversed. For example, "0.2 turn down" means the bed needs to be lowered or the printer raised by 0.2 turn lower at that screw position. See [Manual Bed Levelling Assistant](/User_manual/Connecting_hardware/Z_probe_manual_levelling){target=_blank}.

## M672: Program Z probe

This command is for sending configuration data to programmable Z probes such as the Duet3D delta effector, for example to set the sensitivity. The specified command bytes are sent to the probe. The Duet3D probe stores the configuration data in non-volatile memory, so there is no need to send this command every time the probe is used.

### Parameters

* **Snn:nn:nn...** Sequence of 8-bit unsigned values to send to the currently-selected Z probe
* **Knn** (optional) Z probe number, default 0 (supported in RRF 3.01-RC5 and later)

### Examples
<br>
<pre class="cblock">
M672 S105:50:205
</pre>

### Notes

For the Duet3d smart effector:
* The programming pin has to be defined in the [M558](/User_manual/Reference/Gcodes/M558){target=_blank} command
* To program the sensitivity threshold, send command M672 S105:*aaa*:*bbb* replacing *aaa* by the desired threshold and *bbb* by 255 - *aaa*. The green LED will flash 4 times if the command is accepted. When you subsequently power up the effector, the green LED will flash three times instead of twice to indicate that a custom sensitivity is being used. Higher values make the sensor less sensitive. The default threshold for the Duet3D Smart Effector is 50.
* To revert to factory sensitivity, send command M672 S131:131. The green LED will flash 5 times if the command is accepted. When you subsequently power up the effector, the green LED will flash twice to indicate that default settings are being used.

See the [Smart effector and carriage adapters for delta printer](/Duet3D_hardware/Accessories/Smart_Effector){target=_blank} documentation for more details.

## M673: Align plane on rotary axis

*Supported in RepRapFirmware 2.02 and later.*

This code is intended to align a plane that is mounted on a rotary axis. 

### Parameters

* **U,V,W,A,B,C** Rotary axis name on which the plane is mounted
* **Pnnn** Factor to multiply the correction amount with (defaults to 1)

### Examples
<br>
<pre class="cblock">
M673 A
</pre>

### Notes

To make use of this code it is required to take two probe points via [G30 P](/User_manual/Reference/Gcodes/G30){target=_blank} first.

## M674: Set Z to center point

This code is intended to determine the Z center point of a stash that is mounted on a rotary axis. This code is yet to be implemented.

## M675: Find center of cavity

This code is intended to find the center of a cavity that can be measured using the configured axis endstop. It probes towards the minimum end of the specified axis to find one side of the cavity, backs off a little, and then probes towards the maximum end of the same axis to find the other side. 

### Parameters

* **X,Y,Z** Axis to probe on
* **Fnnnn** Probing feedrate
* **Rnnn** Distance to back off after finding the minimum before probing for the maximum, sufficient to ensure that the probe or endstop stops registering contact
* **Pnnn** Use probe with the given number instead of endstop (RRF3.x and later)

### Examples
<br>
<pre class="cblock">
M675 X R2 F1200
</pre>

### Notes

If using a Z probe for this purpose, make sure the endstop type for the corresponding axis is updated before this code is run. 

How it works:
* Once this code starts, RepRapFirmware will move to the lower end looking for an endstop to be triggered. 
* Once it is triggered, the lower position is saved and the axis maximum is probed. 
* As soon as both triggers have been hit, the center point is calculated and the machine moves to the calculated point.

## M701: Load filament

RepRapFirmware 1.19 and later implements a filament management mechanism to load and unload different materials. This code may be used to load a material for the active tool.

### Parameters

* *This command can be used without any additional parameters.*
* **Snn** Filament to load

### Examples
<br>
<pre class="cblock">
M701 S"PLA"
</pre>

### Notes

This code will work only for tools that have exactly one extruder assigned.

When called the firmware does the following:
* Run the macro file "load.g" in the subdirectory of the given material (e.g. /filaments/PLA/load.g)
* Change the filament name of the associated tool, so it can be reported back to Duet Web Control

If this code is called without any parameters, RepRapFirmware will report the name of the loaded filament (if any).

## M702: Unload filament

*Supported in RepRapFirmware 2.02 and newer.*

This code is intended to unload the previously loaded filament from the selected tool.

### Parameters

* *This command can be used without any additional parameters.*

### Examples
<br>
<pre class="cblock">
M702
</pre>

### Notes

RepRapFirmware will do the following when called:
* Run the macro file "unload.g" in the subdirectory of the given material (e.g. /filaments/PLA/unload.g)
* Change the filament name of the current tool, so it can be reported back to Duet Web Control

## M703: Configure filament

After assigning a filament to a tool, this command may be used to run /filaments/\<filament name>/config.g to set parameters like temperatures, extrusion factor, retract distance. If no filament is loaded, the code completes without a warning.

### Parameters

* *This command can be used without any additional parameters.*

### Examples
<br>
<pre class="cblock">
M703
</pre>

### Notes

If the filaments feature is used, it is recommended to put this code into tpost*.g to ensure the right filament parameters are set.

## M750: Enable 3D scanner extension

This code may be used as an OEM extension to enable scanner functionality in the firmware.

### Examples
<br>
<pre class="cblock">
M750
</pre>

### Notes

After a regular start of RepRapFirmware, the 3D scan extension is disabled by default, but if additional scanner components are attached, this code may be used to enable certain OEM functions.

## M751: Register 3D scanner extension over USB

When a 3D scanner board is attached to the USB port, this code is used to turn on communication between the 3D printing and the scanner board.

### Examples
<br>
<pre class="cblock">
M751
</pre>

### Notes

If the USB connection is removed while the 3D scanner configuration is active, the firmware will disable it again and restore the default communication parameters.

## M752: Start 3D scan

Instruct the attached 3D scanner to initiate a new 3D scan and to upload it to the board's SD card (i.e. in the "scans" directory).

### Parameters

* Snnn: Length/degrees of the scan
* Rnnn: Resolution (new in RRF 2.0) [optional, defaults to 100]
* Nnnn: Scanner mode (new in RRF 2.0) [optional, 0=Linear (default), 1=Rotary]
* Pnnn: Filename for the scan

### Examples
<br>
<pre class="cblock">
M752 S300 Pmyscan
</pre>

### Notes

Before the SCAN command is sent to the scanner, the macro file "scan_pre.g" is executed and when the scan has finished, the macro file "scan_post.g" is run. Be aware that both files must exist to avoid error messages.

## M753: Cancel current 3D scanner action

Instruct the attached 3D scanner to cancel the current operation. Cancelling uploads is not supported.

### Examples
<br>
<pre class="cblock">
M753
</pre>

## M754: Calibrate 3D scanner

Calibrates the attached 3D scanner.

### Parameters

* Nnnn: Scanner mode (new in RRF 2.0) [optional, 0=Linear (default), 1=Rotary]

### Examples
<br>
<pre class="cblock">
M754
</pre>

Before the calibration is performed by the external scanner, "calibrate_pre.g" is run and when it is complete, "calibrate_post.g" is executed.

## M755: Set alignment mode for 3D scanner

Sends the ALIGN ON/OFF command the attached 3D scanner. 

### Parameters

* Pnnn Whether to turn on (> 0) or off (<= 0) the alignment feature

### Examples
<br>
<pre class="cblock">
M755 P1
M755 P0
</pre>

### Notes

Some devices turn on a laser when this command is received. If the 'P' parameter is missing, equal to, or less than 0, the alignment feature is turned off. Depending on whether the alignment is turned on or off, either align_on.g or align_off.g is executed before the ALIGN command is sent to the scanner.

## M756: Shutdown 3D scanner

Sends the SHUTDOWN command the attached 3D scanner.

### Examples
<br>
<pre class="cblock">
M756
</pre>

## M851: Set Z-Probe Offset (Marlin Compatibility)

*RepRapFirmware 2.02 and later*

### Parameters

* **Znnn** Trigger Z height

### Examples
<br>
<pre class="cblock">
M851 Z-2.3
</pre>

### Notes

M851 is implemented for backwards compatibility with other firmware. It sets the Z probe trigger in the same way as [G31 Z-nn](/User_manual/Reference/Gcodes/G31){target=_blank} *(note the sign reversal)*. It also flags the Z-probe G31 parameters as to be saved in config-override.g if the [M500](/User_manual/Reference/Gcodes/M500){target=_blank} command is used.

[G31](/User_manual/Reference/Gcodes/G31){target=_blank} should be used in preference to M851.

## M905: Set local date and time

*Supported in RepRapFirmware version 1.16 and later.*

Updates the machine's local date and time or reports them if no parameters are specified.

### Parameters

* **Pnnn** Current date in the format YYYY-MM-DD
* **Snnn** Current time in the format HH:MM:SS
* **Tnnn** (Supported by DSF v3.3 and later) Timezone to set (e.g *Europe/Berlin*)

### Examples
<br>
<pre class="cblock">
M905 P2016-10-26 S00:23:12
M905 P2016-10-26 S00:23:12 T"Europe/Berlin" ; DSF v3.3 and later only
</pre>

### Notes

The time should be specified in 24-hours format as in "13:45" instead of 1:45PM.

Timezone setting is only supported by Duets in SBC mode with DSF v3.3 and later.

## M906: Set motor currents

Sets the peak currents to send to the stepper motors for each axis. The values are in milliamps.

### Parameters

* **Xnnn** X drive peak motor current
* **Ynnn** Y drive peak motor current
* **Znnn** Z drive peak motor current
* **Ennn** E drive(s) peak motor current(s)
* **Innn** Motor current idle factor (0..100)

### Order dependency

This command must be later in config.g than any [M584](/User_manual/Reference/Gcodes/M584){target=_blank} command.

### Examples
<br>
<pre class="cblock">
M906 X300 Y500 Z200 E350:350
</pre>

### Notes

RRF uses peak current. Divide by 1.414 for RMS current as used in Marlin implementations for Trinamic drivers

Current setting on the various Duet boards are as follows:
* Duet 2 WiF/Ethernet is done in steps of 100mA and is rounded down.
* Duet Maestro is in steps of 50mA and rounded down.
* Duet 3 MB6HC and EXP3HC is in steps of 26.2mA.
* Duet 3 Mini5+ is in steps of 74mA (provisionally), rounded down.
* Duet 3 1LC toolboard is in steps of 50mA, rounded down.

The **I** parameter is the percentage of normal that the motor currents should be reduced to when the printer becomes idle but the motors have not been switched off. The default value is 30% and will always be at least 100mA - starting from RRF 2.02 setting it to 0 will disable the steppers after timeout like M18|M84 do and if an axis is related to the motor, throw out the "homing" of it, since it is likely that the position cannot be precisely determined anymore. Note that the idle current is applied globally for all motors and cannot be set per axis.

Every driver that is assigned must have its current set using M906. Not setting a current will default a low current (approx 1/32 of the driver max current), however M906 will report 0 until a current is assigned. Disable the driver explicitly if you do not want any current sent to a driver that is assigned.

As a rule of thumb, the recommendation is to set M906 to use 60-85% of the rated maximum current for the motor. Though you can go above or below as needed, and will have to tune for a balance of motor temperature, motor torque, and noise level. You can also use the EMF calculator ([reprapfirmware.org](https://www.reprapfirmware.org/){target=_blank} and click on EMF calculator) to play with different values to see how it changes behaviour.

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour. Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M906 Z200, not M906 Z200:200

## M911: Configure auto save on loss of power

*Supported in RepRapFirmware 1.19 and later in standalone mode. Supported in SBC mode in DSF v3.4-b2 and later (see [here](https://github.com/Duet3D/DuetSoftwareFramework/issues/55){target=_blank}).*

### Tabs {.tabset}

#### RepRapFirmware 1.20 and later, DSF v3.4 and later

##### Parameters

* Saaa Auto save threshold in volts. The print will be stopped automatically and resume parameters saved if the voltage falls below this value. Set to around 1V to 2V lower than the voltage that appears at the Duet VIN terminals at full load. A negative or zero value disables auto save.
* Raaa Resume threshold in volts. Must be greater than the auto save voltage. Set to a high value to disable auto resume.
* P"command string" GCode commands to run when the print is stopped.

##### Examples
<br>
<pre class="cblock">
M911 S19.8 R22.0 P"M913 X0 Y0 G91 M83 G1 Z3 E-5 F1000"
</pre>

##### Notes

When the supply voltage falls below the auto save threshold while a print from SD card is in progress, all heaters will be turned off, printing will be stopped immediately (probably in the middle of a move), the position saved, and the specified command string executed. You should typically do the following in the command string:
* If possible, use M913 to reduce the motor current in order to conserve power. For example, on most printers except deltas you can probably set the X and Y motor currents to zero.
* Retract a little filament and raise the head a little. Ideally the retraction should happen first, but depending on the power reserve when low voltage is detected, it may be best to do both simultaneously.

For Duet + SBC, a solid external 5V supply is recommended for the Duet + SBC for this feature to work. When power to the Duet + SBC is cut, the SBC may turn off before the Duet can inform the SBC about the content of resurrect.g, or the SBC may lose power while it's trying to write the content of resurrect.g to the microSD card. An external 5V buck regulator may be sufficient to keep a Duet 3 Mini 5+ and SBC on long enough. For Duet 3 MB6HC, the on-board 5V regulator might not endure long enough for resurrect.g to be written to persistent storage if the Duet powers an SBC as well. Hence we recommend using an external 5V PSU if this feature is configured.

M911 with no parameters displays the current enable/disable state, and the threshold voltages if enabled.

See this page for more details: [Setting up to resume a print after a power failure or planned power down](/User_manual/Tuning/Resume){target=_blank}

#### RepRapFirmware 1.19

##### Parameters

* Saaa:bbb:ccc Shutdown threshold (aaa), pause threshold (bbb) and resume threshold (ccc) all in volts, with aaa < bbb < ccc

##### Examples
<br>
<pre class="cblock">
M911 S12.0:19.5:22.0
</pre>

##### Notes

Enables auto-pause if the power voltage drops below the pause threshold. The firmware records the current state of the print so that it can be resumed when power is restored and executes the pause procedure to attempt to park the print head using the residual energy in the power supply capacitors. If the supply voltage continues to drop below the shutdown threshold, the firmware disables all heaters and motors and goes into the shutdown state until either the voltage exceeds the resume threshold or the board is reset. In either case, it may be possible to resume the paused print. If the supply voltage does not fall below the shutdown threshold but recovers and exceeds the resume threshold, then the print is resumed automatically.

If any of the three values for the S parameter are zero or negative, or the three values are not in ascending order, then auto-save is disabled.

M911 with no parameters displays the current enable/disable state, and the threshold voltages if enabled.

## M912: Set electronics temperature monitor adjustment

### Parameters

* **Pnnn** Temperature monitor channel, default 0
* **Snnn** Value to be added to the temperature reading in degC

### Examples
<br>
<pre class="cblock">
M912 P0 S10.5
</pre>

### Notes

Many microcontrollers used to control 3D printers have built-in temperature monitors, but they normally need to be calibrated for temperature reading offset. The S parameter specifies the value that should be added to the raw temperature reading to provide a more accurate result.

(Currently only for the CPU on-chip temperature sensor P0. Other P parameter could in the future be added for boards with multiple on-chip sensors)

## M913: Set motor percentage of normal current

This allows motor currents to be set to a specified percentage of their normal values as set by [M906](/User_manual/Reference/Gcodes/M906){target=_blank}. It can be used (for example) to reduce motor current during course homing, to make homing quieter or to reduce the risk of damage to endstops, and to reduce current while loading filament to guard against the possibility of feeding too much filament. Use M913 again with the appropriate parameters set to 100 to restore the normal currents.

### Parameters

* **X, Y, Z, E** Percentage of normal current to use for the specified axis or extruder motor(s)

### Examples
<br>
<pre class="cblock">
M913 X50 Y50 Z50 ; set X Y Z motors to 50% of their normal current
M913 E30:30 ; set extruders 0 and 1 to 30% of their normal current
</pre>

### Notes

When M913 is executed, it does not wait for all motion to stop first (unlike M906). This is so that it can be used in the M911 power fail script. When using M913 elsewhere, you will typically want to use M400 immediately before M913.

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour. Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M913 Z50, not M913 Z50:50

## M914: Set/Get Expansion Voltage Level Translator

*This command is supported in the Alligator build of RepRapFirmware only.*

### Parameters

* **Sn** Expansion voltage signal level, n must be 3 or 5

### Examples
<br>
<pre class="cblock">
M914 S5 ; set expansion signal level to 5V
M914 ; report expansion signal voltage level
</pre>

## M915: Configure motor stall detection

This sets the stall detection parameters and optionally the low-load current reduction parameters for TMC2660, TMC2130 or similar driver chips. Use either the P parameter to specify which driver number(s) you want to configure, or the axis names of the axes that those motors drive (the parameters will then be applied to all the drivers associated with any of those axes).

### Tabs {.tabset}

#### RRF 3.4 and later

##### Parameters

* **Pnnn:nnn:...** Drive number(s) to configure
* **X,Y,Z,U,V,W,A,B,C** Axes to configure (alternative to using the P parameter)
* **Snnn** Stall detection threshold (see notes below)
* **Fn** Stall detection filter mode, 1 = filtered (one reading per 4 full steps), 0 = unfiltered (default, 1 reading per full step)
* **Hnnn** (optional) Minimum motor full steps per second for stall detection to be considered reliable, default 200
* **Tnnn** (optional) Coolstep control register, 16-bit unsigned integer
* **Rn** Action to take on detecting a stall from any of these drivers: 0 = no action (default), 1 = just log it, 2 or 3 = create an event (see notes).

##### Notes

* In RRF 3.4.0 thru 3.4.5, motor stalls don't generate events when not printing from SD card. RRF 3.4.6-rc.1 and 3.5.0-beta4 and later do generate events when not printing from SD card.
* In RRF v3.4 and later, R2 and R3 both cause an event to be created when the driver stalls. 
* To handle the event, RRF calls driver-stall.g passing the stalled local driver number in param.D and the CAN address of the board concerned in param.B. 
* If file driver-stall.g is not found then the default action is to report it to the console and carry on.
* File rehome.g is no longer used. 
* See the [events](/User_manual/RepRapFirmware/Events){target=_blank} page for more detail. 

#### RRF 3.3 and earlier

##### Parameters

* **Pnnn:nnn:...** Drive number(s) to configure
* **X,Y,Z,U,V,W,A,B,C** Axes to configure (alternative to using the P parameter)
* **Snnn** Stall detection threshold (see notes below)
* **Fn** Stall detection filter mode, 1 = filtered (one reading per 4 full steps), 0 = unfiltered (default, 1 reading per full step)
* **Hnnn** (optional) Minimum motor full steps per second for stall detection to be considered reliable, default 200
* **Tnnn** (optional) Coolstep control register, 16-bit unsigned integer
* **Rn** Action to take on detecting a stall from any of these drivers: 0 = no action (default), 1 = just log it, 2 = pause print, 3 = pause print, execute /sys/rehome.g, and resume print.

### Order dependency

If this command refers to any axes other than X, Y and Z then it must appear later in config.g than the M584 command that creates those additional axes.

### Examples
<br>
<pre class="cblock">
M915 P0:2:3 S10 F1 R0
M915 X Y S5 R2
</pre>

### Notes

* **S parameter** For most drivers, values range from -64 to +63. For TMC2209 drivers (Duet 3 Mini 5+, Duet 3 Toolboard 1LC) values range from -128 to +127. Lower values make stall detection more sensitive. Values below -10 are not recommended. S3 is a good starting point for many motors.
* If any of the S, F, T and R parameters are absent, the previous values for those parameters associated with the specified drivers will continue to be used. 
* If all the parameters are absent, the existing settings for the specified drive(s) will be reported.
* See the Trinamic TMC2660 and TMC2130 datasheets for more information about the operation and limitations of motor stall detection.
* See here for more detailed information on [Stall Detection and Sensorless Homing](/User_manual/Connecting_hardware/Sensors_stall_detection){target=_blank}.

## M916: Resume print after power failure

*Supported in firmware 1.20 beta 2 and later.*

If the last print was not completed and resume information has been saved (either because the print was paused or because of a power failure), then the heater temperatures, tool selection, head position, mix ratio, mesh bed compensation height map etc. are restored from the saved values and printing is resumed.

### Parameters

* none

### Examples
<br>
<pre class="cblock">
M916
</pre>

### Notes

RepRapFirmware also requires macro file **/sys/resurrect-prologue.g** to be present on the SD card before you can use M916. This file is executed after the heater temperatures have been set, but before waiting for them to reach the assigned temperatures. You should put commands in this file to home the printer as best as you can without disturbing the print on the bed. To wait for the heaters to reach operating temperature first, use command M116 at the start of the file.

Version 1.19 of RepRapFirmware does not support M916 but you can achieve the same effect using command **M98 P/sys/resurrect.g**.

## M917: Set motor standstill current reduction

*Supported in firmware 3.01 and later for Duet 3.*

Motor drivers on Duet 3 allow higher motor currents to be used while the motor is moving. This command sets the percentage of the current set by [M906](/User_manual/Reference/Gcodes/M906){target=_blank} that is to be used when the motor is stationary but not idle, or moving very slowly.

### Parameters

* **X,Y,Z,E** Percentage of normal current to use when the motor is standing still or moving slowly, default 71

### Order dependency

If this command refers to any axes other than X, Y and Z then it must appear later in config.g than the M584 command that creates those additional axes.

### Examples
<br>
<pre class="cblock">
M917 X70 Y70 Z80 E70:70
</pre>

### Notes

Standstill current reduction is not the same as idle current reduction. The standstill current must be high enough to produce accurate motion at low speeds. The idle current needs only to be high enough to hold the motor position well enough so that when the current is restored to normal, the position is the same as it was before the current was reduced to idle.

When M906 is used to set the motor current to 71% or more of the maximum permitted current, RRF will limit the maximum standstill current percentage so that the standstill current is no more than 71% of the maximum permitted motor current. This is to ensure that a single phase of the driver does not pass more than 71% of the maximum current continuously, which would risk overheating the output mosfets of that phase.

Note this is not supported on Duet 2 with TMC2660 or on external drivers.

## M918: Configure direct-connect display

This command is used to tell RepRapFirmware about a directly-connected LCD such as a 12864 or compatible display.

### Parameters

* **Pn** Directly-connected display type: P1 = 128x64 pixel mono graphics display using ST7920 display driver, P2 = 128x64 display using ST7567 display driver (RRF 3.02 and later), P3 = 480x320 TFT display with resistive touch panel using ILI9488 driver (RRF 3.5 and later only).
* **En** (optional, defaults to 1) The number of pulses generated by the rotary encoder per detent. Typical values are 2 and 4. Negative values (e.g. -2 and -4) reverse the encoder direction.
* **F** (optional, supported in RRF 2.03 and later) SPI clock frequency in Hz, default 2000000 (i.e. 2MHz). The maximum usable value with short cables is around 4MHz for 12864 displays and 15MHz for TFT displays.
* **C** (optional, supported in RRF 3.2 and later) Display contrast, in range 0 to 100. Only used with ST7567-based displays. ST7920-based displays usually have a contrast potentiometer instead.
* **R** (optional, supported in RRF 3.2 and later) Display resistor ratio, in range 1 to 7. Only used with ST7567-based displays. The default value of 6 is suitable for the Fysetc Mini 12864 display. Some other displays need 3.

### Examples
<br>
<pre class="cblock">
M918 P1 E2
</pre>

### Notes

See [Connecting 12864 or other display](/User_manual/Connecting_hardware/Display_12864) for supported Duet boards and usage.

## M929: Start/stop event logging to SD card

When event logging is enabled, important events such as power up, start/finish printing and (if possible) power down will be logged to the SD card. Each log entry is a single line of text, starting with the date and time if available, or the elapsed time since power up if not. If the log file already exists, new log entries will be appended to the existing file.

### Parameters

* **P"filename"** The name of the file to log to. Only used if the S1 parameter is present. A default filename will be used if this parameter is missing.
* **Sn** S1 = start logging, S0 = stop logging (RRF <= 3.2.0)
* **Sn** S0 = stop logging, S1 = log level WARN, S2 = log level INFO, S3 = log level DEBUG (RRF >= 3.2.0)

### Examples
<br>
<pre class="cblock">
M929 P"eventlog.txt" S1 ; start logging warnings to file eventlog.txt
M929 S0 ; stop logging
</pre>

### Notes

From RepRapFirmware 3.2.0 and later, more granular logging is available. There are three log levels, and no logging. When logging is enabled, each line in the log will have the log level of that message added after the timestamp.

* WARN: All log messages from previous versions will fall into this log level
* INFO: G10, M117, M291 and M292 invocations will fall into this log level
* DEBUG: all output not listed above will be logged within this log level

**Caution**: do not rename or delete the current log file while logging is enabled!

Also see [M118](/User_manual/Reference/Gcodes/M118){target=_blank}.

## M950: Create heater, fan, spindle or GPIO/servo pin

*Supported in RepRapFirmware 3.*

M950 is used to create heaters, fans, GPIO ports, spindles (3.3 and later) and LED strips (3.5 and later), and to assign pins to them. Each M950 command assigns a pin or pins to a single device. Every M950 command must have **exactly one** of the H, F, J, P, S, D (for Duet 3 MB6HC only) or E (in RRF 3.5 and later) parameters.

If a M950 command has C and/or Q parameters, then the pin allocation and/or frequency will be configured accordingly. Otherwise, the current configuration will be reported.

### Tabs {.tabset}

#### RRF 3.5 and later

##### Parameters

* **Hnn** Heater number
* **Fnn** Fan number
* **Jnn** Input pin number
* **Pnn** or **Snn** Output/servo pin number. Each P and/or S number needs to be unique, eg P1, P2, S3 P4, S5 etc. Servo pins are GpOut pins with a different default PWM frequency.
* **Rnn** Spindle number
* **Dn** (Duet 3 MB6HC only) SD slot number. The only value supported is 1.
* **En** LED strip number
* **C"name"** Pin name(s) and optional inversion status, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank}. Pin name "nil" frees up the pin. A leading '!' character inverts the input or output. A leading '^' character enables the pullup resistor^1^. The '^' and '!' characters may be placed in either order.
* **Qnn** (optional) PWM frequency in Hz. Valid range: 0-65535, default: 500 for GpOut pins, 250 for fans and heaters. For LED strips (supported in RRF 3.5.0-beta.4 and later only) this is the LED clock frequency.
* **Tn** When creating a heater: temperature sensor number, required (see [M308](/User_manual/Reference/Gcodes/M308)). When creating a LED strip (supported in RRF 3.5.0-beta.4 and later only): LED type (optional): 0 = DotStar, 1 = RGB Neopixel (default), 2 = RGBW Neopixel. DotStar LEDs can normally be assigned only to an output intended for them.
* **Lbbb** or **Laaa:bbb** (optional, for spindles only) RPM values that are achieved at zero PWM (optional) and at maximum PWM.
* **Kaaa(:bbb[:ccc])** (optional, for spindles only, RRF 3.5 and later) PWM values (0..1) for spindle control (max [aaa] - or - min, max [aaa:bbb] - or - min, max, idle [aaa:bbb:ccc])
* **Unnn** (optional, RRF 3.5.0-beta.4 and later, for LED strips only) The maximum number of LEDs in the strip. Default 60, larger values use more memory.

^1^ Check the individual hardware pages, some IO pins have permanent pullups.

#### RRF 3.4

##### Parameters

* **Hnn** Heater number
* **Fnn** Fan number
* **Jnn** Input pin number
* **Pnn** or **Snn** Output/servo pin number. Each P and/or S number needs to be unique, eg P1, P2, S3 P4, S5 etc. Servo pins are GpOut pins with a different default PWM frequency.
* **Rnn** Spindle number
* **Dn** (Duet 3 MB6HC only) SD slot number. The only value supported is 1.
* **C"name"** Pin name(s) and optional inversion status, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank}. Pin name "nil" frees up the pin. A leading '!' character inverts the input or output. A leading '^' character enables the pullup resistor^1^. The '^' and '!' characters may be placed in either order.
* **Qnn** (optional) PWM frequency in Hz. Valid range: 0-65535, default: 500 for GpOut pins, 250 for fans and heaters.
* **Tn** When creating a heater: temperature sensor number, required (see [M308](/User_manual/Reference/Gcodes/M308)).
* **Lbbb** or **Laaa:bbb** (optional, for spindles only) RPM values that are achieved at zero PWM (optional) and at maximum PWM.

^1^ Check the individual hardware pages, some IO pins have permanent pullups.

#### RRF 3.3

##### Parameters

* **Hnn** Heater number
* **Fnn** Fan number
* **Jnn** Input pin number
* **Pnn** or **Snn** Output/servo pin number. Each P and/or S number needs to be unique, eg P1, P2, S3 P4, S5 etc. Servo pins are GpOut pins with a different default PWM frequency.
* **Rnn** Spindle number
* **C"name"** Pin name(s) and optional inversion status, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank}. Pin name "nil" frees up the pin. A leading '!' character inverts the input or output. A leading '^' character enables the pullup resistor^1^. The '^' and '!' characters may be placed in either order.
* **Qnn** (optional) PWM frequency in Hz. Valid range: 0-65535, default: 500 for GpOut pins, 250 for fans and heaters.
* **Tn** When creating a heater: temperature sensor number, required (see [M308](/User_manual/Reference/Gcodes/M308)).
* **Lbbb** or **Laaa:bbb** (optional, for spindles only) RPM values that are achieved at zero PWM (optional) and at maximum PWM.

^1^ Check the individual hardware pages, some IO pins have permanent pullups.

#### RRF 3.0

##### Parameters

* **Hnn** Heater number
* **Fnn** Fan number
* **Jnn** Input pin number (RRF 3.01 and later only)
* **Pnn** or **Snn** Output/servo pin number. Each P and/or S number needs to be unique, eg P1, P2, S3 P4, S5 etc. Servo pins are GpOut pins with a different default PWM frequency.
* **C"name"** Pin name(s) and optional inversion status, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank}. Pin name "nil" frees up the pin. A leading '!' character inverts the input or output. A leading '^' character enables the pullup resistor^1^. The '^' and '!' characters may be placed in either order.
* **Qnn** (optional) PWM frequency in Hz. Valid range: 0-65535, default: 500 for GpOut pins, 250 for fans and heaters.
* **Tn** When creating a heater: temperature sensor number, required (see [M308](/User_manual/Reference/Gcodes/M308)).

^1^ Check the individual hardware pages, some IO pins have permanent pullups.

### Order dependency

* When M950 is used to create a heater, the M950 command must come later in config.g than the M308 command that creates the sensor referred to in the T parameter
* M950 must come before any commands that refer to the device being created. For example, when M950 is used to create a heater, it must be earlier than the M307 command used to set the heater parameters, and earlier than any M563 commands that create tools that use that heater. When M950 is used to create a fan, it must come earlier than any M106 commands relating to that fan. When M950 is used to create an LED strip, it must come earlier than any M150 commands that use that strip.

### Configuration examples and notes

### Tabs {.tabset}

#### Heaters

<br>
<pre class="cblock">
M950 H1 C"out1" Q100 T1        ; create heater 1
M950 H2 C"nil"                 ; disable heater 2 and free up the associated pin
M950 H1 C"3.out0+out2" Q100 T1 ; create heater 1 using ports OUT0 and OUT2 on CAN board 3 (RRF 3.4 or later)
</pre>

* **Hnn** Heater number
* **C"name"** Pin name(s) and optional inversion status. Pin name "nil" frees up the pin.
* **Qnn** (optional) PWM frequency in Hz. Valid range: 0-65535, default: 250 for heaters.
* **Tn** When creating a heater, temperature sensor number, required (see [M308](/User_manual/Reference/Gcodes/M308)).

(RRF 3.4 and later) When using M950 to create a heater, multiple port names can be provided, separated by the '+' sign. The maximum number of ports that may be used depends on the board. **Any CAN address at the start of the port name string applies to all the port names.**

#### Fans

<br>
<pre class="cblock">
M950 F3 C"heater2" Q100           ; Fan 3 is connected to heater 2 pin, PWM at 100Hz
M950 F2 C"!fan2+^exp.pb6"         ; Fan 2 uses the Fan2 output, but we are using a PWM fan so the output needs to be inverted, also we are using PB6 as a tacho input with pullup resistor enabled
M950 F5 C"!out4+out4.tach" Q450   ; Create Fan 5 on the mainboard on OUT4 with a tacho input
M950 F0 C"!1.out3+out3.tach" Q450 ; Create Fan 0 on expansion board 1, output OUT3 with a tacho input
</pre>

* **Fnn** Fan number
* **C"name"** Pin name(s) and optional inversion status. Pin name "nil" frees up the pin. A leading '!' character inverts the input or output. A leading '^' character enables the pullup resistor^1^. The '^' and '!' characters may be placed in either order.
* **Qnn** (optional) PWM frequency in Hz. Valid range: 0-65535, default: 250 for fans.

When using M950 to create a fan, the port name string may be either a single port, or two ports separated by the '+' sign. The second port is used to read the fan tacho. **Any CAN address at the start of the port name string applies to both port names.**

#### Inputs

<br>
<pre class="cblock">
M950 J1 C"!^e1stop"            ; Input 1 uses e1Stop pin, inverted, pullup enabled
</pre>

* **Jnn** Input pin number
* **C"name"** Pin name(s) and optional inversion status. Pin name "nil" frees up the pin. A leading '!' character inverts the input or output. A leading '^' character enables the pullup resistor^1^. The '^' and '!' characters may be placed in either order.

#### Outputs and servos

<br>
<pre class="cblock">
M950 P0 C"exp.heater3"         ; create output/servo port 0 attached to heater 3 pin on expansion connector
</pre>

* **Pnn** or **Snn** Output/servo pin number. Each P and/or S number needs to be unique, eg P1, P2, S3 P4, S5 etc. Servo pins are GpOut pins with a different default PWM frequency.
* **C"name"** Pin name(s) and optional inversion status, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names){target=_blank}. Pin name "nil" frees up the pin. A leading '!' character inverts the input or output. A leading '^' character enables the pullup resistor^1^. The '^' and '!' characters may be placed in either order.
* **Qnn** (optional) PWM frequency in Hz. Valid range: 0-65535, default: 500 for GpOut pins.

#### Spindles

(RRF 3.3 and later) When using M950 to create a spindle use the following format:

<br>
<pre class="cblock">
M950 R0 C"pwm_pin+forward_pin+reverse_pin" Qfff Laa:bb
</pre>

* C can have 1, 2 or 3 pins. The first pin defined is a pwm capable pin to set the spindle speed. If a second pin is defined it is used as spindle on/off, if three pins are defined then the second pin is spindle forward and the third is spindle reverse.
* "fff" is the PWM frequency as usual
* "Laa:bb" sets the RPM range as "aa" to "bb". "Lbb" just sets the max RPM to "bb". Default RPM values are 60 min 10000 max

Example:

<br>
<pre class="cblock">
M950 R0 C"!exp.heater3" L12000 ; Spindle 0 uses exp.heater3 as RPM pin and has a max RPM of 12000 (RRF 3.3 and later)
</pre>

* **Rnn** Spindle number
* **C"name"** Pin name(s) and optional inversion status. Pin name "nil" frees up the pin. A leading '!' character inverts the input or output. A leading '^' character enables the pullup resistor^1^. The '^' and '!' characters may be placed in either order.
* **Qnn** (optional) PWM frequency in Hz. Valid range: 0-65535.
* **Lbbb** or **Laaa:bbb** (optional) RPM values that are achieved at zero PWM (optional) and at maximum PWM.
* **Kaaa(:bbb[:ccc])** (optional, RRF 3.5 and later) PWM values (0..1) for spindle control (max [aaa] - or - min, max [aaa:bbb] - or - min, max, idle [aaa:bbb:ccc])

#### SD card slot

<br>
<pre class="cblock">
M950 D1 C"spi.cs0+spi.cs2"     ; on Duet 3 MB6HC support external SD card using pins spi.cs0 and spi.cs2 for the CS and Card Detect pins respectively (RRF 3.4 and later)
</pre>

Duet 3 MB6HC only, configures the SD slot number. The only value supported is 1. (RRF 3.4 and later)

#### LED strips

Supported in RRF 3.5.0-beta.4 and later only

<br>
<pre class="cblock">
M950 E0 C"led" T2              ; create a RGBW Neopixel LED strip on the LED port (RRF 3.5 and later)
</pre>

* **En** LED strip number
* **C"name"** Pin name
* **Qnn** (optional) LED clock frequency.
* **Tn** (optional) LED type: 0 = DotStar, 1 = RGB Neopixel (default), 2 = RGBW Neopixel. DotStar LEDs can normally be assigned only to an output intended for them.
* **Unnn** (optional) The maximum number of LEDs in the strip. Default 60, larger values use more memory.

* The default maximum number of LEDs in a strip is 60. It can be increased using the M950 U parameter, subject to 
  (a) available RAM and 
  (b) on the 6HC and 6XD there is an additional limit because the DMA buffer has to be in non-cached memory. For 6HC and 6XD the max LEDs for a strip connected to the dedicated LED port is currently 240 Neopixel RGBW or 320 RGB. It might reduce in future.
* When configuring a LED stirp on a tool board or 1XD, using a lower U parameter (ie set U to the number of LEDs) is advised to save RAM, because there is very little free RAM on those boards.

## M951: Set height following mode parameters

*Supported in RepRapFirmware 3.x*

Height following mode allows the Z position of the tool to be controlled by a PID controller using feedback from a sensor. See also M594.

### Parameters

* **Hnn** Sensor number
* **Pnn.n** Proportional factor, in mm per sensor unit
* **Inn.n** Integral factor, in mm per sensor unit per second
* **Dnn.n** Derivative factor, in mm per rate of change of sensor units (change in sensor unit per second)
* **Fnn.n** (optional) Sample and correction frequency (Hz), default 5Hz
* **Znn.n:nn.n** Minimum and maximum permitted Z values

### Notes

If commanding the motors to increase Z causes the sensor value to increase, then all of P, I and D must be positive. If commanding the motors to increase Z causes the sensor value to decrease, then all of P, I and D must be negative.

## M952: Set CAN-FD expansion board address and/or normal data rate

Some CAN-connected expansion boards are too small to carry address selection switches. Such boards default to a standard address, which can be changed using this command.

### Parameters

* **Bn** Existing CAN address of expansion board to be changed, 1 to 125.
* **An** New CAN address of that expansion board, 1 to 125.
* **Sn.n** Requested bit rate in Kbits/second (1K = 1000) (optional, default 1000)
* **T0.n** Fraction of the bit time between the bit start and the sample point (optional)
* **J0.n** Maximum jump time as a fraction of the bit time (optional)

### Examples
<br>
<pre class="cblock">
M952 B121 A20  ; change the CAN address of expansion board 121 to 20
M952 B20 S500  ; change the CAN bit rate or expansion board 20 to 500kbps
</pre>

### Notes

The change of CAN address will not take place until the expansion board is restarted.

This command can also be used to change the normal data rate, for example if the printer has CAN bus cables that are too long to support the standard data rate (1Mbits/sec in RepRapFirmware). All boards in the system on the same CAN bus must use the same CAN data rate. The procedure for changing the data rate is:
* Use M952 to change the data rate on all the expansion boards, one at a time. After changing the data rate on each expansion board, you will no longer be able to communicate with it, and you may need to power it down or disconnect it from the CAN bus to prevent it interfering with subsequent CAN communications.
* Change the data rate of the main board last. Then the main board should be able to communicate with all the expansion boards again.

## M953: Set CAN-FD bus fast data rate

This command allows the bandwidth of the CAN bus to be optimised, by increasing the data rate during transmission of CAN-FD data packets by means of the BRS (bit rate switch) feature. The maximum speed supported by CAN-FD is 8Mbits/sec but the practical limit depends on the cable length, cable quality, number of devices on the bus and CAN interface hardware used. The rate specified will be rounded down to the nearest achievable rate.

### Parameters

* **Sn.n** Requested bit rate in Mbits/second. Ignored if it is lower than the bit rate for the negotiation phase.
* **T0.n** Fraction of the bit time between the bit start and the sample point (optional)
* **J0.n** Maximum jump time as a fraction of the bit time (optional)
* **Caa:bb** Transceiver delay compensation offset and minimum, in nanoseconds (optional)

### Examples
<br>
<pre class="cblock">
M953 S4.0 T0.6 J0.2
</pre>

### Notes

The optional **C** parameter allows fine-tuning of the transmitter delay compensation. The first parameter is the offset added to the measured transmitter delay. The optional second value, which must be greater than the first, is the minimum delay compensation applied. 

Glitches seen by the receiver while the transceiver delay is being measured will be ignored if they would result in a transceiver delay compensation lower than this value. 

When CAN is implemented on Microchip SAME5x and SAMC21 processors, these values are converted from nanoseconds into time quanta and stored in the TDCO and TDCF fields of the transceiver delay compensation register.

## M954: Configure as CAN expansion board

*Supported in RRF 3.4 and later on Duet 3 boards*

This command is used to reconfigure the board it is executed on as a CAN-connected expansion board. It would typically be the only command in the config.g file. When it is executed, the board changes its CAN address to the one specified in the A parameter, stops sending CAN time sync messages, and responds to requests received via CAN just like a regular expansion board. 

### Parameters

* **Ann** CAN address to use (required)

### Notes

After this command is executed, for diagnostic purposes a few GCode commands can still be sent to the USB port for local execution, for example [M111](/User_manual/Reference/Gcodes/M111){target=_blank} and [M122](/User_manual/Reference/Gcodes/M122){target=_blank}.

## M955: Configure Accelerometer

*Supported in RRF 3.4 and later (limited support in 3.3)*

This command configures an accelerometer.

### Parameters (provisional)

* **Pnn** or **Pbb.nn** Accelerometer to use (required)
* **Inn** Accelerometer orientation
* **Snnn** Sample rate (Hz)
* **Rnn** Resolution (bits), typically 8, 10 or 12
* **C"aaa+bbb"** Pins to use for CS and INT (in that order) when connecting the accelerometer via SPI
* **Q**nnn (RRF 3.3RC1 and later) SPI clock frequency (optional, default 2000000 i.e. 2MHz)

### Examples
<br>
<pre class="cblock">
M955 P0 C"spi.cs1+spi.cs0" I10 ; configure accelerometer on mainboard using SPI pins and specify orientation 
M955 P121.0 I10                ; configure accelerometer on toolboard with CAN address 121 and specify orientation 
</pre>

### Notes

The **P** parameter selects which accelerometer to use and is mandatory. To use an accelerometer on a CAN-connected expansion board, use the form **P***board-address*.*device-number* for example **P22.0**. Use **P0** for an accelerometer connected locally (i.e. on the mainboard) via SPI.

If none of the other parameters are provided, the current configuration of the specified accelerometer is reported. Otherwise the configuration of that accelerometer is adjusted according to the I, S, and R parameters. These configuration settings persist until they are changed.

The **C** parameter is needed only when the accelerometer is connected to a mainboard, and defines the pins used for the CS and INT signals. It is not needed when using a toolboard with integrated accelerometer.

The **I** (orientation) parameter tells the firmware which of the 24 possible orientations the accelerometer chip is in relative to the printer axes. It is expressed as a 2-digit number. The first digit specifies which machine direction the Z axis of the accelerometer chip (usually the top face of the chip) faces, as follows: 0 = +X, 1 = +Y, 2 = +Z, 4 = -X, 5 = -Y, 6 = -Z. The second digit expresses which direction the X axis of the accelerometer chip faces, using the same code. If the accelerometer chip axes line up with the machine axis, the orientation is 20. This is the default orientation if no orientation has been specified.

The **S** and **R** parameters control how the accelerometer is programmed. The R parameter is ignored unless the S parameter is also provided. If S is provided but R is missing, a default resolution is used. The sensor resolution will be adjusted to be no greater than the value of the R parameter (or the minimum supported resolution if greater), then the sensor sampling rate will be adjusted to a value supported at that resolution that is close to the S parameter. The actual rate and resolution selected can be found by using M955 with just the P parameter.

For more information on connecting accelerometers, see the [Connecting an accelerometer](/User_manual/Connecting_hardware/Sensors_Accelerometer) wiki page.

## M956: Collect accelerometer data and write to file

*Supported in RRF 3.4 and later (limited support in 3.3)*

This command causes the specified number of accelerometer samples to be collected and saved to a .csv file.

### Parameters (provisional)

* **Pnn** or **Pbb.nn** Accelerometer to use (required)
* **Snnn** Number of samples to collect (required)
* **X** and/or **Y** and/or **Z** (optional) Machine axes to collect data for. If no axes are specified, or if the accelerometer type is LIS2DW (supported in RRF 3.5.0 and later) then data is collected for all three axes.
* **An** (required) 0 = activate immediately, 1 = activate just before the start of the next move, 2 = activate just before the start of the deceleration segment of the next move
* **F"filename.csv"** Name of the file to save the data in (optional, supported by RRF 3.4 and later). The default folder is 0:/sys/accelerometers. If not specified then the filename will be composed from the current date/time.

### Notes

The **P** parameter selects which accelerometer to use and is mandatory. To use an accelerometer on a CAN-connected expansion board, use the form **P***board-address*.*device-number* for example **P22.0**.

## M957: Raise event

*Supported in RepRapFirmware 3.4 and later for raising events*

This command is used to raise an event or trigger internally as if the event had actually occurred, and execute any related handler macro for that event or trigger. Its main use is to test event handler and trigger macros.

### Parameters

* **E"type"** Event type name
* **Dnn** Device number to which the event relates
* **Bnn** (optional) CAN address of the board that the event should appear to originate from
* **Pnn** (optional) Additional data about the event (unsigned integer)
* **S"text"** (optional) Short test string to be appended to the event message

### Examples
<br>
<pre class="cblock">
M957 E"heater_fault" D1 B2
</pre>

Raise a heater fault from expansion board at CAN address 2 on heater 1

### Notes

The event type names are firmware-dependent. In RepRapFirmware they are: heater-fault, driver-error, filament-error, driver-warning. However, in RRF 3.4.0 it is necessary to use underscore "\_" in place of dash "-" when using these event names in M957. Future versions of RRF will allow the dash character to be used instead but will still allow underscore for backwards compatibility.

The meaning of the device number depends on the event type. For a driver error it is the driver number. For a heater fault it is the heater number. For a filament error it is the extruder number.

The meaning of the optional additional parameter also depends on the event type. For example, for a driver error it is the driver status.

## M997: Perform in-application firmware update

This command triggers a firmware update if the necessary files are present on the SD card.

### Parameters

* **Snnn** Firmware module number(s), default 0
* **Bnnn** CAN address of the board to be updated (RRF3, Duet 3 only)
* **P"filename"** Filename of firmware binary to use (RRF 3.3 and later)

### Examples
<br>
<pre class="cblock">
M997 S0:1 - update firmware modules 0 and 1
</pre>

### Notes

In RepRapFirmware on the Duet series, module numbers are as follows:

* 0 - main firmware, specific for Duet board. Needs appropriate IAP (In-App Programmer, specific to Duet board) binary to be able to flash update.
* 1 - web server firmware, filename DuetWiFiServer.bin (WiFi-equipped Duets only)
* 2 - web server file system, filename DuetWebControl.bin (needed only when using RepRapFirmware 1.18 series and earlier for Duet 2 WiFi)
* 3 - put the WiFi module into bootloader mode, so that firmware can be uploaded directly via its serial port. Also used to update bootloader on CAN-connected Duet 3 expansion boards.
* 4 - PanelDue firmware (RRF 3.2 and later; see [PanelDue firmware update instructions](/User_manual/RepRapFirmware/Updating_PanelDue){target=_blank}).

With all firmware versions up to RRF v3.2.2, all firmware update files are stored in the ‘0:/sys/’ directory. From RRF v3.3, to avoid too many files in this folder, all firmware update files are stored in ‘0:/firmware/’ directory.

On Duet 3 only this command take an optional B (board number) parameter which is the CAN address of the board to be updated, default 0 (i.e. main board).

The optional **P** parameter can be used to provide the filename of the file to be used for updating a module. This can either only be a filename in which case it will prepend directories.firmware to it (0:/firmware) or can be an absolute path to the file to be used. It is not allowed to use P parameter and multiple modules, e.g. S1:4. (RRF 3.3 and later)

See [Installing and Updating Firmware](/User_manual/RepRapFirmware/Updating_firmware){target=_blank} for detailed documentation.

## M998: Request resend of line

### Parameters

* **Pnnn** Line number

### Examples
<br>
<pre class="cblock">
M998 P34
</pre>

Request a resend of line 34. 

### Notes

In some implementations the input-handling code overwrites the incoming G Code with this when it detects, for example, a checksum error. Then it leaves it up to the GCode interpreter to request the resend.

## M999: Restart

Restarts the firmware using a software reset.

### Parameters

* *This command can be used without any additional parameters.*
* **Bnnn** Optional CAN address of the board to restart (defaults to 0)
* **Pnnn** Reset flags

### Examples
<br>
<pre class="cblock">
M999
</pre>

### Notes

The **P** parameter can also put the board into firmware upload mode (as if the Erase button had been pressed) if parameter `P"ERASE"` is present.

Starting from v3.3 the **B** parameter may be set to -1 to reboot the attached SBC (DuetPi + SBC). DSF v3.5 also supports `P"OFF"` to shut down the SBC.

# T-commands

## T: Select Tool

### Parameters

* **nnn**: Tool number to select. A negative number deselects all tools.
* **R1**: Select the tool that was active when the print was last paused (firmware 1.20 and later)
* **Pnnn**: Bitmap of all the macros to be run (dc42 build 1.19 or later and ch fork 1.17b or later)
* **Tnn**: (RRF 3.4 and later) Alternative way to specify the tool number, which allows use of an expression to calculate the tool number

### Examples
<br>
<pre class="cblock">
T0                          ; select tool 0
T1 P0                       ; select tool 1 but don't run any tool change macro files
T-1 P0                      ; deselect all tools but don't run any tool change macro files
T R1                        ; select the tool that was active last time the print was paused
T                           ; report the current tool number
T T{state.currentTool + 1}  ; select the tool whose number is one higher than the current tool
</pre>

### Description

If T*n* is used to select tool *n* but that tool is already active, the command does nothing. Otherwise, the sequence followed is:

**Note:** Prior to RRF 3.3, when changing tools, tool change macro files are not run unless all axes have been homed. In RRF 3.3 and later, tool change macro files are run ***regardless of whether axes have been homed or not***. You can use conditional GCode to choose which commands are executed if axes have been homed/not homed.

1. If another tool is already selected, run macro tfree#.g where # is the number of that tool.
1. If another tool is already selected, deselect it and set its heaters to their standby temperatures (as defined by the R parameter in the most recent G10/M568 command for that tool)
1. Run macro tpre#.g where # is the number of the new tool
1. Set the new tool to its operating temperatures specified by the S parameter in the most recent G10/M568 command for that tool
1. Run macro tpost#.g where # is the number of the new tool. Typically this file would contain at least a M116 command to wait for its temperatures to stabilise.
1. Apply any X, Y, Z offset for the new tool specified by G10
1. Use the new tool.

### Notes

Selecting a non-existent tool (49, say) just does Steps 1-2 above, and also removes any X/Y/Z offset applied for the old tool. That is to say it leaves the previous tool in its standby state. You can, of course, use the G10/M568 command beforehand to set that standby temperature to anything you like.

After a reset tools will not start heating until they are selected. You can either put them all at their standby temperature by selecting them in turn, or leave them off so they only come on if/when you first use them. The M0, M1 and M112 commands turn them all off. You can, of course, turn them all off with the M1 command, then turn some back on again. Don't forget also to turn on the heated bed (if any) if you use that trick.

Tool numbering starts at 0 by default however M563 allows the user to specify tool numbers, so with them you can have tools 17, 29 and 48 if you want. Negative numbers are not allowed. The highest Tool number that can be defined from RRF3 onwards is 49.

Starting from RRF 3.3beta2 both selecting as well as deselecting with a configured spindle will stop the spindle assigned to these tools. This is in accordance to NIST GCode standard that says "after a tool change is complete the spindle is stopped".

Under special circumstances, the execution of tool macro files may not be desired. RepRapFirmware 1.19 or later supports an optional **P** parameter to specify which macros shall be run. If it is absent then all of the macros above will be run, else you can pass a bitmap of all the macros to be executed. The bitmap of this value consists of tfree=1, tpre=2 and tpost=4.

You may wish to include a move to a parking position within the tfreeN.g GCode macro in order to allow the new extruder to reach temperature while not in contact with the print.

Tool offsets are applied whenever there is a current tool. So they are applied in tfree.g (for the outgoing tool) and in tpost.g (for the incoming tool), but not in tpre.g (because no tool is current at that point).

# GCode Background Information

Codes for print head movements follow the [NIST GCode Interpreter Version 3 standard](http://www.nist.gov/manuscript-publication-search.cfm?pub_id=823374){target=_blank}, so RepRapFirmware should be usable for CNC milling and similar applications but be aware of the [GCodes not implemented](/User_manual/Reference/Gcodes_not_implemented){target=_blank}. See also on [Wikipedia](https://en.wikipedia.org/wiki/G-code){target=_blank}. For more information and background, along with the master list of all RepRap GCodes, check [RepRap GCode page](http://reprap.org/wiki/G-code){target=_blank}.

For the technically minded, GCode line endings are Unix Line Endings (**\n**), but will accept Windows Line Endings (**\r\n**), so you should not need to worry about converting between the two, but it is best practice to use Unix Line Endings where possible.

## Replies from the RepRap machine to the host computer

All communication is in printable ASCII characters. Messages sent back to the host computer are terminated by a newline and look like this:

**xx [line number to resend] [T:93.2 B:22.9] [C: X:9.2 Y:125.4 Z:3.7 E:1902.5] [Some debugging or other information may be here]**

**xx** can be one of **ok**, **rs** or **!!**

**ok** means that no error has been detected.
**rs** means resend, and is followed by the line number to resend.
**!!** means that a hardware fault has been detected. The RepRap machine will shut down immediately after it has sent this message.

The **T:** and **B:** values are the temperature of the currently-selected extruder and the bed respectively, and are only sent in response to M105. If such temperatures don't exist (for example for an extruder that works at room temperature and doesn't have a sensor) then a value below absolute zero (-273^o^C) is returned.

**C:** means that coordinates follow. Those are the **X: Y:** etc values. These are only sent in response to M114 and M117.

The RepRap machine may also send lines that look like this:

**// This is some debugging or other information on a line on its own. It may be sent at any time.**

Such lines will always be preceded by **//**.

On the latest version of Pronterface and Octoprint (1.2.0+) a special comment of the form:

**// action:command**

is allowed to be sent from the firmware, the command can currently be **pause**, **resume** or **disconnect** which will execute those commands on the host. As this is also a comment other hosts will just ignore these commands. The most common response is simply:

**ok**

When the machine boots up it sends the string

**start**

once to the host before sending anything else. This should not be replaced or augmented by version numbers and the like. M115 (see above) requests those.

All this means that every line sent by RepRap to the host computer except the start line has a two-character prefix (one of **ok**, **rs**, **!!** or **//**). The machine should never send a line without such a prefix.

**Exceptions**:

RepRapFirmware responds to some commands with a reply string in JSON format, terminated by a newline. This allows later firmware revisions to include additional information without confusing clients (e.g. PanelDue) that do not expect it, and to make responses self-describing so that the client will not be confused if responses are delayed or lost. The commands affected are:

* M20 S2
* M36
* M408

## Slicer Start and End GCodes

Slicers will optionally add GCode scripts to the beginning and end of their output file to perform specified actions before and/or after a print such as z-probing the build-area, heating/cooling the bed and hotend, performing ooze free "nozzle wipe" startup routine, switching system power on/off, and even "ejecting" parts. For general information and examples (not specific to RepRapFirmware) there is more info on the [Start GCode routines](https://reprap.org/wiki/Start_GCode_routines){target=_blank} and [End GCode routines](https://reprap.org/wiki/End_GCode_routines){target=_blank} pages.