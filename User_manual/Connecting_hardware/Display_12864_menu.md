---
title: 12864 display menu system
description: 
published: true
date: 2025-05-02T09:19:42.328Z
tags: 
editor: markdown
dateCreated: 2021-11-08T15:18:45.632Z
---

# Introduction

Some Duet 3D mainboards support a dynamically-configured menu system to use on a 12864 display + encoder. As the Duet 3D electronics are capable of being used in many different configurations, with differing requirements, a fixed menu system is not suitable. Therefore the menu structure is defined in files on the Duet mainboard SD card.

# Supported Duets

Support for 12864 displays was originally introduced for Duet 2 Maestro. 12864 support has now been extended to other Duet boards:

* Duet 3 Mini 5+ - from RRF 3.1.1
* Duet 2 WiFi and Ethernet - experimental, needs extra hardware, from RRF 3.2 beta1
* Duet Maestro - RRF 2.x and 3.x

See [Connecting 12864 and other displays](/User_manual/Connecting_hardware/Display_12864) for details.

**NOTE:** on **Single Board Computer** (SBC, i.e. Raspberry Pi) setups where a 12864 display is connected to a supported Duet board (see above), the file and macro list are unavailable in the 12864 menus. This is because the SBC presents a virtual SD card rather than a physical one that the 12864 system can not access.
See [this thread on the forum](https://forum.duet3d.com/topic/32997/duet-3-sbc-with-12864-display-sd-card-access) for a workaround to allow access to specific macros.

# Configuration

Before a 12864 display can be used, the M918 P1 Enn command must be sent to enable it (P1) and set the encoder resolution and direction (Enn, where nn usually needs to be -4, -2, 2 or 4). This command would normally be put in the config.g file. See [M918](/User_manual/Reference/Gcodes/M918) in the Gcode dictionary for more details. Example:

```
M918 P1 E4 F2000000 ; configure direct-connect display
```

Folder **/menu** of the micro SD card installed in the Duet is used to hold menu files. Each file in **/menu** defines one page on the display. There must be a file called **main** in **/menu**, which is the initial file loaded at startup.

There are a number of published user-created menu file sets you can use:

* JadonM - [UltimateDuetMenuSystem](https://github.com/jadonmmiller/UltimateDuetMenuSystem)
As far as we know, JadonM's menu system is currently the only one that supports selecting and printing files from the external SD card on the display itself.
* mudcruzr - [Duet-Maestro-12864-Menu-Files](https://github.com/mudcruzr/Duet-Maestro-12864-Menu-Files)
* jottesman - [DUET3D_12864LCD_MenuFiles](https://github.com/tinkerlifeprojects/DUET3D_12864LCD_MenuFiles)

Many thanks to these users for their work!

If you want to create your own set of menu files, or modify existing ones, read on.

# Menu files

You can create and edit menu files within Duet web Control version 1.22.1 and later. If the /menu folder doesn't exist on the SD card, it will be created when you use the New File button to create a menu file.

Generally, each line in a menu file describes one display element. Each line has the following syntax:

*command parameter1 parameter2 parameter3 ...*

The command must be one of the recognised keywords. Most commands cause a display element to be written to the display. Each parameter is similar to a parameter in G- or M-code, i.e. a letter followed by a number or a string, or a colon-separated sequence of numbers. String parameters must always be enclosed in double-quote characters. Parameters may be optional (depending on the command), in which case default values are assumed.

Parameters common to several commands have the following meanings:

* R is the row number for the top of the display element, in pixels counting down from the top of the display, with R0 being the top row. The default is the same row as used for the previous element. The default at the start of the file is 0.
* C is the column number for the left hand edge of the display element, in pixels counting from the left, with C0 being the leftmost column. The default is one pixel after the previous element ends. The default at the start of the file is 0.
* A is an action to execute (see later)
* F is a font to select. There are currently two fonts, 0 (small, the default) and 1.
* D is a number of decimal places to show when displaying numbers. If zero (which is the default), no decimal point is shown.
* T is text to be displayed. In firmware 2.02 and later, the letter T can be omitted because an un-prefixed quoted string will be treated as a T parameter.
* L is a menu or image filename to be used
* W is the width of the element in pixels. If not specified then for buttons and text items, the minimum width needed to print the text is used, with a small margin to allow for highlighting in the case of buttons.
* H is the horizontal alignment (RRF 2.02 and later). 0 = left (default), 1 = centre, 2 = right. Buttons are always centre-aligned.

The following commands are supported:

| Command | Parameters | Action |
|:---|:---|
| image | [Rnn] [Cnn] [Fnn] L"filename" | Display the bitmap image read from the specified file. |
| text | [Rnn] [Cnn] [Wnn] [Hn] [Fnn] T"text" | Display the specified text using the specified font. |
| button | [Rnn] [Cnn] [Wnn] [Fnn] T"text" [Vnn] A"action" [L"filename"] | Display selectable  text using the specified font. If the text is clicked then execute the specified action. Some actions require a filename. |
| value | [Rnn] [Cnn] [Wnn] [Hn] [Fnn] [Dnn] Nvv | Display the specified value with the specified width to the specified number of decimal places |
| alter | [Rnn] [Cnn] [Wnn] [Hn] [Fnn] [Dnn] Nvv | Display the specified value with the specified width to the specified number of decimal places and allow it to be altered |
| files | [Rnn] [Fnn] Nnn I"initial-directory" | Display a list of files N files high and allow one of the files to be selected. The list uses the full width of the display. This menu item type is not available when the Duet is used with a Single Board Computer. |

An action string ('A' parameter) can be any of:

* A Gcode command string (must begin with G, M or T). In such a string, #0 represents the full name of the current file, in double quotes. When executing the 'files' command, this is the file selected by the user. When executing the 'button' command, this is copied from the L parameter.
* 'menu *name** chains to the menu file called *name*
* 'return' returns to the parent menu

Multiple actions can be specified, separated by the vertical-bar character, e.g. "M32 #0|return|return" but 'menu' if present may only be the last command.

The V parameter in the 'button' command specifies the button's visibility with value:

| Value | Meaning |
|:---|:---|
| 0 | always visible (default) |
| 2 | visible when the printer is actively printing (actively printing defined as not paused, pausing or resuming) |
| 3 | visible when the printer is NOT actively printing |
| 4 | visible when the printer is printing (includes paused, pausing and resuming states) |
| 5 | visible when the printer is NOT printing |
| 6 | visible when the printer is printing and in paused state (paused or pausing) |
| 7 | visible when the printer is printing and NOT in paused state (actively printing or resuming) |
| 10 | visible when SD card 0 is mounted |
| 11 | visible when SD card 0 is NOT mounted |
| 20 | visible when the current or default tool has a temperature fault |
| 28 | visible when the bed heater has a temperature fault |

In RRF 3.5.0 and later the value can instead be an expression enclosed in { } that yields a Boolean value, for example: `V{heat.heaters[0].current>60}`

The N parameter in the "value" and "alter" commands specifies the value to display or change as follows:

| Value(s) | Alterable? | Meaning |
|:---|:---|
| 000-078 | no | Tool N first heater current temperature e.g. 0 = tool 0 current temperature |
| 079 | no | Currently selected tool first heater current temperature |
| 080-089 | no | Bed heater (N-80) current temperature e.g. 80 = bed heater 0 current temperature |
| 090-099 | no | Chamber heater (N-90) current temperature e.g. 90 = chamber heater 0 current temperature |
| 100-178 | yes | Tool (N-100) first heater active temperature e.g. 100 = tool 0 active temperature |
| 179 | yes | Currently selected tool first heater active temperature |
| 180-189 | yes | Bed heater (N-180) active temperature e.g. 180 = bed heater 0 active temperature |
| 190-199 | yes | Chamber heater (N-190) active temperature e.g. 190 = chamber heater 0 active temperature |
| 200-278 | yes | Tool (N-200) first heater standby temperature e.g. 200 = tool 0 standby temperature |
| 279 | yes | Currently selected tool first heater standby temperature |
| 280-289 | yes | Bed heater (N-280) standby temperature e.g. 280 = bed heater 0 standby temperature |
| 290-299 | yes | Chamber heater (N-290) standby temperature e.g. 290 = chamber heater 0 standby temperature |
| 300-398 | yes | Fan (N-300) percent full PWM e.g. 302 = fan 2 percent |
| 399 | yes | Current tool fan percent full PWM |
| 400-499 | yes | Extruder (N-400) extrusion factor |
| 500 | yes | Speed factor |
| 501 | no (1) | Message from the last M117 command |
| 510-518 | yes (2) | Current axis location in user coordinates (X, Y, Z, U, V, W respectively) |
| 520 | yes | Currently selected tool number |
| 521 | yes (1) | Z baby-step offset |
| 530-533 | no | Actual IP address, octets 1 through 4. Deprecated when using firmware 2.03 or later, use 534 instead. |
| 534 | no (3) | Actual IP address |
| 535 | no (3) | Percentage of the current print file that has been processed |
| 536 | no (3) | Estimated print time remaining, based on progress through the file |
| 537 | no (3) | Estimated print time remaining, based on filament consumption (not always available) |
| 538 | no (3) | Requested speed of the current move |
| 539 | no (3) | Actual top speed of the current move |

(1) = implemented in firmware 2.02 and later only

(2) = alterable in firmware 2.02 and later only

(3) = implemented in firmware 2.03RC1 and later only

In RRF 3.5.0 and later, in a "value" command the value can instead be an expression enclosed in { } that yields the value to be displayed, for example: `N{heat.heaters[0].current}`

## File format for 'image' command

Byte 0: image width in pixels (call this W)

Byte 1:  image height in pixels (number of rows)

Next n bytes (n = W/8 rounded up to a whole number): Row 0 pixels. The most significant bit of the first byte is the leftmost pixel.

Next n bytes: Row 1 pixels; and so on.

## Example menu files

File "main":

```
text R0 C0 F1 T"My Super 3D Printer "
image L"reprapimg.bin"
text R15 C0 F0 T"Bed temp "
alter N180 W30
text T" actual "
value N80 W30
button R27 C0 T"Preheat PLA" A"M98 P#0" L"/macros/Preheat PLA"
button R39 C0 T"Preheat ABS" A"M98 P#0" L"/macros/Preheat ABS"
button R51 C0 T"Select file to print" A"menu" L"listFiles"
```

**Note:** From RRF 3.5.0 the firmware expects M98 commands to have the macro name or path/name in quotes. See [this thread on the forum](https://forum.duet3d.com/topic/35429/3-5rc4-12864-display-menu-m98). The above should work in RRF 3.5.0 and earlier version. It could be rewritten as:

```
button R27 C0 T"Preheat PLA" A"M98 P""/macros/Preheat PLA"""
```
However, this may then not work in earlier versions of RRF.

File "listFiles":

```
text R0 C0 F0 T"Select file to print  "
button T"Back" A"return"
files R15 N4 I"/gcodes" A"M32 #0|return"
```