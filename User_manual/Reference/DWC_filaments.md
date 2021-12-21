---
title: Filaments
description: DWC and RRF implement a mechanism to simplify the loading and unloading of filaments into tools that have exactly one extruder drive. The purpose of this is to allow for easier filament exchanges and to keep track of the used materials.
published: true
date: 2021-12-14T23:56:27.964Z
tags: 
editor: markdown
dateCreated: 2021-12-03T15:42:10.493Z
---

![filaments_01.jpg](/manual/configuration/filaments_01.jpg =400x)

# Introduction

Duet Web Control and RepRapFirmware from 1.19 implement a mechanism to simplify the loading and unloading of different filaments into tools that have exactly one extruder drive assigned. The purpose of this is to allow for easier filament exchanges and to keep track of the used materials.

# Overview

Each loaded filament, once loaded, is automatically saved in a new file called "/sys/filaments.csv". This file may be enhanced in the future to include usage statistics for different materials.

# Creating new filaments

On Duet Web Control 1.19 you have new page called "Filaments". Switch to this page and create a new filament entry. If you haven't configured any filaments yet, you may see an error message on the filaments page, but this message should fade once a new item is created:

![filaments_02.png](/manual/configuration/filaments_02.png)

Once done, you can modify the GCode macros that are invoked when the filament is either loaded or unloaded by right-clicking on the new entry:

![filaments_03.png](/manual/configuration/filaments_03.png)

# Load macros

As mentioned before, the purpose of the new filament mechanism is to simplify the process of loading and unloading macros. Hence the load macro is invoked for a given tool with one extruder when it is explicitly loaded. An example for a load macro (for ABS in this case) may look like this:

```
M291 P"Please wait while the nozzle is being heated up" R"Loading ABS" T5 ; Display message
G10 S200 ; Set current tool temperature to 200C
M116 ; Wait for the temperatures to be reached
M291 P"Feeding filament..." R"Loading ABS" T5 ; Display new message
M83 ; Extruder to relative mode
G1 E10 F600 ; Feed 10mm of filament at 600mm/min
G1 E470 F3000 ; Feed 470mm of filament at 3000mm/min
G1 E20 F300 ; Feed 20mm of filament at 300mm/min
G4 P1000 ; Wait one second
G1 E-10 F1800 ; Retract 10mm of filament at 1800mm/min
M400 ; Wait for moves to complete
M292 ; Hide the message
G10 S0 ; Turn off the heater again
```

You may adjust this template for your own needs. It can be modified by right-clicking on the entries as shown in the previous section.

# Unload macros

Similar to the load macro there is another macro to perform necessary steps to eject the current filament. This macro is always invoked when a filament is being changed or unloaded. An example (once more for ABS) may look like this:

```
M291 P"Please wait while the nozzle is being heated up" R"Unloading ABS" T5 ; Display message
G10 S100 ; Heat up the current tool to 100C
M116 ; Wait for the temperatures to be reached
M291 P"Retracting filament..." R"Unloading ABS" T5 ; Display another message
G1 E-20 F300 ; Retract 20mm of filament at 300mm/min
G1 E-480 F3000 ; Retract 480mm of filament at 3000mm/min
M400 ; Wait for the moves to finish
M292 ; Hide the message again
M84 E0:1 ; Turn off extruder drives 1 and 2
```

# Using filaments

Filament assignments can be only made for tools that have exactly one extruder drive assigned. This limitation ensures that the load and unload macros work with each extruder drive.

## Loading filaments

In principle filaments can be always loaded by clicking on Tools that have one extruder assigned. When you click on the tool caption, a popover shows show up asking you to either select the tool or to load a filament:

![filaments_04.png](/manual/configuration/filaments_04.png)

When you click on "Load Filament", you will get a list of all the filaments that are not in use. You can only load one filament at once. The current filament mechanism is intended to reflect your spools and the underlying mechanism will be likely enhanced to include some usage statistics as well.

![filaments_05.png](/manual/configuration/filaments_05.png)

Once you click on an entry, the tool you clicked on first will be selected (unless it is already selected) and the load macro of the chosen filament will be run. Once that is complete, Duet Web Control will display the new filament in the tool row and the new filament assignment is saved to the Duet's SD card.

## Unloading filaments

Unloading filaments works very similar to the load process. Just click on the tool with a loaded filament and you should see another popover:

![filaments_06.png](/manual/configuration/filaments_06.png)

When you click on "Unload", the firmware will select the chosen tool (unless it is already selected) and invoke the unload macro. Once that is complete, the firmware will erase the filament assignment for the tool's extruder drive.

## Changing filaments

If a filament is already loaded and you only want to change it, there is another option in the popover for this purpose. In principle DuetWebControl will select the clicked tool (unless already selected), run the unload macro of the currently loaded filament and then the load macro for the chosen other filament. If you want to make use of this mechanism, it is advised to add extra blocking prompts using [M291](/User_manual/Reference/Gcodes/M291) to the load and/or unload macros.

## Custom per filament config.g

In addition to the macros for loading and unloading a filament, you can also use the M703 gcode to load a custom filament specific config.g to modify settings pertinent to your chosen filament.

After assigning a filament to a tool, this command may be used to run /filaments/\<filament-name>/config.g to set parameters like temperatures, extrusion factor, retract distance, etc. If no filament is loaded, the code completes without a warning.

If the filaments feature is used, it is recommended to put this code into tpost*.g to ensure the right filament parameters are set. Supported in RepRapFirmware 2.02 and newer.

The filament config.g only needs to contain the commands you want to modify. It does NOT need to duplicate every setting from /sys/config.g

*Example custom commands for /filaments/PETG/config.g*

```
M207 S0.9 R0.0 F3000 T3000 Z0.0           ; firmware retraction settings for PETG
M307 H0 A214.3 C641.7 D1.3 S1.00 V24.0 B0 ; Bed PID tune for 120c
M307 H1 A491.0 C190.9 D2.8 S1.00 V23.9 B0 ; Hot end PID tune at 280c
M566 X1200 Y1200 Z60 E3000                ; jerk settings for PETG
M204 P1000 T4000                          ; Set printing and travel accelerations
```

These settings will then override those loaded at boot time in /sys/config.g

# GCodes

The filament mechanism utilizes three GCodes:
* [M701](/User_manual/Reference/Gcodes/M701) to load a filament
* [M702](/User_manual/Reference/Gcodes/M702) to unload a filament
* [M703](/User_manual/Reference/Gcodes/M703) to load a custom filament config.g