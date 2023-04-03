---
title: Setting up macro files for common tasks
description: You can use macro files to automate common tasks. A macro file is simply a text file on the SD card containing a sequence of GCode commands.
published: true
date: 2022-05-16T11:46:12.186Z
tags: 
editor: markdown
dateCreated: 2021-12-03T19:28:44.097Z
---

# Overview

You can use macro files to automate common tasks. A macro file is simply a text file on the SD card containing a sequence of GCode commands.

If you want a macro file to appear in the list of macros shown by Duet Web Control and PanelDue, put it in the **/macros** folder of the SD card. If you don't want a macro to appear in the user interface, you can put it in the **/sys** folder. This folder already contains macros that are run automatically when certain events occur, for example the homing macros and the pause and resume macros.

The SD card shipped with your Duet may already contain some files with UPPER_CASE names in **/macros**. These are used by the factory for testing new Duets. You may delete them.

## Examples

There are a number of examples here: [Macros](/User_manual/Tuning/Macros)

# Creating macros

You can create macros easily in Duet Web Control. To create a new macro file in the /macros folder, go to the **Macros** page of [Duet Web Control](/User_manual/Reference/Duet_Web_Control_Manual) and click on **New file**. 

# Naming and ordering macros

The name of a macro file in the /macros folder should be the text that you want to appear in the macro lists shown by Duet Web Control and PanelDue. You do not need a ".g" extension at the end.

By default the macros are listed in alphabetical order. You can change this by prefixing the macro names with one or more decimal digits followed by an underscore, for example:

1_Off
2_Half On
3_On

The full filename will be used when the macros are sorted to display them in order, but only the text after the underscore will be displayed on the button. So the above will display as:

Off
Half On
On

Note: versions of PanelDue prior to 1.20beta2 do not implement this feature.

# Running Macros

## What happens when a macro is run?

The following values in GCodeMachineState get saved/restored when a macro is called, or by push/pop: 
drivesRelative, axesRelative, volumetricExtrusion, g53Active, usingInches, macroRestartable

When a system macros is run (see [Macros](/User_manual/Tuning/Macros)), they ignore workplace coordinate offsets.

Note that if a pause ([M25](/User_manual/Reference/Gcodes/M25)) is commanded while a macro is being executed, the pause will be deferred until the macro has completed.

## From gcode

Macros can be run from any source of code:

* From a gcode file printing from the SD card (e.g. a custom macro to wipe the tool head on a brush as part of the start gcode)
* From a console connected by USB/telnet/DWC etc
* From another macro - they can be nested.

To run a macro the M98 gcode is used:

[M98 P"mymacro.g"](/User_manual/Reference/Gcodes/M98)

## From Duet Web Control

Duet Web Control has an interface for managing, editing and uploading macros.

![macros_02.png](/manual/configuration/macros_02.png =500x)

For more information see the Duet Web Control Manual [Duet Web Control Manual](/User_manual/Reference/Duet_Web_Control_Manual)

Once macros are setup then can be run from the macro page or directly from the machine control page:

![macros_03.png](/manual/configuration/macros_03.png =800x)

## From the Panel Due

Macros can be selected from the macro page on the Panel Due:

![macros_04.jpg](/manual/configuration/macros_04.jpg =500x)

A number of macros can be displayed on the control page as well for quick access:

![macros_05.jpg](/manual/configuration/macros_05.jpg =500x)

(*4.3 inch screen pictured*)

See the previous section on how to choose the order that the macros are displayed.