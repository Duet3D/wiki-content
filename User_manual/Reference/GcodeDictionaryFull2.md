---
title: GDF2
description: 
published: true
date: 2021-12-21T02:21:23.451Z
tags: 
editor: markdown
dateCreated: 2021-12-21T02:21:23.451Z
---


## M408: Report JSON-style response

Deprecated in RRF 3.3 and later. Use M409 to get response from Object Model, which provides more information.

**Parameters**

* **Snnn** Response type (only used when P is zero or not present)
* **Rnnn** Response sequence number (see **seq** and **resp** in list below)

**Examples**

```
M408 S0
```

**Notes**

This reports a JSON-style response by specifying the desired type using the 'S' parameter. The following response types are supported:

* Type 0 is a short-form response, similar to the response used by older versions of the web interface.
* Type 1 is like type 0 except that static values are also included.
* Type 2 is similar to the response provided by the web server for Duet Web Control.
* Type 3 is an extended version of type 2 which includes some additional parameters that aren't expected to change very frequently.
* Type 4 is an extended version of type 2 which may be used to poll for current printer statistics.
* Type 5 reports the current machine configuration.

Here is an example of a typical type 0 response:

```
{"status":"I","heaters":[25.0,29.0,28.3],"active":[-273.1,0.0,0.0],"standby":[-273.1,0.0,0.0],"hstat":[0,2,1],"pos":[-11.00,0.00,0.00],"extr":[0.0,0.0],"sfactor":100.00, "efactor":[100.00,100.00],"tool":1,"probe":"535","fanPercent":[75.0,0.0],"fanRPM":0,"homed":[0,0,0],"fraction_printed":0.572}
```

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

For a more detailed comparison of type 2 - 5, see [Status Responses](https://github.com/Duet3D/RepRapFirmware/wiki/JSON-responses).

PanelDue currently uses only M408 S0 and M408 S1.

## M409: Query object model

**Parameters**

* **K"key"** Key string, default empty
* **F"flags"** Flags string, default empty

**Examples**

```
M409 K"move.axes" F"f"   ; report all frequently-changing properties of all axes
M409 K"move.axes[0] F"v,n,d4"  ; report all properties of the first axis, including values not normally reported, to a maximum depth of 4
M409 K"move.axes.homed"  ; for all axes, report whether it is homed
M409 K"#move.axes"     ; report the number of axes
M409 F"v"          ; report the whole object model to the default depth
```

**Notes**

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

For details of the Object Model supported by RepRapFirmware, see [Object Model of RepRapFirmware](https://github.com/Duet3D/RepRapFirmware/wiki/Object-Model-Documentation).

## M450: Report Printer Mode

**Examples**

```
M450
```

**Notes**

Printers can be used for different task by exchanging the toolhead. Depending on the tool, a different behavior of some commands can be expected. This command reports the current working mode. Possible responses are:

PrinterMode:FFF

PrinterMode:Laser

PrinterMode:CNC

The default at power up is FFF.

## M451: Select FFF Printer Mode

**Parameters**

* none

**Examples**

```
M451
```

**Notes**

Switches to Fused Filament Fabrication mode for filament printing.

## M452: Select Laser Device Mode

### {.tabset}

#### RepRapFirmware 3.x

**Parameters**

* **C"name"** Pin name(s) and optional inversion status, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names). A leading '!' character inverts the input or output.
* **Rnnn** The value of the S parameter in G1 commands that corresponds to full laser power, default 255
* **Sn** 1= laser power is sticky across G1 commands, 0 (default) = laser is off when executing G1 commands that have no S parameter
* **Fnnn** The PWM frequency used to drive the laser - default is 500Hz

**Example**

```
; old code (RRF 2.x):
M452 P3 I1 F100  ; laser uses heater3 pin, PWM frequency 100Hz

; new code (RRF 3.x):
M452 C"!exp.heater3" F100 ; laser uses heater3 pin inverted, PWM frequency 100Hz
```

**Notes**

Switches to laser mode. This mode enables handling of a laser pin and makes sure that the laser is only activated during G1 moves if laser was enabled (using G1 Snn moves) or E is increasing (using [M571](/User_manual/Reference/Gcodes/M571)). G0 moves should never enable the laser.

**Very important!** If you use M452 to put your machine into Laser mode and are upgrading from RepRapFirmware **v2.01 or earlier**, you must replace all S parameters in G0/G1 commands in homing files etc. with H parameters. This is because S is now used to control laser power.

In RRF3, the P and I parameters are removed. Use the C parameter to select the laser control pin instead.

M3 and M5 no longer turn the laser on and off; use G1 Snn moves to control laser power.

See also [Configuring RepRapFirmware for a laser engraver/cutter](/User_manual/Machine_configuration/Configuration_laser).

#### RepRapFirmware 2.x

**Parameters**

* **Pnnn** Logical pin number used to control the laser
* **In** Invert (I1) or don't invert (I0, default) the output polarity
* **Rnnn** The value of the S parameter in G1 and/or M3 commands that corresponds to full laser power, default 255
* **Sn** 1= laser power is sticky across G1 commands, 0 (default) = laser is off when executing G1 commands that have no S parameter
* **Fnnn** The PWM frequency used to drive the laser - default is 500Hz

**Examples**

```
M452 P2 R255 F200 ; switch to laser mode using the heater 2 (E1 heater) output pins to control the laser
```

**Notes**

Switches to laser mode. This mode enables handling of a laser pin and makes sure that the laser is only activated during G1 moves if laser was enabled (using M3 Snn or G1 Snn moves) or E is increasing (using [M571](/User_manual/Reference/Gcodes/M571)). G0 moves should never enable the laser. 

M3/M5 can be used to enable/disable the laser for moves. 

Logical pin numbers for the P parameter are as defined for the M42 and M208 commands. If a heater or fan output is used to control the laser, you must disable the corresponding heater (see M307) or fan (see M106) first.

**Very important!** If you use M452 to put your machine into Laser mode and are upgrading from RepRapFirmware **v2.01 or earlier**, you must replace all S parameters in G0/G1 commands in homing files etc. with H parameters. This is because S is now used to control laser power.

See also [Configuring RepRapFirmware for a laser engraver/cutter](/User_manual/Machine_configuration/Configuration_laser).

## M453: Select CNC Device Mode

### {.tabset}

#### M453 in RepRapFirmware 3.3beta2 and later

**Parameters**

none

Switches to CNC mode. All other parameters have been removed and moved into M950.

**Examples**

```
; Old code
M453 S0 T1 C"!exp.heater3" R12000 ; Assign tool 1 to spindle index 0, with PWM pin on heater 3 and 12000 RPM achieved at full PWM

; New code
M950 R0 C"!exp.heater3" L12000 ; Create spindle index 0, with PWM pin on heater 3 and 12000 RPM achieved at full PWM
M563 P1 S"Spindle 1" R0 ; Create tool 1 with spindle 0 and call it "Spindle 1"
M453
```

```
; Old code
M453 S0 T1 C"exp.heater3+exp.heater4+exp.heater5" Q100 ; spindle PWM on heater 3 pin, on/off on heater 4, reverse/forward on heater 5, PWM frequency 100Hz

; New code
M950 R0 C"exp.heater3+exp.heater4+exp.heater5" Q100
M563 P1 S"Spindle 1" R0 ; Create tool 1 with spindle 0 and call it "Spindle 1"
M453
```

#### M453 in RepRapFirmware 3.2

**Parameters**

* **Snnn** (optional) Spindle index, defaults to 0. Duet 2 supports 4 spindles max
* **C"aaa+bbb+ccc"** Names of the ports used to drive the spindle motor. "aaa" is the PWM port used to control the speed. "bbb" (optional) is the digital port used to turn the spindle motor on. "ccc" (optional) is the name of the port used to command reverse instead of forward spindle rotation.
* **Rbbb** or **Raaa:bbb** (optional) RPM values that are achieved at zero PWM and at maximum RPM. Used to convert the S parameter in M3 and M4 commands to a PWM value.
* **Qnnn** (optional) The PWM frequency to use
* **Tnnn** (optional) Assign spindle to a tool allowing better control in DWC

**Examples**

```
M453 C"exp.heater3+exp.heater4+exp.heater5" Q100 T1 ; spindle PWM on heater 3 pin, on/off on heater 4, reverse/forward on heater 5, PWM frequency 100Hz
```

#### M453 in RepRapFirmware 3.0 and 3.1.x

**Parameters**

* **Snnn** (optional) Spindle index, defaults to 0. Duet 2 supports 4 spindles max
* **C"fff+rrr"** Names of the ports used to drive the spindle motor in clockwise and counterclockwise directions. Omit the "+rrr" part if the spindle turns clockwise only.
* **Rnnn** Spindle RPM that is achieved at full PWM. Used to convert the S parameter in M3 and M4 commands to a PWM value.
* **Fnnn** (optional) The PWM frequency to use
* **Tnnn** (optional) Assign spindle to a tool allowing better control in DWC

**Examples**

```
M453 C"exp.heater3+exp.heater4" F100 T1 ; spindle forward/reverse on heater 3&4 pins, PWM frequency 100Hz
```

#### M453 in RepRapFirmware 1.x and 2.x

**Parameters**

* **Snnn** (optional) Spindle index, defaults to 0. Duet 2 supports 4 spindles max
* **Pfff:rrr** Logical pin numbers used to drive the spindle motor in clockwise and counterclockwise directions. Omit the ":rrr" part if the spindle turns clockwise only. (Not supported in RRF3, see notes)
* **In** Invert (I1) or don't invert (I0, default) the output polarity. (Not supported in RRF3, see notes)
* **Rnnn** Spindle RPM that is achieved at full PWM. Used to convert the S parameter in M3 and M4 commands to a PWM value.
* **Fnnn** (optional) The PWM frequency to use
* **Tnnn** (optional) Assign spindle to a tool allowing better control in DWC

**Examples**

```
M453 P2 R5000 ; switch to CNC mode using heater 2 (E1 heater) pins to control the spindle motor
```

Logical pin numbers for the P parameters are as defined for the M42 and M208 commands. If you wish to assign a heater or fan output to control the spindle motor as in the above example, you must first disable the corresponding heater (see M307) or fan (see M106).

**Notes**

Switches to CNC mode. In this mode M3/M4/M5 control the pins defined for the milling device. By default, no output is assigned to a spindle motor, so it must be configured. 

See also [Configuring RepRapFirmware for a CNC machine](/User_manual/Machine_configuration/Configuration_CNC).

## M470: Create Directory on SD-Card

**Parameters**

* **P"name"** Name of directory to create

**Examples**

```
M470 P"/sys/config.d"
```

**Notes**

This will create a new directory on the SD-Card. If not otherwise specified the default root should be the first/internal SD-Card.

Supported in RRF >= 2.03.

## M471: Rename File/Directory on SD-Card

Supported in RRF >= 2.03.

**Usage**

* M471 S"source/name" T"dest/name" D1

**Parameters**

* **S"name"** Name of existing file/directory
* **T"name"** New name of file/directory
* **Dnnn** Setting this to 1 will delete any existing file that matches the T parameter value

**Examples**

```
M471 S"/sys/config-override.g" T"/sys/config-override.g.bak"
```

**Notes**

Rename or move a file or directory. Using the D parameter will delete any existing file with the target name. Renaming or moving across directories is possible though not from one SD-Card to another.

## M486: Object cancellation

Supported in RRF 3.1 and later.

**Parameters**

* **Tnn** Tell the firmware how many objects there are
* **S[index]** Inserted in GCode to indicate which object is being printed
* **Ann** Used in conjunction with S parameter, names an object
* **Pnn** Cancel object nn. First object is 0.
* **Unn** Uncancel object nn. First object is 0.
* **C** Cancel current object

**Examples**

```
M486 T12               ; Total of 12 objects (otherwise the firmware must count)
M486 S3                ; Indicate that the 4th object is starting now
M486 S3 A"cube copy 3" ; Indicate that the 4th object is starting now and name it
M486 S-1               ; Indicate a non-object, purge tower, or other global feature
M486 P10               ; Cancel object with index 10 (the 11th object)
M486 U2                ; Un-cancel object with index 2 (the 3rd object)
M486 C                 ; Cancel the current object (use with care!)
M486                   ; List the objects on the build plate
```

**Descripton**

This provides an interface to identify objects on the print bed and cancel them. Basic usage: Use **M486 Tnn** to tell the firmware how many objects there are, so it can provide an LCD interface. (Otherwise the firmware counts them up in the first layer.) In every layer of your GCode, preface each object's layer slice with **M486 S[index]** to indicate which object is being printed. The index should be zero-based. To cancel the first object, use **M486 P0**; to cancel the 5th object use **M486 P4**; and so on. The "current" object is canceled with **M486 C**.

GCodes associated with the canceled objects are no longer printed. Firmware supports this feature by ignoring G0-G3/G5 moves in XYZ while updating F and keeping the E coordinate up-to-date without extruding.

Slicers should number purge towers and other global features with a negative index (or other flag) to distinguish them from regular print objects, since it is important to preserve color changes, purge towers, and brims.

In RepRapFirmware, if the GCode file being printed contains object label comments (e.g. using the "Label objects" option in PrusaSlicer) then it is not necessary to use M486 S commands to indicate which object is being printed. Objects on the build plate will be numbered from 0 in the order in which their labels first appear in the file.

If you do use M486 S commands in the GCode file instead of object label comments, then RepRapFirmware provides an optional A parameter to the M486 S command to allow objects to be named. The name of each object need only be specified once.

For the benefit of Duet Web Control and other user interfaces, the list of objects on the build plate known to RRF may also be retrieved from the object model using M409, including their names (if available) and approximate locations.

## M500: Store parameters

Implemented in RepRapFirmware 1.17 and later.

**Parameters**

* **Pnn** or **Pnn:nn** Stores additional parameters. P31 stores G31 Probe status parameters; P10 stores G10/M568 tool offsets.

**Examples**

```
M500
M500 P31
M500 P10
M500 P10:31
```

**Description**

Save current parameters to the sys/config-override.g on the SD card, similar to other firmware's storing to EEPROM. The parameters stored are:

* M307 auto tune results
* PID parameters, if you used M301 to override the auto tune PID settings
* Delta printer M665 and M666 settings
* Any M208 axis limits that were determined using a G1 S3 move
* If the P31 parameter is used, the G31 trigger height, trigger value and X and Y offsets for each possible Z probe type (in older firmware versions the G31 parameters are stored even if the P31 parameter is not present)
* If the P10 parameter is present, the G10/M568 tool offsets

Ensure that M501 is at the end of config.g in order for the values in config-override.g to be loaded on startup.

## M501: Read stored parameters

**Parameters**

* None

**Examples**

```
M501
```

**Description**

Set the active parameters to those stored in sys/config-override.g on the SD card. This command should be included near the end of the config.g file so that the stored parameters will be loaded at startup. It can also be used to revert parameters to the values stored in config-override.g after experimenting with them.

## M502: Revert stored parameters

**Examples**

```
M502
```

**Description**

This sets all machine parameters to the values defined in config.g, ignoring the config-override.g file so that any changes that were saved by M500 are not applied. It does this by running config.g but ignoring any M501 commands that it contains.

M502 does not clear or reset the config-override.g file; so next time the machine is started, the values that were saved by M500 will once again be applied. If you want to cancel the changes saved by M500 permanently, you can run M502 and then M500.

## M503: Print settings

**Examples**

```
M503
```

**Description**

This command asks the firmware to reply with the current print settings stored in sys/config.g. The reply output includes the GCode commands to produce each setting. For example, the Steps Per Unit values are displayed as an M92 command.

**Notes**

The output may be truncated if it is too long. M503 does **not** include values stored in config-override.g.

## M505: Set configuration file folder

**Parameters**

* P"name" ; name of folder, default path is the existing sys path if a relative path is given

**Examples**

```
M505 P"experimental" ; change config file path from /sys/ to /sys/experimental/
```

**Description**

Following this command, files that would normally be fetched from /sys/ (for example, homing files and system macro files in RepRapFirmware) are fetched from the specified folder instead. Any such files that are already being executed will continue to run.

This command can be used to allow multiple configurations to be maintained easily. In RepRapFirmware the file /sys/config.g can contain just these two lines:

```
M505 P"config1"
M98 P"config.g"`
```

The first line changes the config file folder to /sys/config1 and the second one executes file config.g in that folder. To select an alternative configuration, only the first line needs to be edited.

## M540: Set MAC address

**Parameters**

* **Pnnn** The MAC address

**Examples**

```
M540 PDE:AD:BE:EF:CA:FE
```

**Description**

Sets the [MAC address](http://en.wikipedia.org/wiki/MAC_address) of the printer. This should be done before any other network commands. The MAC address is six one-byte hexadecimal numbers separated by colons. Only works on Ethernet-equipped Duet mainboards, in standalone mode (i.e. not Duets with WiFi or Duet boards with SBC).

**Notes**

On WiFi-equipped Duet boards (Duet 3 Mini 5+ WiFi and Duet 2 WiFi) the MAC address is unique and set on the WiFi Module so this command has no effect. 

The default MAC address on a Ethernet-equipped Duet boards is generated from the unique processor ID so there is normally no need to change it.

All devices running on the same network should have different MAC addresses. For your printers, changing the last digit is sufficient.

## M550: Set Name

**Parameters**

* **P"nnn"** Machine name

**Examples**

```
M550 P"Godzilla"
```

**Description**

Sets the name of the printer to (in the case of the above example) Godzilla. The name can be any string of printable characters except ';', which still means start comment. The name shows at the top of the DWC page.

The machine name is also used to allow local network discovery using **mDNS local network discovery**. Rather than remembering the ip address of the printer to connect to, or having to find it if assigned by DHCP (mDNS works both with fixed ip address and DHCP), you can use it's name. Using the example name above, in your browser connect to the DWC with `http://Godzilla.local`.

**Notes**

Quotation marks around the machine name are mandatory in RRF3, but discretionary in earlier firmware versions.

Using the machine name to access the machine on the network relies on mDNS. This needs to be supported on the device trying to connect. See a longer description about [mDNS support here](/User_manual/Machine_configuration/Networking#a-note-about-mdns-local-network-discovery).

The machine name is also used as the NetBIOS name, which can help to identify the Duet on a network. This is only supported on Duet 2 WiFi and legacy Duet 0.6/0.85.

Both the mDNS and NetBIOS name are limited to 15 characters. If you use a longer name, the mDNS name will be the first 15 characters, eg if the Duet name is "3DPrinterWithVeryLongName", you should still be able to connect to "3DPrinterWithVe.local".

## M551: Set Password

**Parameters**

* **P"nnn"** Password

**Examples**

```
M551 P"my-very-secret-word"
```

**Description**

The code 'P' is not part of the password. Note that as this is sent in clear it does not (nor is it intended to) offer a security. But on machines that are (say) on a network, it might prevent idle messing about. The password can contain any printable characters except ';', which still means start comment.

**Notes**

If the specified password differs from the default one (i.e. reprap), the user will be asked to enter it when a connection is established.

Quotation marks around the password are mandatory in RRF3, but discretionary in earlier firmware versions.

## M552: Set IP address, enable/disable network interface

### {.tabset}

#### WiFi interfaces (Duet 2/3 WiFi)

**Parameters**

* **Innn** (Optional) Number of the network interface to manage (defaults to 0).
* **P"ssid"** (optional, RepRapFirmware 1.20 and later) SSID of network to connect to. The SSID and password must already have been registered using M587. If this parameter is not present, the WiFi will try to connect to the strongest network that is broadcasting its SSID and whose SSID has been registered using M587.
* **Snnn** 0 = disable networking, 1 = enable networking as a client, 2 = enable networking as an access point , -1 = disable WiFi module

**Examples**

```
M552 S1 P"MyNetwork"
```

Enables networking as a client, and joins the network with the SSID 'MyNetwork', using the parameters (password, IP/gateway address, netmask) configured in [M587](/User_manual/Reference/Gcodes/M587).

**Notes**

Also works with the WiFi interface on an attached SBC. See M587 for configuration limitation.

On Duet boards with WiFi interfaces running firmware 1.19 and later, the IP address is set in the M587 command when you configure the access point details.

#### Ethernet interfaces (Duet 2/3 Ethernet and 06/085)

**Parameters**

* **Innn** (Optional) Number of the network interface to manage (defaults to 0).
* **Pnnn** IP address, 0.0.0.0 means acquire an IP address using DHCP
* **Snnn** 0 = disable networking, 1 = enable networking
* **Rnnn** (Optional) HTTP port, default 80 (Deprecated, RepRapFirmware 1.17 and earlier only)

**Examples**

**Duet 2 Ethernet:**
```
M552 S1 P192.168.1.14
```

Sets the IP address of the machine to (in this case) 192.168.1.14. If the S parameter is not present then the enable/disable state of the network interface is not changed.

**Duet 3 in SBC mode:**

* M552 I1 S1 P0.0.0.0 ; set the second interface on the SBC to use DHCP and enable it.

The I1 setting here specifies the second network interface on the SBC. This uses the [DuetPi Management Plugin](https://github.com/Duet3D/DuetSoftwareFramework/tree/v3.3-dev/src/DuetPiManagementPlugin#readme) (installed by default from RRF 3.3 onwards) to set the address on the SBC. To determine which interface is which on the SBC the object model explorer can be used to see the current settings of each interface.

**Notes**

M552 with no parameters reports the current network state and IP address.

In firmware 1.18 and later the HTTP port address is set using the M586 command, so the R parameter of this command is no longer supported.

## M553: Set Netmask

**Parameters**

* **Innn** (Optional) Number of the network interface to manage (defaults to 0)
* **Pnnn** Net mask

**Examples**

```
M553 P255.255.255.0
```

Sets the network mask of the RepRap machine to (in this case) 255.255.255.0. A restart may be required before the new network mask is used. If no 'P' field is specified, this echoes the existing network mask configured.

DuetWifiFirmware versions 1.18 and earlier do not support setting the network mask manually.

## M554: Set Gateway and/or DNS server

**Parameters**

* **Innn** (Optional) Number of the network interface to manage (defaults to 0)
* **Pnnn** Gateway
* **Snnn** (Optional) DNS server (only supported by DSF 3.3 with DuetPi system config plugin)

**Examples**

```
M554 P192.168.1.1
```

Sets the Gateway IP address of the RepRap machine to (in this case) 192.168.1.1. A restart may be required before the new gateway IP address is used. If no 'P' field is specified, this echoes the existing Gateway IP address configured.

DuetWifiFirmware versions 1.18 and earlier do not support setting the gateway address.

## M555: Set compatibility

**Parameters**

* **Pnnn** Emulation type

**Examples**

```
M555 P1
```

**Description**

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

**Parameters**

* **Snnn** Height of the measured distances
* **Xnnn** Deviation in X direction
* **Ynnn** Deviation in Y direction
* **Znnn** Deviation in Z direction
* **Pnnn** Apply XY compensation to Y axis instead of X (defaults to 0, requires RRF 3.2-b4 or newer)

**Examples**

```
M556 S100 X0.7 Y-0.2 Z0.6
```

**Description**

This tells software the tangents of the angles between the axes of the machine obtained by printing then measuring a test part. The S parameter is the length of a triangle along each axis in mm. The X, Y and Z figures are the number of millimeters of the short side of the triangle that represents how out of true a pair of axes is. The X figure is the error between X and Y, the Y figure is the error between Y and Z, and the Z figure is the error between X and Z. Positive values indicate that the angle between the axis pair is obtuse, negative acute.

Printable parts for calibrating the deviation from orthogonality can be found on the [RepRapPro Github repository](https://github.com/reprappro/RepRapFirmware/tree/master/STL). For a fuller explanation of the measuring process, see the [RepRap Ltd documentation](https://reprapltd.com/reprappro/documentation/ormerod-2/axis-compensation/index.html#Orthogonal_Axis_Compensation)

## M557: Set Z probe point or define probing grid

### {.tabset}

#### Define G29 probe grid 

**Parameters **

* **Xaaa:bbb** Minimum and maximum X coordinates to probe
* **Yaaa:bbb** Minimum and maximum Y coordinates to probe
* **X,Y,U,V,W,A,B,C...aaa:bbb** Minimum and maximum coordinates of an arbitrary axis (except Z) to probe (RRF >=3.3beta2)
* **Raaa** Radius to probe
* **Saaa** Probe point spacing (RepRapFirmware 1.19beta10 and later also support **Saaa:bbb**)
* **Pnn** or **Pxx:yy** (RRF 2.02 and later) Number of points to probe in the X and Y axis directions (alternative to specifying the probe point spacing)

All values in mm.

**Examples**

```
M557 X0:200 Y0:220 S20
M557 X0:100 Y0:120 S50:60
M557 R150 S15
```

**Description**

Defines the grid for [G29 Mesh Bed probing](/User_manual/Reference/Gcodes/G29). For Cartesian printers, specify minimum and maximum X and Y values to probe and the probing interval. For Delta printers, specify the probing radius. If you define both, the probing area will be the intersection of the rectangular area and the circle. There is a firmware-dependent maximum number of probe points supported. Currently this is 441 for the Duet 2 and Duet 3 (enough for a 21x21 grid), and 121 on the Duet 06/085 (enough for a 11x11 grid).

**Notes**

In RRF >= 3.3beta2 it is possible to use an arbitrary axes pair for probing, e.g. X-A or U-C. When using **Raaa** to define a radius this will default to X-Y.

#### Define G32 probe points

**Parameters**

Deprecated and not supported in firmware 1.18 and later. Cartesian/CoreXY printers only,

* **Pnnn** Probe point number
* **Xnnn** X coordinate
* **Ynnn** Y coordinate

**Examples**

```
M557 P1 X30 Y40.5
```

**Description**

Defines the points for for G32 bed probing. The P value is the index of the point (indices start at 0) and the X and Y values are the position to move extruder 0 to to probe the bed. An implementation should allow a minimum of three points (P0, P1 and P2). This just records the point coordinates; it does not actually do the probing. See [G32](/User_manual/Reference/Gcodes/G32). Defining the probe points in this way is deprecated in RepRapFirmware, you should define them in a bed.g file instead.

## M558: Set Z probe type

### {.tabset}

#### RepRapFirmware 3.x

**Parameters**

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

**Order dependency**

M558 must come before G31.

**Examples**

```
M558 P5 C"e0stop" H5 F120 T3000  ; Z probe connected to Duet 2 E0 endstop input

M574 Z0 P"nil" ; (RRF 3.0 on Duet 2 ONLY) no Z endstop switch, free up Z endstop input
M558 P5 C"zstop" H5 F120 T3000  ; Z probe connected to Duet 2 Z endstop input

; BL Touch on Duet 3 Mini 5+
M950 S0 C"io3.out" ; servo/gpio 0 is io3.out pin
M558 P9 C"io3.in" H5 F500:120 T3000 ; BLTouch connected to io3.in pin
...
M280 P0 S10 ; send control signal to BLTouch through servo/gpio 0

; BLTouch on Duet WiFi
M950 S0 C"exp.heater3" ; create servo/gpio 0 on heater 3 pin on expansion connector
M558 P9 C"^zprobe.in" H5 F120 T3000  ; BLTouch connected to Z probe IN pin
...
M280 P0 S10 ; send control signal to BLTouch through servo/gpio 0

; For the Duet Smart Effector on Duet 2
M558 P8 C"zprobe.in+zprobe.mod" R0.4 F1200 ; zprobe.mod is the programming pin for M672
```

**Notes**

A Z probe may be a switch, an IR proximity sensor, or some other device. The **P** parameter selects which to use:

* P0 indicates that no Z probe is present. Whenever Z probing is commanded, you will be prompted to jog the Z axis until the nozzle is just touching the bed and then signal completion.
* P1 specifies an unmodulated or smart IR probe, or any other probe type that emulates one (probe output is an analog signal that rises with decreasing nozzle height above the bed). If there is a control signal to the probe, it is driven high when the probe type is P1.
* P2 specifies a simple modulated IR probe, where the modulation is commanded directly by the main board firmware using the control signal to the probe.
* P3 is similar to P1 but drives the control signal to the probe low. This may be used to switch between different Z probes.
* P5 selects a switch by default (normally closed) for bed probing between the In and Gnd pins of the IO connector (Duet 3) or Z-probe connector (Duet 2).
* P8 is as P5 but is unfiltered, for faster response time.
* P9 is as P5 but for a BLTouch probe that needs to be retracted and redeployed between probe points.
* P10 means use Z motor stall detection as the Z probe trigger.

Z probe types 4, 6 and 7 (used in RRF 2.x) are no longer supported. Instead, use type 5 (filtered digital) or 8 (unfiltered digital) and use the C parameter to specify the input. 

M558 with P parameter deletes the existing probe with that K number (if any) and creates a new Z probe. This resets the G31 values for that probe to default values.

If your Z probe is connected to the Z endstop input, **in RRF 3.0 on Duet 2 boards only** (not in RRF 3.01 and later, and not in RRF 3.0 on Duet 3), that input is by default pre-assigned to be used by the Z endstop, so you must free it up first with `M574 Z0 P"nil"`.

Only one Type 2 probe can be configured, and if using Duet 3 then it must be connected to the Duet 3 main board, not to a CAN-connected expansion or tool board.

The **C** parameter specifies the input pin and the optional modulation pin. See [Pin names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names) for a list of available pins and their names to use. Invert the input by prefixing the input pin with ! character, when using an NPN output inductive or capacitive sensor or using an NO switch (not recommended, use a NC switch instead). The pullup resistor on the Z probe input is disabled by default. Enable it by prefixing the input pin (C parameter) with the ^ character. Enable pullup resistor with ^ if using Duet 2, running RRF3, using the Z probe input pin, and the probe type is a switch or BLTouch.

The **H** parameter defines the Z probe dive height, which is the height above the trigger height from which probing starts. The default is 3mm or 5mm depending on firmware version. You may wish to increase it during initial calibration. When using mesh bed compensation or running G30 commands with specified XY coordinates (for example from the bed.g file), the firmware moves the Z probe to this height above where it expects the bed to be before commencing probing. The maximum depth of probing from this position is twice the dive height. A large dive height will tolerate a very uneven bed or poor calibration. A small dive height will make probing faster, because the Z probe has less distance to travel before reaching the bed. Default value if omitted is 5mm.

From RRF 3.3 you can provide two **F** parameters instead of one, where the second is lower than the first, for example F1000:500. When doing a plain G30 command, an additional probe will be done using the first speed to establish the approximate bed position, before one or more additional probes are done using the second speed. The first speed will not be used when probing at a defined point or when mesh bed probing.

The **A** and **S** parameters control multiple probing. Probing is repeated until two consecutive probe attempts produce results that differ by no more than the S parameter; then the average of those two results is used. For example, S-1 would force averaging. However, if the number of attempts specified by the A parameter is reached without getting two consecutive results within tolerance of each other, no further probe attempts are made and the average result of all the attempts is used.

Related commands: [G29](/User_manual/Reference/Gcodes/G29), [G30](/User_manual/Reference/Gcodes/G30), [G31](/User_manual/Reference/Gcodes/G31), [G32](/User_manual/Reference/Gcodes/G32), [M401](/User_manual/Reference/Gcodes/M401), [M402](/User_manual/Reference/Gcodes/M402).

See also: [Choosing a Z probe](/User_manual/Connecting_hardware/Z_probe_choosing), [Connecting a Z probe](/User_manual/Connecting_hardware/Z_probe_connecting)

#### RepRapFirmware 2.x and earlier

**Parameters**

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

**Order dependency**

M558 must come before G31.

**Examples**

```
M558 P1 X1 Y0 Z1 F500 T5000 H3 ; Z probe is used for homing X and Z axes (RRF 1.19 and earlier)

M558 P4 H5 F120 T3000  ; Z probe connected to E0 endstop input

M558 P7 H5 F120 T3000  ; Z probe connected to Z endstop input

; BLTouch on Duet Maestro
M558 P9 H5 F120 T3000  ; BLTouch connected to Z probe IN pin
...
M280 P64 S10 ; send control signal to BLTouch through Z probe MOD pin

; BLTouch on Duet WiFi
M558 P9 H5 F120 T3000  ; BLTouch connected to Z probe IN pin
M307 H3 A-1 C-1 D-1 ; free up heater 3 to use as BLTouch servo pin
...
M280 P3 S10 I1 ; send control signal to BLTouch through heater 3 pin
```

**Notes**

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

In RepRapFirmware versions 1.20beta4 and earlier, the **X**, **Y** and **Z** parameters specify whether each axis uses the Z probe as a substitute homing switch or not. If the parameter is nonzero, the Z probe is used for homing that axis. If the parameter is zero, the endstop switch for that axis is used for homing instead. In firmware 1.20beta6 and later, use the S parameter in the [M574](/User_manual/Reference/Gcodes/M574) command instead to indicate whether you are using a homing switch or a Z probe for homing X and Y.

Related commands: [G29](/User_manual/Reference/Gcodes/G29), [G30](/User_manual/Reference/Gcodes/G30), [G31](/User_manual/Reference/Gcodes/G31), [G32](/User_manual/Reference/Gcodes/G32), [M401](/User_manual/Reference/Gcodes/M401), [M402](/User_manual/Reference/Gcodes/M402).

See also: [Choosing a Z probe](/User_manual/Connecting_hardware/Z_probe_choosing), [Connecting a Z probe](/User_manual/Connecting_hardware/Z_probe_connecting)

## M559: Upload file

**Parameters**

* **P**"filename". File name to upload to. 
* **S**nnn File size for binary transfer. If not present, the transfer is terminated by a M29 command
* **C**nnn CRC-32 of the file (optional)

**Examples**

```
M559 P"config.g"
```

**Notes**

In RRF 3.1 and earlier, the default filename is config.g and the default path is /sys. In RRF 3.2 and later there is no default filename.

Quotation marks around the filename are mandatory in RRF 3.2 and later.

Was used by the original web interface to upload a configuration file. Can now be used to upload any file.

## M560: Upload file

**Parameters**

* **P**"filename". File name to upload to. 
* **S**nnn File size for binary transfer. If not present, the transfer is terminated by a special string, described below.
* **C**nnn CRC-32 of the file (optional)

**Examples**

```
M560 P"index.html"
```

**Notes**

In RRF 3.1 and earlier, the default filename is reprap.htm and the default path is /www.

After sending M560 the file should be sent, terminated by the string:
`<!-- **EoF** -->` 
Clearly that string cannot exist in the body of the file, but can be put on the end to facilitate this process.

## M561: Set Identity Transform

**Examples**

```
M561
```

**Description**

This cancels any bed-plane fitting as the result of probing (or anything else) and returns the machine to moving in the user's coordinate system.

## M562: Reset temperature fault

**Parameters**

* **Pnnn** Heater number

**Examples**

```
M562 P2 ; Reset a temperature fault on heater 2
```

**Notes**

If the heater has switched off and locked because it has detected a fault, this will reset the fault condition and allow you to use the heater again. Obviously to be used with caution. If the fault persists it will lock out again after you have issued this command. P0 is heater H0, P1 is heater H1, and so on.

In firmware 1.20 and later, M562 with no parameters will clear a heater fault on all heaters

## M563: Define or remove a tool

**Parameters**

* **Pnnn** Tool number
* **S"name"** Tool name (optional)
* **Dnnn** Extruder drive(s)
* **Hnnn** Heater(s)
* **Fnnn** Fan(s) to map fan 0 to (RepRapFirmware 1.16 and later)
* **Xnnn** Axis or axes to map X movement to (RepRapFirmware 1.16 and later)
* **Ynnn** Axis or axes to map Y movement to (RepRapFirmware 1.19 and later)
* **Lnnn** Drive to use for filament mapping. By default RRF will use the first and only extruder drive if this parameter is not specified (supported by RRF >= 2.02)
* **Rnn** Spindle number (RRF >= 3.3)

**Examples**

```
M563 P0 D0:2:3 H1:3 ; create a tool using extruder drives 0, 2 and 3 and heaters 1 and 3
M563 P1 D1 H2 X3 ; create a tool using extruder drive 1 and heater 2 with X movement mapped to the U axis
M563 P2 D0:1 H1:2 X0:3 F0:2 ; create a tool using extruder drives 0 and 1, heaters 1 and 2, with X movement mapped to both X and U axes and fan 0 mapped to fans 0 and 2
M563 P3 D0 H1 S"Chocolate extruder" ; create a named tool using extruder drive 0 and heater 1
```

**Description**

Tools are usually (though not necessarily) extruders. Normally an M563 command to define a tool is immediately followed by a G10/M568 command to set the tool's offsets and temperatures.

**P** The 'P' field specifies the tool number. In RRF3, tool numbers may be between 0 and 49. In RRF2 they may be between 0 and 65535. If you use the M563 command with a P value for a tool that has already been defined, and you provide any other parameters, that tool is redefined using the new values you provide.

**D** The 'D' field specifies the drive(s) used by the tool - in the first example drives 0, 2 and 3. The 'D' field number corresponds to the 'E' parameter defined in the M584 command. '0' means first 'E' driver in M584 and so on. Drive 0 is the first drive in the machine after the movement drives (usually X, Y and Z). If there is no 'D' field the tool has no drives. Tools are driven using multiple values in the 'E' field of G1 commands, each controlling the corresponding drive in the 'D' field above, as follows:

```
G1 X90.6 Y13.8 E2.24:2.24:15.89
G1 X70.6 E0:0:42.4
```

The first line moves straight to the point (90.6, 13.8) extruding a total of 2.24mm of filament from both drives 0 and 2 and 15.98mm of filament from drive 3. The second line moves back 20mm in X extruding 42.4mm of filament from drive 3.

Alternatively, if the slicer does not support generating G1 commands with multiple values for the extrusion amount, the M567 command can be used to define a tool mix ratio.

**H** The 'H' field specifies the tool's heaters - in the first example heaters 1 and 3. Heater 0 is usually the hot bed (if any) so the first extruder heater is usually 1. If there is no H field the tool has no heaters. It is permissible for different tools to share some (or all) of their drives and heaters. So, for example, you can define two tools with identical hardware, but that just operate at different temperatures.

**F** The print cooling fan of the tool, default 0. Use this parameter if you are not using fan 0 as the print cooling fan for the tool you are defining. You do not need to, and should not, map thermostatic fans to tools. To map more than one fan to the tool, the definition would typically look like this:

```
M563 P0 D0 H1 F0:1 ; tool 0 uses extruder drive 0 and heater 1. Fan 0 and Fan 1 are mapped to tool 0
```

**R** The spindle number mapped to this tool. (RRF >= 3.3beta2)

```
M563 P0 R0 ; assign spindle 0 to tool 0
```

**X, Y** The X and Y mapping option is used to create tools on machines with multiple independent X and/or Y carriages. The additional carriages are set up as axes U, V etc. (see M584) and the X mapping option in M563 defines which carriage or carriages are used. Axes are mapped in the order XYZUVWABC, where X=0, Y=1, Z=2, U=3 etc, not by driver number.

**S** As shown in the example above the S parameter can be used to give a tool a name. RepRapFirmware supports an additional form of the M563 command. The command:

```
M563 S1
```

means add 1 (the value of the S parameter) to all tool numbers found in the remainder of the current input stream (e.g. the current file if the command is read from a file on the SD card), or until a new M563 command of this form is executed. The purpose of this is to provide compatibility between systems in which tool numbers start at 1, and programs such as slic3r that assume tools are numbered from zero.

RepRapFirmware maps the loaded filament on a per-extruder basis so if you have a mixing tool (one with more than one extruder), the **L** parameter tells the web interface which filament to display. If there is more than one extruder and the L parameter is omitted, no filament is displayed at all.

**Notes**

M563 with just a P parameter just reports the existing configuration of the tool. Therefore, if you want to create a tool with no heaters and no extruders, you must provide at least one other parameter. For example, you can use the S parameter to name the tool.

RepRapFirmware allows the deletion of existing tools if M563 is called in this way:

```
M563 P1 D-1 H-1
```

## M564: Limit axes

**Parameters**

* **Hnnn** H1 = forbid movement of axes that have not been homed, H0 = allow movement of axes that have not been homed (firmware 1.21 and later)
* **Snnn** S1 = limit movement within axis boundaries, S0 = allow movement outside boundaries

**Examples**

```
M564 S0 H0
```

**Description**

Allow moves outside the print volume and before homing, or not. If the S parameter is 0, then you can send G codes to drive the RepRap outside its normal working volume, and it will attempt to do so. Likewise if the H parameter is zero you can move the head or bed along axes that have not been homed. The default behaviour is S1 H1. On some types of printer (e.g. Delta and SCARA), movement before homing may be prohibited regardless of the H parameter.

## M566: Set allowable instantaneous speed change

**Parameters**

* **Xnnn** Maximum instantaneous speed change of the X axis (mm/min)
* **Ynnn** Maximum instantaneous speed change of the Y axis
* **Znnn** Maximum instantaneous speed change of the Z axis
* **Ennn** Maximum instantaneous speed change of the extruder drives
* **Pn** Jerk policy (RepRapFirmware 2.03 and later)

**Examples**

```
M566 X600 Y600 Z50 E600
```

**Description**

Sets the maximum allowable speed change (sometimes called 'jerk speed') of each motor when changing direction.

The model files and GCode files used by repraps generally render circles and other curves shapes as a sequence of straight line segments. If the motors were not allowed any instantaneous speed change, they would have to come to a stop at the junction between each pair of line segments. By allowing a certain amount of instantaneous speed change, printing speed can be maintained when the angle between the two line segments is small enough.

**X** and **Y** parameter: If you set the X and Y values too low, then the printer will be slow at printing curves. If they are too high then the printer may be noisy when cornering and you may suffer ringing and other print artefacts, or even missed steps.

**Z** parameter: When mesh bed compensation is used, movement may be jerky if the allowed Z jerk is too low, because the Z speed needs to change abruptly as the head moves between squares in the mesh.

**P** parameter: The default jerk policy is 0, which replicates the behaviour of earlier versions of RRF (jerk is only applied between two printing moves, or between two travel moves, and only if they both involve XY movement or neither does). Changing the jerk policy to 1 allows jerk to be applied between any pair of moves.

**Notes**

The minimum jerk speed supported in as at firmware version 2.02RC3 is 0.1mm/sec.

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour.

Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M566 Z50, not M566 Z50:50

## M567: Set tool mix ratios

**Parameters**

* **Pnnn** Tool number
* **Ennn** Mix ratios

**Examples**

```
M567 P2 E0.1:0.2:0.1:0.6
```

This example sets the mix ratio for tool 2 (the P value). When a G1 or G0 command contains only a single E value but the current tool uses multiple extruders, each extruder will extrude the amount specified in the E parameter times the mix value for that tool. For example:

G1 X20 E1.3

This will move to X=20 extruding a total length of filament of 1.3mm. The first drive of tool 2 will extrude 0.1\*1.3mm, the second 0.2\*1.3mm and so on. The ratios don't have to add up to 1.0 - the calculation done is as just described. But when using a mixing hot end it is normally sensible if they do, otherwise under or over extrusion would occur. When printing concurrently with multiple print heads (e.g. ditto printing on an IDEX machine), it is normal for each print head to have a [total] extrusion factor of 1.0.

**Notes**

**WARNING**: Currently the firmware will allow to set a mixing ratio where every extruder will drive at 100% - in case of a setup where several extruders feed the same hotend (like Diamond Hotends) this can lead to dangerously high pressures that could damage the printer.

The default is for the first driver to be set to 1, and all others to be set to 0. (ie for 2 drivers the default is E1:0)

## M568: Set Tool Settings

*Available in RepRapFirmware 3.3beta2 and later. The R and S parameters are alternatives to the temperature-setting functions of G10, which may be deprecated in the future.*

**Usage**

* M568 Pnnn Rnnn Snnn Fnnn An

**Parameters**

* **Pnnn** Tool number. If this parameter is not provided, the current tool is used.
* **Rnnn** Standby temperature(s)
* **Snnn** Active temperature(s)
* **Fnnn** Spindle RPM
* **An** Required heater state: 0 = off, 1 = standby temperature(s), 2 = active temperature(s). Supported in RRF 3.3 and later. If there is a current tool and the P parameter specifies a different tool, then any heaters used by the current tool are not affected.

**Examples**

```
M568 P1 R140 S205 ; set standby and active temperatures for tool 1
M568 P0 F5200 ; set spindle RPM for tool 0
M568 P2 A1  ; set tool 2 heaters to their standby temperatures
```

**Description**


The **R** value is the standby temperature in °C that will be used for the tool, and the **S** value is its operating temperature. If you don't want the tool to be at a different temperature when not in use, set both values the same.

The **F** value will be used to set the configured spindle RPM for this tool's spindle. This value is direction-independent and needs to be given as a positive number. If the spindle is already running it will apply the new speed to the current direction (selected by M3/M4) immediately.

**Notes**

Remember that any parameter that you don't specify will automatically be set to the last value for that parameter.

RepRapFirmware will report the tool parameters if only the tool number is specified.

Temperatures set with M568 do not wait for the heaters to reach temp before proceeding. In order to wait for the temp use a M116 command after the M568 to wait for temps to be reached.

See the [T code (select tool)](/User_manual/Reference/Gcodes/T) below. In tools with multiple heaters the temperatures for them all are specified thus: R100.0:90.0:20.0 S185.0:200.0:150.0 .

See also [M585](/User_manual/Reference/Gcodes/M585).

#### M568: Deprecated : Turn off/on tool mix ratios

**Deprecated:** from firmware 1.19 onwards, this command is no longer required or supported. If a G1 command for the tool provides just one E value, then the mix ratio defined by M567 will always be used.

**Parameters**

* **Pnnn** Tool number
* **Snnn** Whether mix ratios should be activated. 0 (default) mixing is turned off; non-zero it is turned on.

**Examples**

```
M568 P2 S0
```

Turn on/off automatic mix ratios for tool 2. 

**Notes**

After turning off command G1 instructions must send as many E values as the tool has drives: G1 X20 E0.2:0.4:0.166:0.3

## M569: Set motor driver direction, enable polarity, mode and step pulse timing

**Parameters**

* **Pnnn** Motor driver number
* **Snnn** Direction of movement of the motor(s) attached to this driver: 0 = backwards, 1 = forwards (default 1)
* **Rnnn** Driver enable polarity: 0 = active low, 1 = active high, -1 = driver is always disabled and is not monitored (default 0)
* **Tnnn** (firmware 1.14 and later) Minimum driver step pulse width and interval in microseconds
* **Taa:bb:cc:dd** (firmware 1.21 and later) Minimum driver step pulse width, step pulse interval, direction setup time and direction hold time, in microseconds
* **Dnn** (firmware 2.0 and later, only applies to TMC2660, TMC22xx, TMC2160, TMC5160 and TMC5161 stepper drivers) Driver mode: 0=constant off time, 1=random off time, 2=spread cycle, 3=stealthChop or stealthChop2 (mode 3 for TMC22xx/TMC2160/TMC516x only), 4 = Closed Loop (only for [Duet 3 Expansiom 1HCL boards](/Duet3D_hardware/Duet_3_family/Duet_3_Toolboard_1HCL)). The default is spreadCycle for TMC2660, TMC2160 and TMC516x drivers, and stealthChop2 for TMC22xx. In stealthChop mode the drivers will switch over to spreadCycle automatically at high speeds, see the V parameter.
* **Fnn** (firmware 2.02 and later) Off-time in the chopper control register, 1 to 15
* **Cnnnn** (firmware 2.0 and later, only applies to TMC2660, TMC22xx, TMC2160 and TMC516x stepper drivers) Lowest 17 bits of the chopper control register value.
* **Bnn** (firmware 2.02 and later) Blanking time (*tbl*) in the chopper control register, 0 to 3. See the TMC driver datasheet.
* **Hnn** (firmware 2.02 and later) *thigh* parameter for those stepper driver chips that support it, e.g. TMC5160 and TMC2160. Send M569 P# (where # is the driver number) with no additional parameters to see how this translates into mm/sec. See also the V parameter.
* **Yaa:bb** or **Yaa:bb:cc** (firmware 2.02 and later) Hysteresis start, end and decrement values in the chopper control register. See the TMC driver datasheet for the meaning.
* **Vnnn** (firmware 2.02 and later) *tpwmthrs* parameter for those stepper driver chips that support it. This is the interval in clock cycles between 1/256 microsteps below which the drivers will switch from stealthChop to to spreadCycle mode. Only applies when the driver is configured in stealthChop mode. Typical value are from 100 (high speed) to 4000 (low speed). Send M569 P# (where # is the driver number) with no additional parameters to see how this translates into mm/sec.

**Examples**

```
M569 P0 S0        ; reverse the direction of the motor attached to driver 0
M569 P5 R1 T2.5:2.5:5:0  ; driver 5 requires an active high enable, 2.5us minimum step pulse, 2.5us minimum step interval, 5us DIR setup time and no hold time
```

**Notes**

* If no T parameter is given, then on boards having internal drivers the step pulse width and interval are guaranteed to be suitable for the on-board drivers only, and will generally be too fast for external drivers. On the EXP1XD board the default is T2.6:2.6:2.6:2.6.
* RepRapFirmware only remembers the highest T parameters seen in any M569 command, and applies that value to all drivers for which any nonzero T parameter was specified. Therefore if you reduce the T parameters, you must restart the firmware for the change to take effect. On Duet 3 systems with CAN-connected expansion boards this note applies separately to each board, and does not apply to the EXP1XD.
* Some versions of RepRapFirmware prior to 1.14 also provided XYZ and E parameters to allow the mapping from axes and extruders to stepper driver numbers to be changed. From 1.14 onward, this functionality is provided by M584 instead.

## M569.1: Stepper driver closed loop configuration

**Parameters**

* **Pnn** Motor driver number
* **Tn** Encoder type: 0=none, 1=linear quadrature encoder on axis, 2=quadrature encoder on motor shaft, 3=Duet closed loop magnetic sensor (based on the AS5047D)
* **Cn.n** Encoder counts per full step (when using a quadrature encoder on motor shaft) [count per mm will become available as support for linear encoders is implemented]
* **En.n:m.m** Error thresholds. Closed loop errors greater than m will be treated as a stall. Closed loop errors greater than n will be treated as a pre-stall. The action that is taken on a stall/pre-stall can be configured using M915. Note: Although this is supported on the 1HCL closed loop boards, it is not yet supported in the main RepRap Firmware.
* **Rn.n** Proportional constant
* **In.n** Integral constant
* **Dn.n** Derivative constant
* **Hn.n** Minimum holding current as a percentage of the configured motor current when operating in closed loop mode

**Description**

Set the configuration parameters of a closed loop driver. See the [M569](/User_manual/Reference/Gcodes/M569) D parameter for switching a driver to closed loop after it has been configured.

Encoder counts per step (Cn.n) can be found from the datasheet of the encoder being used. If the value is stated as counts per revolution (CPR), divide by the steps per revolution of the stepper motor to get the count per step. For example, a 1000 CPR encoder attached to a 200 step/rev motor will have a count per step of 1000 ÷ 200 = 5.

**Notes**

Supported for drivers attached to:
* [Duet 3 Expansiom 1HCL boards](/Duet3D_hardware/Duet_3_family/Duet_3_Toolboard_1HCL)

See '[Tuning the Duet 3 Expansion 1HCL](/User_manual/Tuning/Duet_3_1HCL_tuning)' for further details on setting the proportional/integral/derivative constants.

## M569.2: Read or write stepper driver register

Supported in RRF 3.3 and later on main boards with TMC22xx or TMC51xx stepper drivers.

**Parameters**

* **Pnn** Motor driver number
* **Rnn** Register number, 0-127
* **Vnnnn** Value to write (optional)

**Examples**

```
M569.2 P1 R0
```

If the V parameter is not provided, this command reads the specified register and returns the value of that register. If the V parameter is provided, that value is written to the specified register.

**WARNING!** Use of M569.2 to write stepper driver registers may result in damage to the stepper drivers, for example from excessive motor current or insufficient blanking time.

## M569.3: Read Motor Driver Encoder

Report a current motor encoder positions to the host in units of arc degrees (1/360'ths of turns), relative to some reference position that you set with the **S** parameter.

Before the first call with the **S** parameter, the reference is unknown and arbitrary.

**Parameters**

* **Pnn** Motor driver number, or board address and driver number. Several (remote) drivers may be specified, separated by colon. No more than number of visible axes, as specified by M584 P parameter, are allowed.
* **S** Sets an encoder reference point. Current and subsequent M569.3 Pn.n calls returns numbers that are relative to the M569.3 Pn.n S call.

If P is not supplied, an error is returned.

A maximum of four CAN-connected drivers can be reached with M569.3 counting from machine boot. CAN addresses that fail to respond don't count towards this maximum.

**Examples**

```
M569.3
Error: M569: missing parameter 'P'
```

```
M569.3 P54.0
Error: M569.3: Message not received
```

```
M569.3 P50.0:51.0:52.0:53.0
[-155.28, -4089.60, 6842.04, 0.00, ],
```

```
M569.3 P53.0:51.0:52.0:50.0
[0.00, -4089.60, 6842.04, -155.28, ],
```

```
M569.3 P53.0:51.0:52.0:50.0 S
[0.00, 0.00, 0.00, 0.00, ],
```

```
M569.3 P49.0
Error: M569.3: Max CAN addresses we can reference is 4. Can't reference board 49.
```

## M569.4: Set Motor Driver Torque Mode

Tell one or more motor drivers to apply a specified torque regardless of position. Planned for support in RRF 3.4.

**Parameters**

* **Pn** or **Pn.n** Motor driver number, or board address and driver number. Can also be a colon separated list of driver numbers.
* **Tn** Where n is the mode/torque to apply in units of Nm.

If **P** or **T** parameter is missing, then no action is taken.

The driver is put back into position mode by requesting a torque smaller than 0.0001 Nm.

**Examples**

```
M569.4
Error: M569: missing parameter 'P'
```

```
M569.4 P50.0:51.0
Error: M569: missing parameter 'T'
```

```
M569.4 P50.0 T0.001
0.001000 Nm
```

```
M569.4 P50.0:51.0 T0
pos_mode, pos_mode
```

**Notes**

Hangprinter's "torque mode" will be implemented as a RepRapFirmware macro that depends on M569.4.

## M569.5: Closed loop data collection

Collect performance data from a drive whilst in closed loop mode. Can be used alongside the Duet [Closed Loop plugin](https://github.com/Duet3D/Closed-Loop-Plugin) for visualisation. Records back to a CSV file located in the /sys/closed-loop directory, which will be created if it does not exist.

Supported for drivers attached to:

* [Duet 3 Expansiom 1HCL boards](/Duet3D_hardware/Duet_3_family/Duet_3_Toolboard_1HCL)

Note: The driver must be configured in closed loop mode (See [M569](/User_manual/Reference/Gcodes/M569) D parameter).

**Parameters**

* **Pnn** Motor driver number
* **F"filename.csv"** Filename to write to, optional, defaults to *driveraddr_datetime.csv*. All files are stored in the *sys/closed-loop* directory.
* **An** Mode: 0=record immediately, 1=record when the target position of the motor is next changed (i.e. on next move)
* **Rnn** Sample rate. Measured in samples per second. If R0 is sent, the board will record as fast as possible.
* **Dnn** Variable filter. Determines which variables are recorded. See below for a list of available variables and how they are represented.
* **Vnn** Perform a tuning manoeuvre. Available manoeuvres described below.
* **Snn** Number of samples to record. When recording with rate R0, this sample rate is limited depending on the number of variables being recorded. The command will report back the maximum allowable samples if the maximum is exceeded. The maximum allowable samples may differ between board types since it is dependant on the available RAM.

The following variables are available to record:

| Variable Name | Description | Variable ID |
|:---|:---|
| Raw Encoder Reading | The raw reading that the expansion board has read from the encoder. For a quadrature encoder, this represents the number of pulses received, so may be 4 times the expected CPR. | 1 |
| Current motor Steps | The current position of the motor measured in the standard unit of 'steps'. Similar to raw encoder reading, but will be standardised across different types of encoders with different CPRs. | 2 |
| Target Motor Steps | The number of steps that the motor has been commanded to take. This becomes the target signal fed into the PID controller. | 4 |
| Current Error | The difference between the current motor steps and the target motor steps. Directly used as an input to the PID controller. | 8 |
| PID Control Signal | The control signal (normalised between -255 and 255 coming from the PID controller. | 16 |
| PID P Term | The value of the PID controller's P term. | 32 |
| PID I Term | The value of the PID controller's I term | 64 |
| PID D Term | The value of the PID controller's D term. | 128 |
| Step Phase | The current position of the motor's stator, expressed as the proportion of the way through the motor's 4 step cycle. Normalised between 0-4095 | 256 |
| Desired Step Phase | The angle for which current will be applied. For maximal torque, this value will be 25% ahead of step phase, for minimal torque, this value will equal step phase. | 512 |
| Phase Shift | The difference between the desired step phase and the step phase. Roughly proportional to torque applied. | 1024 |
| Coil A Current | The current running through coil A, expressed as a proportion of the motor's maximum current normalised between -255 to 255. | 2048 |
| Coil B Current | The current running through coil B, expressed as a proportion of the motor's maximum current normalised between -255 to 255. | 4096 |

To record multiple variables, sum the variable IDs and pass the resulting value as the D parameter. For example, to record coil A current (2048) and coil B current (4096), use D6144.

The following manoeuvres are available:

| Manoeuvre Name | Description | Manoeuvre ID |
|:---|:---|
| Polarity Detection and Zeroing | Detects in which orientation the stepper motor coils are connected, this will also detect if a motor's wiring is faulty or it is not plugged in. Ensures that a feedback reading of 0 corresponds to the position the encoder assumes when only coil A is energised. Note this move would normally be carried out using M569.6 prior to PID loop tuning. | 1 |
| Absolute SPI Encoder Calibration | Calibrates the encoder positions to the motor. Note this move would normally be carried out using M569.6 prior to PID loop tuning and only needs to be done once per Motor,encoder and 1HCL combination. | 2 |
| Step Manoeuvre | Applies a step change to the PID target to view the step response of the PID controller. | 64 |

**Examples**

```
M569.5 P50.0 S500 A0 R0 D6 V64
```

Record 500 samples (S500) of the current motor steps and target motor steps (D6) of driver 0 attached to board 50 (P50.0) immediately (A0) and as fast as possible (R0) whilst performing a step manoeuvre (V64)

**The Duet [Closed Loop plugin](https://github.com/Duet3D/Closed-Loop-Plugin) can be used to generate and run M569.5 commands.**

## M569.6: Execute closed loop tuning move

Perform a [runtime tuning manoeuvre](/User_manual/Tuning/Duet_3_1HCL_tuning#runtime-tuning) with a closed loop drive.

Supported for drivers attached to:

* [Duet 3 Expansiom 1HCL boards](/Duet3D_hardware/Duet_3_family/Duet_3_Toolboard_1HCL)

**Parameters**

* **Pnn** Motor driver number
* **Vnn** Tuning move(s) to perform. See below for usage details.

**Warning: Duet firmware currently only supports tuning one driver at a time. This means that when tuning a multi-driver axis, one driver will move and the other(s) will not. If attempting to tune a multi-driver axis, please take appropriate mitigation to ensure the axis doesn't become stressed/misaligned when only one one driver moves.**

The table below lists the available tuning manoeuvres. For more information see the dedicated [closed-loop tuning page](/User_manual/Tuning/Duet_3_1HCL_tuning).

| Manoeuvre Name | Description | Required? | Manoeuvre ID |
|:---|:---|
| Polarity Detection and Zeroing | Detects in which orientation the stepper motor coils are connected, this will also detect if a motor's wiring is faulty or it is not plugged in. Ensures that a feedback reading of 0 corresponds to the position the encoder assumes when only coil A is energised. | Yes for all encoder types. This needs to be done after each power on and reset, and ideally should be part of the homing files for axis with closed loop drivers. | 1 |
| Absolute SPI Encoder Calibration | Calibrates the encoder positions to the motor. | Yes for Absolute SPI connected (magnetic) encoders. This needs to be done just once for a combination of motor, encoder and 1HCL board as the results are stored in the 1HCL memory. | 2 |

**Examples**

```
M569.6 P50.0 V1 ; conduct polarity detection and zeroing move on closed loop driver on HCL board at address 50.
M569.6 P51.0 V2 ; conduct absolute SPI encoder calibration on move on closed loop driver on HCL board with magnetic encoder at address 50.
```

## M569.7: Configure motor brake port

**Parameters**

* **Pn.n** Motor CAN board address (if applicable) and driver number
* **C"port"** Port name of the brake control port. The port must be on the same CAN board as the driver. The CAN address does not need to be specified in the port name, but if it is then it must be the same as the driver address.

**Examples**

```
M569.7 P40.0 C"out1"   ; driver 0 on board 40 uses port out1 on board 40 to control the brake
```

When the motor driver is enabled, the specified output port will be turned on at the same time to release the brake. When the motor driver is disabled, the output port will be turned off. Idle current mode does not count as disabled.

Note: after M569.7 is executed, the port will be initially off. Therefore, M569.7 should be executed before the motor is first enabled.

## M570: Configure heater fault detection

**Parameters for RepRapFirmware 1.15e and later**

* **Hnnn** Heater number
* **Pnnn** Time in seconds for which a temperature anomaly must persist on this heater before raising a heater fault (default 5 seconds)
* **Tnnn** Permitted temperature excursion from the setpoint for this heater (default 15C)
* **Snnn** Integer timeout in minutes (can be set to 0) for print to be cancelled after heater fault (Firmware 1.20 and later). If the S parameter timeout occurs (which only happens if a SD print is in progress), RRF will also try to turn off power via the PS_ON pin.

**Parameters for RepRapFirmware 1.14 and earlier**

* **Snnn** Heater timeout (in seconds)

**Examples**

```
M570 S120
```

After a heater has been switched on, wait 120 seconds for it to get close to the set temperature. If it takes longer than this, raise a heater fault.

**Order dependency**

When using RepRapFirmware 3 the M570 command must come later in config,g than the M950 command that creates the heater specified in the H parameter.

**Examples**

```
M570 H1 P4 T15
```

**Warning!** Heating fault detection is provided to reduce the risk of starting a fire if a dangerous fault occurs, for example if the heater cartridge or thermistor falls out of the heater block. You should carefully consider sensible values for the detection time or permitted temperature excursion, setting them to incorrectly will reduce the protection. Also note that this protection should not be relied upon exclusively. Protection against fire should be provided external to the operation of the firmware as well (fuses, fire detection, do not print unattended etc)

## M571: Set output on extrude

**Parameters**

* **Snnn** Output value
* **Fnnn** Output PWM frequency (RepRapFirmware 1.17 and later; deprecated in 3.2beta2 and later)
* **Qnnn** Output PWM frequency (RepRapFirmware 3.2beta2 and later)
* **Pnnn** (RepRapFirmware 1 and 2) Logical pin number (RepRapFirmware 1.17), defaults to the FAN0 output in firmware 1.19 and earlier until M571 with a P parameter has been seen
* **P"pin-name"** (RepRapFirmware 3) Name of the pin to use

**Examples**

```
M571 P3 F200 S1  ; turn on logical pin 3 while extrusion is commanded (RRF 2)
M571 P"heater3" S0.5 ; turn on heater 3 output at 50% PWM while extrusion is commanded (RRF 3)
```

This turns the controlled pin output on whenever extrusion is being done, and turns it off when the extrusion is finished. The output could control a fan or a stirrer or anything else that needs to work just when extrusion is happening. It also can be used to control a laser beam. The S parameter sets the value of the PWM to the output. 0.0 is off; 1.0 is fully on.

In RepRapFirmware 1.17 and later you can use the P parameter to change the pin used and you can also set the PWM frequency. For RepRapFirmware 1.x and 2.x, pin numbers are the same as in the M42 and M280 commands. The pin you specify must not be in use for anything else, so if it is normally used as a heater you must disable the heater first using M307, or if it is used for a fan you must disable the fan using M106 with the I-1 parameter. In RepRapFirmware 3 you specify the pin name instead.

RepRapFirmware 1.20 and later do not default to using the FAN0 output, so you must send M571 with a P parameter at least once to define the pin that you wish to use.

## M572: Set or report extruder pressure advance

**Parameters**

* **Dnnn** Extruder number(s)
* **Snnn** Pressure advance amount (in seconds) to use for that extruder or extruders

**Examples**

```
M572 D0 S0.1 ; set extruder 0 pressure advance to 0.1 seconds
M572 D0:1:2 S0.2 ; set extruder 0, 1 and 2 pressure advance to 0.2 seconds (RepRapFirmware 1.20 and later)
```

This sets the pressure advance coefficient (S parameter) for the specified extruder (D parameter). Only one S value is allowed. If you wish to set different pressure advance for different extruders, use multiple M572 commands.

Pressure advance causes the extruder drive position to be advanced or retarded during printing moves by an additional amount proportional to the rate of extrusion. At the end of a move when the extrusion rate is decreasing, this may result in the extruder drive moving backwards (i.e. retracting). Therefore, if you enable this feature, you may need to reduce the amount of retraction you use in your slicing program to avoid over-retraction.

If you configure Input Shaping, you will need to retune your Pressure Advance. It is recommend to first tune Input Shaping, then Pressure Advance, then Retraction.

For more details such as tuning the value see [Pressure advance](/User_manual/Tuning/Pressure_advance).

## M573: Report heater PWM

**Parameters**

* **Pnnn** Heater number

**Examples**

```
M573 P1
```

This gives a running average (usually taken over about five seconds) of the PWM to the heater specified by the P field. If you know the voltage of the supply and the resistance of the heater this allows you to work out the power going to the heater. Scale: 0 to 1.

## M574: Set endstop configuration

### {.tabset}

#### M574 - RepRapFirmware 3

**Parameters**

* **Xnnn** Position of X endstop: 0 = none, 1 = low end, 2 = high end.
* **Ynnn** Position of Y endstop: 0 = none, 1 = low end, 2 = high end.
* **Znnn** Position of Z endstop: 0 = none, 1 = low end, 2 = high end.
* **P"pin_name"** Defines the pin name(s) that the endstop(s) for the specified axis are connected to, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names). Needed when S=1. May need ! before pin name to invert signal, or ^ to enable the pullup resistor, for example on the Duex expansion board.
* **Snnn** 1 = switch-type (eg microswitch) endstop input, 2 = Z probe (when used to home an axis other than Z), 3 = single motor load detection, 4 = multiple motor load detection (see Notes).

**Order dependency**

This command must be later in config.g than the M584 command that creates additional axes, or axes that have multiple motors and endstops.

**Usage**

The M574 command has been updated to allow for more flexibility. This includes supporting axes defined with multiple motors and multiple endstops (one per motor), use of non-default endstop inputs, and re-assigning endstop inputs.

**Notes**

* Use a separate M574 command for each axis.
* Endstop type S0 (active low switch) is no longer supported in M574 commands. Instead, use type S1 and invert the input by prefixing the pin name with '!'. **Ex: M574 X1 S1 P"!xstop"**. Invert the input when using an NPN output inductive or capacitive sensor, or using a NO switch (not recommended, use a NC switch instead).
* New parameter P gives the pin name(s) for the endstop(s) for the specified axis. If the number of pins matches the number of motors assigned to that axis, motors will be stopped individually when their endstop switches trigger.
* New endstop type S4 means use motor stall detection (like S3) but if there are multiple motors, stop each one individually as it stalls. S3 means use motor stall detection but as in RRF 2: stop all relevant motors when the first one stalls.
* The S2 option of M574 is intended for use only when axes other than Z are using the Z probe for homing. The only printers known that do this using Duet electronics are the RepRapPro Ormerod, Huxley Duo, and Mendel Tricolour machines. When using the Z probe to home Z, M574 Z has no bearing on the probe setup or usage. A Z probe and a Z endstop can both be configured at the same time. G30 calling the probe setup with M558, and G1 H1 Z moves calling the endstop configured with M574 Z.

**Examples**

Old RRF2.x code:

```
M574 X1 Y1 Z1 U2 S1  ; active high endstop switches, XYZ at min, U at max
```

New RRF3 code:

```
M574 X1 S1 P"xstop"  ; X min active high endstop switch
M574 Y1 S1 P"ystop"  ; Y min active high endstop switch
M574 Z1 S1 P"zstop"  ; Z min active high endstop switch
M574 U2 S1 P"e0stop"  ; U max active high endstop switch
```

To use two Z motors using independent homing switches, declare two Z motors in M584, then declare two pins for Z endstops in a single M574 command. Example

```
M584 X0 Y1 Z2:3 E4
M574 Z1 S1 P"io2.in+io3.in" ; Z axis with two motors, individual min endstops, active high
```

The order of endstop switch pin names in M574 must match the order of Z motor driver numbers in M584. When homing Z, RRF3 homes the motors of the axis at the same time, independently to their defined endstops.

#### M574 - RepRapFirmware 2.x and earlier

**Parameters**

* **Xnnn** Position of X endstop: 0 = none, 1 = low end, 2 = high end.
* **Ynnn** Position of Y endstop: 0 = none, 1 = low end, 2 = high end.
* **Znnn** Position of Z endstop: 0 = none, 1 = low end, 2 = high end.
* **E** Select extruder endstops to define active high or low (RepRapFirmware 1.16 and earlier only)
* **Snnn** Endstop type: 0 = active low endstop input, 1 = active high endstop input, 2 = Z probe, 3 = motor load detection

**Order dependency**

If this command refers to any axes other than X, Y and Z then it must be later in config.g than the M584 command that creates those additional axes.

**Examples**

```
M574 X1 Y2 Z0 S1 ; X endstop at low end, Y endstop at high end, no Z endstop, all active high
```

**Usage**

This defines the type of endstop switch or opto sensor that the printer has for each axis: 0 = none, 1 = low end, 2 = high end. The optional S parameter defines whether the endstop input is active high (S1, the default) or low (S0). A normally-closed endstop switch wired in the usual way produces an active high output (S1).

The S2 and S3 options are supported in firmware 1.20 and later.

This is intended for use with boards that provide a single endstop input for each axis that may be used for either a high or a low end endstop, such as the Duet. On delta printers, the XYZ parameters refer to the towers and the endstops should normally all be high end (i.e. at the top of the towers).

**Notes**

If you have more than one homing switch for an axis because you want to home multiple motors driving that axis individually, you will need to split them into separate axes during homing, and home those axes together. See [Axis levelling using endstops](/User_manual/Connecting_hardware/Z_probe_auto_levelling#axis-levelling-using-endstops).

In RepRapFirmware 1.16 and earlier, the M574 command with E parameter was used to specify whether a Z probe connected to the E0 endstop input produces an active high (S1) or active low (S0) output. In RepRapFirmware 1.17 and later, use the I parameter of the M558 command instead.


## M575: Set serial comms parameters

**Parameters**

* **Pnnn** Serial channel number
* **Bnnn** Baud rate, default 57600 (same as the default PanelDue baud rate)
* **Snnn** Mode: 0 = PanelDue mode without checksums; 1 (default) = PanelDue mode, checksum required; 2= raw mode, 3 = raw mode with checksums. Modes 2 and 3 are only supported in RRF 3.01-RC11 and later.

**Examples**

```
M575 P1 B57600 S1
```

This sets the communications parameters of the serial comms channel specified by the P parameter. P0 specifies the main serial interface (typically a USB port, or serial-over-USB), while P1 specifies an auxiliary serial port (for example, the port used to connect a PanelDue) and P2 specifies a second auxiliary port if there is one. The B parameter is the required baud rate (this parameter is typically ignored if the port is a true USB port).

In RRF 3.1 and later for Duet 3, the auxiliary serial port remains disabled until a M575 P1 command is received. This is to allow the IO_0 port to be used for other purposes. In RRF 3.2 and later on all boards, the auxilliary serial port(s) remain disabled until enabled using M575.

## M576: Set SPI comms parameters

**Parameters**

* **Snnn** Maximum delay between full SPI transfers (in ms, defaults to 25ms)
* **Fnnn** Maximum delay between full SPI transfers when a file is open (in ms, defaults to 5ms)
* **Pnnn** Number of events required to skip the delay (defaults to 4)

This sets the communications parameters of the SPI channel. Supported in RRF 3.4 and later in SBC mode.

## M577: Wait until endstop is triggered

### {.tabset}

#### M577 - RepRapFirmware 3.01RC2 and later

**Parameters**

* **Sn** Desired endstop or input level: 1 = triggered/active (default), 0 =not triggered/inactive
* **X, Y, Z, U, V, W, A, B, C, D** Axis endstop to wait for
* **Pnnn** Input pin number to wait for (see M950 with J parameter)

**Examples**

```
M577 X S0  ; wait until X endstop is not triggered
M577 P2 ; wait
```

Wait for an endstop switch to be triggered or an input to become active.

#### M577 - RepRapFirmware 3.0 up to 3.01RC1

**Parameters**

* **P"nnn"** Specifies one or more pin names, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names)

**Examples**

```
M577 P"!xstop+!e0stop"
```

In RRF3, use the P parameter to specify one or more pin names. The pin(s) do not need to be exclusively used by M577; for example, it is permitted to specify the name of a pin that has already been declared as used by an endstop switch in a M574 command.

The command waits for a high input level. To wait for a low input level, invert the pin name by prefixing "!".

Example - old code (RRF2.x):

```
M577 X E0 S0 ; wait until the X and E0 endstop inputs are both low
```

New code (RRF3):

```
M577 P"!xstop+!e0stop" ; wait until the X and E0 endstop inputs are both low
```

#### M577 - RepRapFirmware 2.x and earlier

**Parameters**

* **Snnn** Desired endstop level
* **Xnnn** Select X axis endstop
* **Ynnn** Select Y axis endstop
* **Znnn** Select Z axis endstop
* **Ennn** Select extruder drive endstop

**Examples**

```
M577 E0 S1
```

Wait for an endstop switch to be pressed. The example above will wait until the first extruder endstop is triggered.

The following trigger types may be used using the 'S' parameter:

0: Endstop not hit
1: Low endstop hit
2: High endstop hit
3: Near endstop (only Z probe)

## M578: Fire inkjet bits

**This command is not enabled unless the SUPPORT_INKJET feature is enabled when the firmware is built.**

**Parameters**

* **Pnnn** Inkjet head number
* **Snnn** Bit pattern

**Examples**

```
M578 P3 S5
```

This fires inkjet head 3 (the P field) using the bit pattern specified by the S field. The example shown would fire bits 101. If the P parameter is ommitted inkjet 0 is assumed.

This is a version of the M700 command used by [Inkshield](http://reprap.org/wiki/Inkshield).

An alternative way of controlling inkjets would be to use the P parameter on the G1 command, in conjunction with the M670 command.

## M579: Scale Cartesian axes

**Parameters**

* **Xnnn** Scale factor for X axis
* **Ynnn** Scale factor for Y axis
* **Znnn** Scale factor for Z axis
* **U, V, W, A, B, C** Scale factors for additional axes

**Examples**

```
M579 X1.0127 Y0.998
```

On a Cartesian RepRap you can get prints exactly the right size by tweaking the axis steps/mm using the M92 G Code above. But this does not work so easily for Delta and other RepRaps for which there is cross-talk between the axes. This command allows you to adjust the X, Y, and Z axis scales directly. So, if you print a part for which the Y length should be 100mm and measure it and find that it is 100.3mm long then you set Y0.997 (= 100/100.3).

On a suitable-configured IDEX printer, a scaling factor of -1 for the U axis can be used to turn a ditto print into a mirror image print.

## M580: Select Roland

* This M-code is not available by default. To enable it change the value of SUPPORT_ROLAND in the Pins_*.h file from 0 to 1 and recompile the firmware

**Parameters**

* **Rnnn** Whether Roland mode should be activated
* **Pnnn** Initial text to send to the Roland controller

**Examples**

```
M580 R1 PVS4;!VZ2;!MC1;
```

The [Modela MDX-20](https://www.rolanddga.com/support/products/milling/modela-mdx-20-3d-milling-machine.htm) and similar milling machines are very widely available in hackerspaces and maker groups, but annoyingly they don't speak G Codes. As all RepRap firmware includes a GCode interpreter, it is often easy to add functions to convert G Codes to RML. M580 selects a Roland device for output if the R field is 1, and returns to native mode if the R field is 0. The optional P string is sent to the Roland if R is 1. It is permissible to call this repeatedly with R set to 1 and different strings in the P field to communicate directly with a Roland.

## M581: Configure external trigger

#### M581 - RepRapFirmware 2.x and earlier

**Parameters**

* **Tnn** Logical trigger number to associate the endstop input(s) with, from zero up to a firmware-specific maximum (e.g. 9 for RepRapFirmware)
* **X, Y, Z, E** Selects endstop input(s) to monitor
* **P** Reserved, may be used in future to allow general I/O pins to cause triggers
* **S** Whether trigger occurs on a rising edge of that input (S1, default), falling edge (S0), or ignores that input (S-1). By default, all triggers ignore all inputs.
* **C** Condition: whether to trigger at any time (C0, default) or only when printing a file from SD card (C1)

**Examples**

```
M581 E1:2 S1 T2 C1 ; invoke trigger 2 when a rising edge is detected on the E1 or E2 endstop input and a file is being printed from SD card
```

**Notes**

* When M581 is executed, if the T parameter is present but the other parameters are omitted, the trigger inputs and edge polarities for that trigger number are reported. Otherwise, the specified inputs and their polarities are added to the conditions that cause that trigger. Using S-1 with no X Y Z or E parameters sets the trigger back to ignoring all inputs.
* Trigger number 0 causes an emergency stop as if M112 had been received. Trigger number 1 causes the print to be paused as if M25 had been received. Any trigger number # greater than 1 causes the macro file sys/trigger#.g to be executed. Polling for further trigger conditions is suspended until the trigger macro file has been completed. RepRapFirmware does not wait for all queued moves to be completed before executing the macro, so you may wish to use the M400 command at the start of your macro file. If several triggers are pending, the one with the lowest trigger number takes priority.
* A maximum of 16 triggers can be configured on Duet 2.
* Warning: if executed during a build process, and more than one line long the GCode within the trigger file may be executed between later commands from the build file. Bounding the trigger file with M25 and M24 may help, but this will cause error warnings if the trigger happens outside of a build process. The use of M25/M24 will cause the execution of pause and resume system macros.

#### M581 - RepRapFirmware 3.0 to 3.01RC1 (but not 3.01RC2 and later)

**Parameters**

* **P** Specify one or more pin names, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names)
* **Tnn** Logical trigger number to associate the endstop input(s) with, from zero up to a firmware-specific maximum
* **C** Condition: whether to trigger at any time (C0, default) or only when printing a file from SD card (C1)

**Examples**

```
M581 P"e0stop+e1stop" T2 C1 ; invoke trigger 2 when a rising edge is detected on the E1 or E2 endstop input and a file is being printed from SD card
M581 P"nil" T2 ; don't invoke trigger 2 on any input change any more
```

**Notes**

* Use the P parameter to specify one or more pin names. Use P"nil" to disable that trigger number.
* The pin(s) do not need to be exclusively used by M581; for example, it is permitted to specify the name of a pin that has already been declared as used by an endstop switch in a M574 command.
* The S parameter used in RRF2.x is removed. The command waits for a low-to-high input transition. To wait for a high-to-low transition, invert the pin name using '!'.
* When M581 is executed, if the T parameter is present but the other parameters are omitted, the trigger inputs and edge polarities for that trigger number are reported. Otherwise, the specified inputs and their polarities are added to the conditions that cause that trigger.
* Trigger number 0 causes an emergency stop as if M112 had been received. Trigger number 1 causes the print to be paused as if M25 had been received. Any trigger number # greater than 1 causes the macro file sys/trigger#.g to be executed. Polling for further trigger conditions is suspended until the trigger macro file has been completed. RepRapFirmware does not wait for all queued moves to be completed before executing the macro, so you may wish to use the M400 command at the start of your macro file. If several triggers are pending, the one with the lowest trigger number takes priority.
* Warning: if executed during a build process, and more than one line long the GCode within the trigger file may be executed between later commands from the build file. Bounding the trigger file with M25 and M24 may help, but this will cause error warnings if the trigger happens outside of a build process. The use of M25/M24 will cause the execution of pause and resume system macros.

#### M581 - RepRapFirmware 3.01 and later

**Parameters**

* **P** Specify one or more input pin numbers that you created using M950 with the J parameter, or -1 to delete the trigger
* **Tnn** Logical trigger number to associate the input(s) with, from zero up to a firmware-specific maximum
* **S** Whether trigger occurs on an inactive-to-active edge of that input (S1, default), active-to-inactive edge (S0), or ignores that input (S-1). By default, all triggers ignore all inputs.
* **R** Condition: whether to trigger at any time (R0, default), only when printing a file from SD card (R1), or only when not printing a file from SD card (R2, supported in RRF 3.2 and later). R-1 temporarily disables the trigger.
* **X**, **Y**, **Z** or any other axis letter: axis or axes whose endstop switches are to cause the trigger

**Examples**

```
M581 T2 P0:3 S1 R1 ; invoke trigger 2 when an inactive-to-active edge is detected on input 0 or input 3 and a file is being printed from SD card
M581 T3 X Y S1  ; invoke trigger 3 when the X or Y endstop switch is triggered
M581 T2 P-1 ; don't invoke trigger 2 on any input change any more
```

**Notes**

* When M581 is executed, if the T parameter is present but the other parameters are omitted, the trigger inputs and edge polarities for that trigger number are reported. Otherwise, the specified inputs and their polarities are added to the conditions that cause that trigger.
* Trigger number 0 causes an emergency stop as if M112 had been received. Trigger number 1 causes the print to be paused as if M25 had been received. Any trigger number # greater than 1 causes the macro file sys/trigger#.g to be executed. Polling for further trigger conditions is suspended until the trigger macro file has been completed. RepRapFirmware does not wait for all queued moves to be completed before executing the macro, so you may wish to use the M400 command at the start of your macro file. If several triggers are pending, the one with the lowest trigger number takes priority.

* A maximum of 32 triggers can be configured on Duet 3, a maximum of 16 on Duet 2.

* Warning: if executed during a job, and more than one line long the GCode within the trigger file may be executed between later commands from the job. Bounding the trigger file with M25 and M24 may help, but this will cause warnings if the trigger happens outside of a job. The use of M25/M24 will cause the execution of pause and resume system macros.

## M582: Check external trigger

**Parameters**

* T Trigger number to poll

**Examples**

```
M582 T2 ; check levels of inputs that give rise to trigger #2
```

Triggers set up by the M581 command are normally activated only when the specified inputs change state. This command provides a way of causing the trigger to be executed if the input is at a certain level. For each of the inputs associated with the trigger, the trigger condition will be checked as if the input had just changed from the opposite state to the current state.

For example, if you use M581 to support an out-of-filament sensor, then M582 allows you to check for out-of-filament just before starting a print.

## M584: Set drive mapping

**Parameters**

* **Xnnn** Driver number(s) for X motor(s)
* **Ynnn** Driver number(s) for Y motor(s)
* **Znnn** Driver number(s) for Z motor(s)
* **Rn** (optional, supported in RRF 3.2beta1 and later) 0 = axes creates in this command are linear, 1 = axes created are rotational. If not present, then RRF 3.2beta3 and later assume UVW are linear and ABCD are rotational.
* **Sn** (optional, supported in RRF 3.2beta3 and later) 0 = axes created in this command are treated as linear in feedrate calculations, 1 = axes created are treated as rotational in feedrate calculations. See section 2.1.2.5 of the NIST GCode standard for how the feedrate is interpreted. Default is S0 for linear axes and S1 for rotational axes (see the R parameter).
* **U, V, W, A, B, Cnnn** Driver number(s) for additional axes U, V, W, A, B and C (UVW available in RepRapFirmware 1.16 and later; UVWABC available in RepRapFirmware 1.19 and later; UVWABCD available in RepRapFirmware 3.0 and later).
* **Ennn** Driver number(s) for E motor(s)
* **Pnnn** Number of visible axes, defaults to the total number of axes configured.

**Order dependency**

* M584 must come earlier in config.g than any M350 and M906 commands. If it creates new axes, it must also be earlier than any M92, M201, M203, M208, M350, M566, M574, M667 and M669 commands.

**Examples**

```
M584 X0 Y1 Z2:3 E4:5:6 ; Driver 0 controls the X motor, 1 controls Y, 2 and 3 control Z motors, 4 5 and 6 control E motors
```

**Notes**

* **VERY IMPORTANT!** Assigning a drive using M584 does not remove its old assignment. Therefore, **if you assign a drive that defaults to being an extruder drive, you should also assign the extruder drives explicitly as in the above example**. Failure to do so may result in unexpected behaviour.
* You can use M584 to create additional axes - for example, to represent additional carriages on a machine with multiple independent X carriages. In 1.20 and later firmware you can create new axes in any order. In earlier firmware versions, additional axes must be created in the order UVWABC. You can hide some of the last axes you create using the P parameter. Hidden axes have no homing buttons or jog controls in the user interface.
* RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour. Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M92 Z80, not M92 Z80:80
* If you create more than one axis in a M584 command, the axes are created in the order UVWABCD regardless of the order of the parameters in the M584 command. This affects which axes will be hidden if you use the M584 P parameter to hide axes. For example, M584 C5 U6 creates axes U and C in that order, so M584 P4 would hide the C axis, not the U axis. If you want to create the axes in the order C then U (so that M584 P4 hides the U axis), use two M584 commands: M584 C5 followed by M584 U6.
* Using M584 to map drivers to axes does not affect endstop inputs. In RRF2, endstop inputs XYZ are pre-allocated, after that they are allocated in the order in which axes are created. So if you create just one extra axes (e.g. U), it will use the E0 endstop input. If more than one axis is created in a single M584 command, endstop inputs are allocated to the new axes in axis creation order (see previous item). For example, M584 C5 U6 would allocate endstop input E0 to the U axis and E1 to the C axis.
* On the Duet 2 WiFi and Duet 2 Ethernet, if you configure multiple drivers for an axis, either all of them must be TMC2660 drivers on the Duet or a Duet expansion board, or none of them must be. This is to facilitate dynamic microstepping and other features of the TMC2660.
* In RRF3, M584 works the same way as in RRF2, with exception that on Duet 3 the driver on expansion boards are assigned with \<board address>.\<driver number>. Example:

```
M584 X0 Y1 Z2 E3:4:1.0:1.1
```

The "0" index for the main board is implicit, this is equivalent to the previous example:

```
M584 X0.0 Y0.1 Z0.2 E0.3:0.4:1.0:1.1
```

* If you assign an axis or extruder to one or more drivers, and later you want to reassign it to a different driver, then to ensure correct operation you must disable those axes/extruders using M18 before using M584 to reassign them. After using M584, you must set the motor current using M906 and the microstepping using M350. Example:

```
; Here axis B and extruder E0 have already been assigned and possibly moved, but we now want to reassign them
M18 B E0      ; disable the axes/extruder that we are going to reassign
M584 B3 E4     ; reassign them
M906 B1000 E1000  ; set the motor currents
M350 B16 E16 I1  ; set the microstepping
```

## M585: Probe Tool

Supported in RRF 1.20 and later.

In machines with at least one tool probe this code allows to update the current tool's offset by driving it into a given endstop.

This code works similarly to G1 .. H1 (machine homing; G1 .. S1 is RRF 2.02 and earlier) except that it sets the offset of the current tool instead of the machine position, and that a custom endstop number (RRF 2.x) or custom Z probe (RRF 3.x) can be used.

**Parameters**

* **Xnnn** - Probe tool in X direction where nnn specifies the expected distance between the trigger point of your endstop switch and the starting point
* **Ynnn** - Probe tool in Y direction where nnn specifies the expected distance between the trigger point of your endstop switch and the starting point
* **Znnn** - Probe tool in Z direction where nnn specifies the expected distance between the trigger point of your endstop switch and the starting point
* **U,V,W,A,B,Cnnn** - As for X,Y,Z above
* **Ennn** - [Not supported in RRF 3] Custom endstop number to use (optional). This must be the drive number of the according endstop (i.e. X=0, Y=1, Z=2, E0=3 etc.)
* **Lnnn** - [RRF 2.04 and later, not supported in RepRapFirmware 3] Trigger level of the custom endstop (optional, 0 = active-low, 1 = active-high). This requires the 'E' parameter to be present
* **Pn** - [RRF 3 only] Z probe number to use (optional)
* **Fnnn** - Requested feedrate of the probing move. If this parameter is omitted, the last set feedrate is used
* **Snnn** - Direction of the probing move. S=0 (default) means travel forwards (towards the axis maximum), S=1 means go backwards (towards the axis minimum)
* **Rnnn** - Probing radius, i.e. the relative movement amount from the current position (optional, if used the S parameter is ignored) [requires RRF 1.20beta8 or later]

**Notes**

* You can only specify one axis per M585 call and that XYZ are not the only possible axes for this code (UVWABC would be valid as well).
* The values of the XYZ parameters are the absolute distances between the position at which the endstop is actually triggered and your own start position. It is mandatory to measure this distance once before M585 can be used reliably. An example: Say you wish to probe the tool offset on the X axis. If the trigger position of your endstop is at X=210 and you want to drive your tool from X=190 into the endstop switch, you need to specify -20 as your X parameter because you expect to travel 20mm towards the endstop switch and need to correct this factor. If you drive the tool backwards (e.g. from X=210 to X=190), the correction factor should be 20.
* In case you are using different switches for tool probing, RepRapFirmware allows you to use a custom endstop. If a different endstop than the axis endstop is used, the drive number of the matching endstop can be specified via the optional E parameter (e.g. E4 for the E1 endstop).
* In principle the following workflow should be performed for each axis using a macro file. You may wish to enhance this workflow depending on your own requirements and endstop configuration.

* Reset the axis tool offset (G10/M568 Pxx X0 Y0 Z0)
  * Select your tool (Txx)
  * Move the tool to your starting position (G1 X?? Y?? Z?? F3000)
  * Drive the tool into the endstop, stop there and apply the new tool offset with the given correction factor (M585 XYZ?? F1000 E??)
  * Call G10/M568 Pxx with your tool number to get the corrected tool offset or call M500 (supported in RRF 1.20beta3 and later) to store the probed tool offsets on the SD card

**Notes - RepRapFirmware 3**

* You can use M585 to probe until a regular axis endstop is triggered as before.
* The E and L parameters are removed, instead there is a new P parameter that specifies a probe number. If you want to probe until a custom input is triggered, use M558 to configure an additional probe that uses that pin, then refer to that probe in your M585 command.

Example - old code:

```
M585 X100 F600 E3 L0 S0 ; probe X until E0 endstop goes low
```

New code:

```
M558 K1 P5 C"!e0stop" ; create probe #1 for use with M585, active low
...
M585 X100 F600 P1 S0 ; probe X until probe #1 is triggered
```

## M586: Configure network protocols

**Parameters**

* **Pnn** Protocol: 0 = HTTP or HTTPS, 1 = FTP or SFTP, 2 = Telnet or SSH (which of the two choices depends on the T parameter)
* **Snn** 0 = disable this protocol, 1 = enable this protocol
* **Rnn** TCP port number to use for the specified protocol. Ignored unless S = 1. If this parameter is not provided then the default port for that protocol and TLS setting is used.
* **Tnn** 0 = don't use TLS, 1 = use TLS. Ignored unless S = 1. If this parameter is not provided, then TLS will be used if the firmware supports it and a security certificate has been configured. If T1 is given but the firmware does not support TLS or no certificate is available, then the protocol will not be enabled and an error message will be returned.
* **C"\<site>"** Set or reset allowed site for cross-orgin HTTP requests (RRF > 3.2-b4.1)
* **Note**: TLS has not yet been implemented in RepRapFirmware, therefore T1 will not work.

M586 with no S parameter reports the current support for the available protocols.

RepRapFirmware 1.18 and later enable only HTTP (or HTTPS if supported) protocol by default. If you wish to enable FTP and/or Telnet, enable them using this command once or twice in config.g.

## M587: Add WiFi host network to remembered list, or list remembered networks

**This command must not be used in the config.g file. In SBC mode (v3.3 and later) it is not possible to configure different IP addresses per SSID**

**Parameters**

* **S"ccc"** Network SSID (case sensitive)
* **P"ccc"** Network password (case sensitive)
* **Inn.nn.nn.nn** (optional) IP address to use when connected to this network. If zero or not specified then an IP address will be acquired via DHCP.
* **Jnn.nn.nn.nn** (optional) Gateway IP address to use when connected to this network.
* **Knn.nn.nn.nn** (optional) Netmask to use when connected to this network
* **Lnn.nn.nn.nn** (optional, supported only by DuetPi + DSF v3.3 or newer) DNS server to use
* **Cnnn** (supported only by DuetPi + DSF v3.3 or newer) Country code for the WiFi adapter, only required if not set before

The SSID and password must always be enclosed in double quotation marks.

**Notes**

Many programs used to send GCodes convert all characters to uppercase. In firmware 1.19.2 and later, within any quoted string you can use a single-quote character to indicate that the following character should be changed to lowercase. For example, M587 S"ABC" P"P'A'S'SW'O'R'D" would specify that the password is "PassWord". Use two single quote characters to represent one actual single quote character in the password or in the SSID. For example, if your SSID is "Pete's network" then enter "Pete*s network".

The use of special characters in the SSID cannot be guaranteed to work. In general it's best to avoid most special characters. Spaces, periods, dashes, underscores, and other punctuation is likely ok, but special characters on the number keys likely are not safe. (@#$%^&*). If you are having troubles adding your SSID, try a simplified version with only letters and numbers.

M587 with no parameters lists all the remembered SSIDs, but not the remembered passwords.

The M587 command will fail if the WiFi module has not yet been taken out of reset. So if the WiFi module has not been started, send M552 S0 to put it in idle mode first.

**Important!** Do not use M587 within config.g. As well as being a security hazard, writing the access point parameters to WiFi chip every time you start the Duet may eventually wear out the flash memory. Also, the wifi module does not get enabled until the end of running config.g (see [this forum thread](https://forum.duet3d.com/post/42798) for explanation). It is better to use a macro to send M587.

## M588: Forget WiFi host network

**This command must not be used in the config.g file**

**Parameters**

* **S"ccc"** Network SSID to remove from the remembered list

The specified SSID will be removed from the remembered list and the associated password cleared out of EEPROM. If the SSID is given as "*" then all remembered networks will be forgotten.

**Example:** M588 S"*"

The M588 command will fail if the WiFi module has not yet been taken out of reset. So if the WiFi module has not been started, send M552 S0 to put it in idle mode first. M588 does not work from within config.g at startup.

## M589: Configure access point parameters

**This command must not be used in the config,g file**

**Parameters**

* **S"ccc"** The SSID that the WiFi interface should use when it is commanded to run as an access point
* **P"ccc"** The WiFi password
* **Inn.nn.nn.nn** The IP address to use
* **Cnn** The WiFi channel to use (optional)

**Example command: M589 S"DuetSSID" P"password" I192.168.0.1 C1**

**To use AP mode:**

* Send a M589 command once from the console, or via macro to set the access point name, IP address etc. These parameters will be saved within the WiFi module.
* The M589 command will fail if the WiFi module has not yet been taken out of reset. So if the WiFi module has not been started, send M552 S0 to put it in idle mode first.
* M589 does not work from within config.g at startup.
* Use M552 S2 in config.g to start the wifi module.
* WPA2 security will be used by default.
* Look for the wireless network name you specified on your device and connect to it using the password you set.

## M591: Configure filament sensing

This configures filament sensing for the specified extruder. The sensor may be a simple filament presence detector, or a device that measures movement of filament, or both. The action on a filament error is to run filament-error#.g (RRF 3.2 and later, where # is the extruder number), failing that run filament-error.g (RRF 3.2 and later), or failing that run pause.g (RRF 1.19 and later). After that the print is paused and a notification is sent advise you that there has been a filament error. Note that filament monitoring in RRF is only active when printing from SD card.

#### M591 - RepRapFirmware 3

**Parameters**

* **Dnn** Extruder drive number (0, 1, 2...)
* **Pnn** Type of sensor:
  * 0=none
  * 1=simple sensor (high signal when filament present)
  * 2=simple sensor (low signal when filament present)
  * 3=Duet3D rotating magnet sensor
  * 4=Duet3D rotating magnet sensor with microswitch
  * 5 = Duet3D laser sensor
  * 6 = Duet3D laser sensor with microswitch
  * 7 = pulse-generating sensor
* **C"name"** Pin name the filament sensor is connected to (RRF3 only), see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names)
* **Sn** 0 = disable filament monitoring (default), 1 = enable filament monitoring when printing from SD card. Supported for all filament sensor types in firmwares 1.21.1 and in 2.0 and later. In firmware 1.21 this parameter is not supported for sensor types 1 and 2. Filament monitors accumulate calibration data (where applicable) even when filament monitoring is disabled.

**Additional parameters for Duet3D laser filament monitor**

* **Raa:bb** Allow the filament movement reported by the sensor to be between aa% and bb% of the commanded values; if it is outside these values and filament monitoring is enabled, the print will be paused
* **Enn** minimum extrusion length before a commanded/measured comparison is done, default 3mm
* **An** (firmware 2.03 and later) 1 = check All extruder motion, 0 (default) = only check extruder motion of printing moves (moves with both movement and forward extrusion)
* **Lnn** (firmware 3.2 and later) Calibration factor, default 1.0. The filament movement reported by the laser sensor is multiplied by this value before being compared with the commanded extrusion. Intended for use with sensors that use the laser to read movement of a wheel that is turned by the filament.

**Additional parameters for Duet3D rotating magnet filament monitor**

* **Lnn** Filament movement per complete rotation of the sense wheel, in mm
* **R, E, A** As for Duet3D laser filament monitor

**Additional parameters for a pulse generating filament monitor**

* **Lnn** Filament movement per pulse in mm
* **R, E** As for Duet3D laser filament monitor

**Examples**

```
M591 P3 C"e0stop" S1 D0 ; filament monitor connected to E0 endstop
```

#### M591 - RepRapFirmware 1.21 to 2.x

**Parameters**

As RRF3, except 'C' parameter is the endstop number.

* **Cnn** Which input the filament sensor is connected to. On Duet electronics: 0=X endstop input, 1=Y endstop input, 2=Z endstop input, 3=E0 endstop input etc. If you have a Duex 2 or Duex 5 in your system, note that C5 thru C9 (the endstop inputs on the DueX) cannot be used for filament monitors, but C10 and C11 (the endstop inputs on the CONN_LCD connector) can.

**Examples**

```
M591 D0 P3 C3 S1 R70:130 L24.8 E3.0  ; Duet3D rotating magnet sensor for extruder drive 0 is connected to E0 endstop input, enabled, sensitivity 24.8mm.rev, 70% to 130% tolerance, 3mm detection length
M591 D0   ; display filament sensor parameters for extruder drive 0
```

#### M591 - RepRapFirmware 1.20 and earlier

**Parameters**

* **Dnn** Extruder drive number (0, 1, 2...)
* **Pnn** Type of sensor, where:
  * 0=none
  * 1=simple sensor (high signal when filament present)
  * 2=simple sensor (low signal when filament present)
  * 3=Duet3D rotating magnet sensor
  * 4=Duet3D rotating magnet sensor with microswitch
* **Cn** Which input the filament sensor is connected to. On Duet electronics: 0=X endstop input, 1=Y endstop input, 2=Z endstop input, 3=E0 endstop input etc.

**Additional parameters for Duet3D rotating magnet filament monitor**

* **Snn** Sensitivity, in mm of filament movement per complete rotation of the sense wheel.
* **Rnn** Tolerance as a percentage of the commanded extrusion amount. A negative value puts the firmware in calibration mode.
* **Enn** minimum extrusion length before a commanded/measured comparison is done, default 3mm

**Examples**

```
M591 D0 P5 C3 R70:140 E3.0 S1  ; Duet3D rotating magnet sensor for extruder drive 0 is connected to E0 endstop input, sensitivity 1.05, tolerance 70% to 140%, 3mm detection length
M591 D1 ; display filament sensor parameters for extruder drive 1
```

**Documentation**

* [Duet3d Rotating Magnet Filament Monitor](/Duet3D_hardware/Accessories/Rotating_Magnet_Filament_Monitor)
* [Duet3d Laser Filament Monitor](/Duet3D_hardware/Accessories/Laser_Filament_Monitor)
* [Connecting and configuring filament-out sensors](/User_manual/Connecting_hardware/Sensors_filament)

## M592: Configure nonlinear extrusion

**Parameters**

* **Dnn** Extruder drive number (0, 1, 2...)
* **A**nnn A coefficient in the extrusion formula, default zero
* **B**nnn B coefficient in the extrusion formula, default zero
* **L**nnn Upper limit of the nonlinear extrusion compensation, default 0.2
* **T** nnn Reserved for future use, for the temperature at which these values are valid

**Examples**

```
M592 D0 A0.01 B0.0005 ; set parameters for extruder drive 0

M592 D0 ; report parameters for drive 0
```

Most extruder drives use toothed shafts to grip the filament and drive it through the hot end. As the extrusion speed increases, so does the back pressure from the hot end, and the increased back pressure causes the amount of filament extruded per step taken by the extruder stepper motor to reduce. This may be because at high back pressures, each tooth compresses and skates over the surface of the filament for longer before it manages to bite. See [RepRap forum post here](http://forums.reprap.org/read.php?262,802277) and the [graph here](http://forums.reprap.org/file.php?262,file=100851,filename=graph.JPG) for an example.

Nonlinear extrusion is an experimental feature in RepRapFirmware to compensate for this effect. The amount of extrusion requested is multiplied by (1 + min(L, A*v + B*v^2)) where v is the requested extrusion speed (calculated from the actual speed at which the move will take place) in mm/sec.

Nonlinear extrusion is not applied to extruder-only movements such as retractions and filament loading.

## M593: Configure Input Shaping

**Parameters (RRF 3.2 and earlier)**

* **Fnnn** Frequency of ringing to cancel by DAA, in Hz. Zero or negative values disable DAA.
* **Lnnn** Minimum acceleration allowed, default 10mm/sec^2. DAA will not be applied if it requires the average acceleration to be reduced below this value.

**Parameters (RRF 3.3 and later)**

* **P"type"** Type of input shaping to use, not case sensitive. In RRF 3.3, "type" must be "none" or "daa", and if no P parameter is given but the F parameter is given then "daa" is assumed, for compatibility with previous releases. RRF 3.4 instead supports "none", "zvd", "zvdd", "zvddd", "mzv", "ei2", "ei3" and "custom".
* **Fnnn** Frequency of ringing to cancel in Hz
* **Snnn** (optional) Damping factor of ringing to be cancelled, default 0.1.
* **Lnnn** (optional) Minimum acceleration allowed, default 10mm/sec^2. Input shaping will not be applied if it requires the average acceleration to be reduced below this value.
* **Hnn:nn...** Amplitudes of each impulse except the last, normally below 1.0. Only used with P"custom" parameter.
* **Tnn:nn** Durations of each impulse except the last. Only used with P"custom" parameter.

**Information about the Input Shapers**


|width=75% | Input Shaper | Shaper Duration | Vibration reduction with default damping factor (0.1) |
|:---|:---|
| ZVD | 1 / Frequency | ± 15% Frequency |
| ZVDD | 1.5 / Frequency | |
| ZVDDD | 2 / Frequency | |
| MZV | 1 / Frequency | ± 4% Frequency |
| EI2 | 1.5 / Frequency | ± 35% Frequency |
| EI3 | 3 / Frequency | ± -45%...+50% Frequency |

**Example (RRF 3.2 and earlier)**

```
M593 F40.5  ; use DAA to cancel ringing at 40.5Hz
```

**Example (RRF 3.3)**

```
M593 P"daa" F40.5  ; use DAA to cancel ringing at 40.5Hz
M593 P"none"   ; disable DAA
```

**Examples (RRF 3.4 and later)**

```
M593 P"zvd" F40.5  ; use ZVD input shaping to cancel ringing at 40.5Hz
M593 P"none"     ; disable input shaping
M593 P"custom" H0.4:0.7 T0.0135:0.0135 ; use custom input shaping
```

The purpose of input shaping is to reduce ringing (also called ghosting).

In firmware 2.02 up to 3.3 the only form of input shaping supported is Dynamic Acceleration Adjustment (DAA). By default, DAA is disabled. If it is enabled, then acceleration and deceleration rates will be adjusted per-move to reduce ringing at the specified frequency. Acceleration limits set by M201 and M204 will still be honoured when DAA is enabled, so DAA will only ever reduce acceleration. Therefore your M201 and M204 limits must be high enough so that DAA can reduce the acceleration to the optimum value. Where possible DAA reduces the acceleration or deceleration so that the time for that phase is the period of the ringing. If that is not possible because of the acceleration limits, it tries for 2 times the period of the ringing.

Input shaping is most useful to avoid exciting low-frequency ringing, for which S-curve acceleration is ineffective and may make the ringing worse. High-frequency ringing would be better countered by using S-curve acceleration; however, low-frequency ringing is more of a problem in most 3D printers.

The ringing frequencies are best measured using an accelerometer, for which support is provided in RRF 3.3 and later. Alternatively, take a print that exhibits ringing on the perimeters (for example a cube), preferably printed single-wall or external-perimeters-first. Divide the speed at which the outer perimeter was printed (in mm/sec) by the distance between adjacent ringing peaks (in mm). When measuring the distance between peaks, ignore peaks close to the corner where the ringing started (these peaks will be spaced more closely because the print head will have been accelerating in that area).

Cartesian and CoreXY printers will typically have different frequencies of ringing for the X and Y axes. In this case it is is usually best to aim to cancel the lower ringing frequency. If the frequencies are not much different, in a moving-bed Cartesian printer you can reduce the higher ringing frequency by adding mass to that axis or reducing belt tension on that axis. Note that X axis ringing causes artefacts predominantly on the Y face of the test cube, and vice versa.

High X and Y jerk values reduce the effectiveness of DAA; therefore you should set the X and Y jerk limits only as high as necessary to allow curves to be printed smoothly.

Keep in mind that you have to retune Pressure Advance after you have configured Input Shaping. The Pressure Advance will differ from shaper to shaper and from frequency to frequency.

## M594: Enter/Leave Height Following mode

**Parameters**

* **Pn** P1 = enter height following mode, P0 = leave height following mode

Height following mode allows the Z position of the tool to be controlled by a PID controller using feedback from a sensor. See also M951.

If a movement command (e.g. G1) explicitly mentions the Z axis while height following mode is active, existing moves in the pipeline will be allowed to complete and the machine allowed to come to a standstill. Then height following mode will be terminated and the new move executed.

## M595: Set movement queue length

Supported in RRF 3.2 and later.

**Parameters**

* **Pnn** Maximum number of moves held in the movement queue. RepRapFirmware uses this value to determine how many DDA objects to allocate.
* **Snn** (optional) Number of pre-allocated per-motor movement objects. If the number of pre-allocated objects is insufficient, RepRapFirmware will attempt to allocate additional omnes dynamically when they are needed.
* **Rnnn** Grace period in milliseconds (supported in RRF 3.3 and later). When filling the movement queue from empty, the system waits for this amount of time after the last movement command was received before starting movement. This is to allow the movement queue to fill more before movement is started when commands are received from USB, Telnet or another serial channel. It should not be needed when processing a GCode file from the SD card.
* **Qn** (optional, RRF3.3 and later) Movement queue number, default 0. Some builds of RRF have a secondary movement queue. You can configure the length of that queue by specifying Q1.

Different features of motion control firmware may have competing demands on microcontroller RAM. In particular, operations that use many short segments (e.g. laser rastering) need longer movement queues than typical 3D printing, but have fewer motors to control. This command allows the movement queue parameters to be adjusted so that the queue can be lengthened if necessary, or kept short if a long movement queue is not needed and there are other demands on RAM.

M595 without any parameters reports the length of the movement queue and the number of per-motor movement objects allocated.

## M600: Filament change pause

This command behaves like M226 except that if macro file filament-change.g exists in /sys on the SD card, it is run in preference to pause.g. Supported in firmware 2.02 and later.

## M650: Set peel move parameters

This command is sent by nanoDLP to set the parameters for the peel move used after curing a layer. RepRapFirmware 2.02 ignores this command. If you use RepRapFirmware 2.03 with nanoDLP, create an empty M650.g file in the /sys folder of the SD card so that RRF will ignore it without emitting an error message.

## M651: Execute peel move

This command is sent by nanoDLP to execute a peel move after exposing a layer. RepRapFirmware 2.02 executes macro /sys/peel-move.g in response to this command. To use RepRapFirmware 2.03 or later with nanoDLP, create a macro file M651.g in the /sys folder of the SD card and populate it with the commands needed to execute the peel move.

## M665: Set delta configuration

**Parameters**

* **Lnnn** Diagonal rod length^2^
* **Rnnn** Delta radius
* **Bnnn** Safe printing radius
* **Hnnn** Nozzle height above the bed when homed after allowing for endstop corrections
* **Xnnn** X tower position correction^1^
* **Ynnn** Y tower position correction^1^
* **Znnn** Z tower position correction^1^

**Examples**

```
M665 L250 R160 B80 H240 X0 Y0 Z0
```

Set the delta calibration variables

**Notes**

^1^X, Y and Z tower angular offsets from the ideal (i.e. equilateral triangle) positions, in degrees, measured anti-clockwise looking down on the printer.

^2^In RRF 2.03 and later, multiple L values can be provided, for example **L260.1:260.2:260.0**. The values are the lengths of the rods to the X, Y and Z towers respectively. If more than 3 values are provided, the firmware assumes that there are as many towers as L values up to the maximum supported (currently 6). The XY coordinates of the additional towers must be defined subsequently using the M669 command. If only one L value is provided, the machine is assumed to have 3 towers with all rods having the same length.

## M666: Set delta endstop adjustment

**Parameters**

* **Xnnn** X axis endstop adjustment
* **Ynnn** Y axis endstop adjustment
* **Znnn** Z axis endstop adjustment
* **Annn** X bed tilt in percent^1^
* **Bnnn** Y bed tilt in percent^1^

**Examples**

```
M666 X-0.1 Y+0.2 Z0
```

Sets delta endstops adjustments.

Positive endstop adjustments move the head closer to the bed when it is near the corresponding tower. Endstop corrections are expressed in mm.

^1^RepRapFirmware 1.16 and later.

## M667: Select CoreXY or related mode

This command is deprecated in RRF 2.03 and later. Use M669 instead.

**Parameters**

* **Snnn** CoreXY mode
* **Xnnn** X axis scale factor (RRF 2.02 and earlier)
* **Ynnn** Y axis scale factor (RRF 2.02 and earlier)
* **Znnn** Z axis scale factor (RRF 2.02 and earlier)

**Order dependency**

* M667 must come earlier in config.g than any M671 command.

**Examples**

```
M667 S1
```

M667 S0 selects Cartesian mode (unless the printer is configured as a delta using the M665 command). Forward motion of the X motor moves the head in the +X direction. Similarly for the Y motor and Y axis, and the Z motor and Z axis. This is the default state of the firmware on power up.

M667 S1 selects CoreXY mode. Forward movement of the X motor moves the head in the +X and +Y directions. In firmware 1.19 and later, forward movement of the Y motor moves the head in the +X and -Y directions.

M667 S2 selects CoreXZ mode. Forward movement of the X motor moves the head in the +X and +Z directions. In firmware 1.19 and later, forward movement of the Z motor moves the head in the -X and +Z directions.

In versions of RRF prior to 2.03, additional parameters X, Y and Z may be given to specify factors to scale the motor movements by for the corresponding axes. For example, to specify a CoreXZ machine in which the Z axis moves 1/3 of the distance of the X axis for the same motor movement, use M667 S2 Z3. The default scaling factor after power up is 1.0 for all axes.

In RRF 2.03 and later the XYZ parameters are no longer supported. Use the M669 XYZ parameters to alter the movement matrix instead.

To change the motor directions, see the M569 command.

## M669: Set kinematics type and kinematics parameters

**Parameters**

* **Knnn** Kinematics type: 0 = Cartesian, 1 = CoreXY, 2 = CoreXZ, 3 = linear delta, 4 = serial SCARA, 5 = CoreXYU, 6 = Hangprinter, 7 = polar, 8 = CoreXYUV, 9 = five-bar parallel SCARA (in RRF 3.01, experimental), 10 = rotary delta, 11 = Mark Forged, 12 = reserved for Collinear Tripteron, 13 = reserved for 5-axis robot arm

Note: RepRapFirmware 2.03 and later can support any kinematics for which the movement of each axis is a linear combination of the movement of the motors. The relationship between axis movement and motor movement is defined by a matrix. So K0, K1, K2, K5, K8 and K11 all select the same kinematics, but with different default matrices.

**Order dependency**

* M669 must come earlier in config.g than any M671 command.

Selects the specified kinematics, then uses the other parameters to configure it. If the K parameter is missing then the other parameters are used to update the configuration data for the current kinematics. If no parameters are given then the current kinematics and configuration parameters will be reported.

#### Parameters for Cartesian/CoreXY/CoreXZ/CoreXYU/CoreXYUV/MarkForged kinematics (RRF 2.03 and later only)

* **Xnn:nn:nn...** Motor movement coefficients required to move X axis (first row of matrix)
* **Ynn:nn:nn...** Motor movement coefficients required to move Y axis (second row of matrix)
* **Znn:nn:nn...** Motor movement coefficients required to move Z axis (third row of matrix)
* **Unn:nn:nn..., Vnn:nn:nn... etc.** Motor movement coefficients required to move U, V... axes (fourth and subsequent rows of matrix)
* **Snnn** Segments per second (RRF 3.3 and later)
* **Tnnn** Minimum segment length (mm) (RRF 3.3 and later)

All these parameters are optional. The movement coefficient matrices are initialised to suitable value for the kinematics type you selected in the M667 or M669 command, but you can modify them using these parameters. If you send M669 with no parameters, the existing matrix will be reported.

Note: when CoreXZ kinematics is selected, the default matrix assumes there is a 3:1 reduction on the Z axis, as in the original CoreXZ design described at on the [RepRap forums here](https://reprap.org/forum/read.php?2,377858). If your CoreXZ printer has a different reduction or no reduction then you will need to use the Z parameter to change the Z line of the matrix. For example, if there is no Z reduction then use Z1:0:-1.

In RRF 3, segmentation is not used unless the S and/or T parameter is given. Segmenting moves is useful when faster pause response is wanted.

**Examples**

```
M669
Kinematics is Cartesian, matrix:
1.00 0 0
0 1.00 0
0 0 1.00
```

CoreXY with extra Markforge U axis (see [this forum post](https://forum.duet3d.com/post/136554) for an example)

```
M669 K1 X1:1:0:0 Y1:-1:0:-1 Z0:0:1:0 U0:0:0:1
```

Note U motor values in X, Y, Z and U parameters come after the Z motor values. M669 reports:

```
M669
Kinematics is modified CoreXY, matrix:
1.00 1.00 0 0
1.00 -1.00 0 -1.00
0 0 1.00 0
0 0 0 1.00
```

#### Parameters for Linear Delta kinematics (RRF 2.03 and later only)

* **Xnn:nn:nn...** Extruder offset from nozzle in X
* **Ynn:nn:nn...** Extruder offset from nozzle in X
* **Snnn** Segments per second (RRF 3.3 and later)
* **Tnnn** Minimum segment length (mm) (RRF 3.3 and later)

This is used when a 4th axis is added to a linear Delta, to carry the extruder and follow in Z. It specifies the XY offsets of the extruder outputs on additional towers, relative to machine centre in the M669 command. See [Adding additional towers to carry flying extruders](https://docs.duet3d.com/User_manual/Machine_configuration/Configuration_linear_delta#adding-additional-towers-to-carry-flying-extruders).

In RRF 3, segmentation is not used unless the S and/or T parameter is given. Segmenting moves is useful when faster pause response is wanted.

#### Parameters for serial SCARA kinematics

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

**Examples**

```
M669 K4 P300 D250 A-90:90 B-135:135 C0:0:0 S100 X300 Y0
```

The minimum and maximum arm angles are also the arm angles assumed by the firmware when the homing switches are triggered. The P, D, A and B parameters are mandatory. The C, X and Y parameters default to zero, and the segmentation parameters default to firmware-dependent values.

#### Parameters for Polar kinematics

* **Annn** Maximum turntable acceleration in degrees per second^2 ^1^
* **Fnnn** Maximum turntable speed in degrees per second ^1^
* **Hnnn** Radius of the nozzle from the centre of the turntable when the radius arm homing switch is triggered
* **Raaa:bbb** Minimum (aaa) and maximum (bbb) radius on the turntable reachable by the nozzle.
* **Snnn** Segments per second (because smooth XY motion is approximated by means of segmentation)
* **Tnnn** Minimum segment length (mm) (because smooth XY motion is approximated by means of segmentation)
* **Xnnn** X offset of bed origin from turntable centre (not yet implemented)
* **Ynnn** Y offset of bed origin from proximal joint (not yet implemented)

Note ^1^ : The A and F parameters only apply to normal moves not to G1 H2 (individual motor) moves. The intention is that when printing well away from the centre, the normal X and Y limits set by M201 and M203 are sufficient. When printing at a small radius, movement may need to be slowed down to limit the turntable speed and acceleration.

There is currently no facility for offsetting the radius arm sideways from the centre of rotation of the turntable, or for moving the origin.

## M670: Set IO port bit mapping

**Parameters**

* **Pnn:nn:nn...** - List of logical port numbers that bits 0, 1, 2... control (supported in RRF2.x and earlier)
* **Cnnn** - Used to specify the pin name(s) to be controlled (supported in RRF3), see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names)
* **Tnnn** - Port switching time advance in milliseconds

**Notes**

* In RRF3, the P parameter is removed. Use the new C parameter to specify the pin names to be used.

Example - old code:

```
M670 T5 P220:221:222
```

New code:

```
M670 T5 C"sx1509b.0+sx1509b.1+sx1509b.2"
```

* RepRapFirmware 1.19 and later provides an optional P parameter on the G1 command to allow I/O ports to be set to specified states for the duration of the move. The argument to the P parameter is a bitmap giving the required state of each port. The M670 command specifies the mapping between the bits of that argument and logical port numbers. Optionally, the T parameter can be used to advance the I/O port switching a short time before the corresponding move begins.

## M671: Define positions of Z leadscrews or bed levelling screws

**Parameters**

* **Xnn:nn:nn...** List of between 2 and 4 X coordinates of the leadscrews that drive the Z axis or the bed levelling screws
* **Ynn:nn:nn...** List of between 2 and 4 Y coordinates of the leadscrews that drive the Z axis or the bed levelling screws
* **Snn** Maximum correction allowed for each leadscrew in mm (optional, default 1.0)
* **Pnnn** Pitch of the bed levelling screws (not used when bed levelling using independently-driven leadscrews). Defaults to 0.5mm which is correct for M3 bed levelling screws.
* **Fnn** Fudge factor, default 1.0

**Order dependency**

* M671 must come later in config.g than any M667 or M669 command.

**Examples**

```
M671 X-15.0:100.0:215.0 Y220.0:-20.0:220.0 ; Z leadscrews are at (-15,220), (100,-20) and (215,220)
```

Informs the firmware of the positions of the leadscrews used to raise/lower the bed or gantry. When this command is used to define the leadscrew positions, the numbers of X and Y coordinates must both be equal to the number of drivers used for the Z axis (see the M584 command). This allows the firmware to perform bed levelling by adjusting the leadscrew motors individually after bed probing. See the G32 command.

For machines without multiple independently-driven Z leadscrews, this command can also be used to define the positions of the bed levelling screws instead. Then bed probing can be used to calculate and display the adjustment required to each screw to level the bed. The thread pitch (P parameter) is used to translate the height adjustment needed to the number of turns of the levelling screws.

For printers that print directly onto a desktop and have levelling feet, this command can be used to define the coordinates of the levelling feet, so that bed probing can be used to calculate and display the adjustments needed to the feet. In this case the displayed corrections must be reversed. For example, "0.2 turn down" means the bed needs to be lowered or the printer raised by 0.2 turn lower at that screw position.

The firmware algorithm assumes perfect gimbal joints between the bed and the leadscrews, so that the bed is completely free to adopt the plane (or the twisted plane if there are 4 leadscrews) defined by the leadscrews. In real printers this is rarely the case and the corrections are insufficient to level the bed, so multiple G32 commands need to be sent if the bed is a long way off level. The F parameter allows for the corrections calculated by the firmware to be multiplied by a factor so as to achieve faster convergence in this situation.

Note: the M671 command in config.g must come after any command to change the kinematics, for example M667 or M669.

## M672: Program Z probe

**Parameters**

* **Snn:nn:nn...** Sequence of 8-bit unsigned values to send to the currently-selected Z probe

**Examples**

```
M672 S105:50:205
```

This command is for sending configuration data to programmable Z probes such as the Duet3D delta effector, for example to set the sensitivity. The specified command bytes are sent to the probe. The Duet3D probe stores the configuration data in non-volatile memory, so there is no need to send this command every time the probe is used.

For the Duet3d smart effector:
* The programming pin has to be defined in the M558 command
* To program the sensor, send command M672 S105:aaa:bbb replacing aaa by the desired sensitivity and bbb by 255 - aaa. The green LED will flash 4 times if the command is accepted. When you subsequently power up the effector, the green LED will flash three times instead of twice to indicate that a custom sensitivity is being used.
* To revert to factory settings, send command M672 S131:131. The green LED will flash 5 times if the command is accepted. When you subsequently power up the effector, the green LED will flash twice to indicate that default settings are being used.

See the [Smart effector and carriage adapters for delta printer](/Duet3D_hardware/Accessories/Smart_Effector) documentation for more details.

## M673: Align plane on rotary axis

**Parameters**

* **U,V,W,A,B,C** Rotary axis name on which the plane is mounted
* **Pnnn** Factor to multiply the correction amount with (defaults to 1)

**Examples**

```
M673 A
```

This code is intended to align a plane that is mounted on a rotary axis. To make use of this code it is required to take two probe points via G30 P first.

Supported in RepRapFirmware 2.02 and later.

## M674: Set Z to center point

This code is intended to determine the Z center point of a stash that is mounted on a rotary axis. This code is yet to be implemented.

## M675: Find center of cavity

**Parameters**

* **X,Y,Z** Axis to probe on
* **Fnnnn** Probing feedrate
* **Rnnn** Distance to move away from the lower endstop before the next probing move starts

**Additional Parameter in RepRapFirmware 3 and later**

* **Pnnn** Use probe with the given number instead of endstop

**Examples**

```
M675 X R2 F1200
```

This code is intended to find the center of a cavity that can be measured using the configured axis endstop. If using a Z probe for this purpose, make sure the endstop type for the corresponding axis is updated before this code is run. Once this code starts, RepRapFirmware will move to the lower end looking for an endstop to be triggered. Once it is triggered, the lower position is saved and the axis maximum is probed. As soon as both triggers have been hit, the center point is calculated and the machine moves to the calculated point.

## M701: Load filament

**Parameters**

* *This command can be used without any additional parameters.*
* **Snn** Filament to load

**Examples**

```
M701 S"PLA"
```

RepRapFirmware 1.19 and later implements a filament management mechanism to load and unload different materials.

This code may be used to load a material for the active tool, however be aware that this code will work only for tools that have exactly one extruder assigned.

When called the firmware does the following:

1) Run the macro file "load.g" in the subdirectory of the given material (e.g. /filaments/PLA/load.g)

2) Change the filament name of the associated tool, so it can be reported back to Duet Web Control

If this code is called without any parameters, RepRapFirmware will report the name of the loaded filament (if any).

## M702: Unload filament

**Parameters**

* *This command can be used without any additional parameters.*

**Examples**

```
M702
```

In contrast to M701 this code is intended to unload the previously loaded filament from the selected tool. RepRapFirmware will do the following when called:

1) Run the macro file "unload.g" in the subdirectory of the given material (e.g. /filaments/PLA/unload.g)

2) Change the filament name of the current tool, so it can be reported back to Duet Web Control

## M703: Configure filament

**Parameters**

* *This command can be used without any additional parameters.*

**Examples**

```
M703
```

After assigning a filament to a tool, this command may be used to run /filaments/\<filament name>/config.g to set parameters like temperatures, extrusion factor, retract distance. If no filament is loaded, the code completes without a warning.

If the filaments feature is used, it is recommended to put this code into tpost*.g to ensure the right filament parameters are set. Supported in RepRapFirmware 2.02 and newer.

## M750: Enable 3D scanner extension

**Examples**

M750

This code may be used as an OEM extension to enable scanner functionality in the firmware. After a regular start of RepRapFirmware, the 3D scan extension is disabled by default, but if additional scanner components are attached, this code may be used to enable certain OEM functions.

## M751: Register 3D scanner extension over USB

**Examples**

M751

When a 3D scanner board is attached to the USB port, this code is used to turn on communication between the 3D printing and the scanner board. If the USB connection is removed while the 3D scanner configuration is active, the firmware will disable it again and restore the default communication parameters.

## M752: Start 3D scan

**Parameters**

* Snnn: Length/degrees of the scan
* Rnnn: Resolution (new in RRF 2.0) [optional, defaults to 100]
* Nnnn: Scanner mode (new in RRF 2.0) [optional, 0=Linear (default), 1=Rotary]
* Pnnn: Filename for the scan

**Examples**

M752 S300 Pmyscan

Instruct the attached 3D scanner to initiate a new 3D scan and to upload it to the board's SD card (i.e. in the "scans" directory). Before the SCAN command is sent to the scanner, the macro file "scan_pre.g" is executed and when the scan has finished, the macro file "scan_post.g" is run. Be aware that both files must exist to avoid error messages.

## M753: Cancel current 3D scanner action

**Examples**

M753

Instruct the attached 3D scanner to cancel the current operation. Cancelling uploads is not supported.

## M754: Calibrate 3D scanner

**Parameters**

* Nnnn: Scanner mode (new in RRF 2.0) [optional, 0=Linear (default), 1=Rotary]

**Examples**

M754

Calibrates the attached 3D scanner. Before the calibration is performed by the external scanner, "calibrate_pre.g" is run and when it is complete, "calibrate_post.g" is executed.

## M755: Set alignment mode for 3D scanner

**Parameters**

* Pnnn Whether to turn on (> 0) or off (<= 0) the alignment feature

**Examples**

M755 P1

M755 P0

Sends the ALIGN ON/OFF command the attached 3D scanner. Some devices turn on a laser when this command is received. If the 'P' parameter is missing, equal to, or less than 0, the alignment feature is turned off. Depending on whether the alignment is turned on or off, either align_on.g or align_off.g is executed before the ALIGN command is sent to the scanner.

## M756: Shutdown 3D scanner

**Examples**

M756

Sends the SHUTDOWN command the attached 3D scanner.

## M851: Set Z-Probe Offset (Marlin Compatibility)

*RepRapFirmware 2.02 and later*

M851 Znn is implemented for backwards compatibility with other firmware. It sets the Z probe trigger in the same way as [G31 Z-nn](/User_manual/Reference/Gcodes/G31) *(note the sign reversal)*. It also flags the Z-probe G31 parameters as to be saved in config-override.g if the M500 command is used.

[G31](/User_manual/Reference/Gcodes/G31) should be used in preference to M851.

## M905: Set local date and time

Supported in RepRapFirmware version 1.16 and later.

**Parameters**

* **Pnnn** Current date in the format YYYY-MM-DD
* **Snnn** Current time in the format HH:MM:SS
* **Tnnn** (Supported by DSF v3.3 and later) Timezone to set (e.g *Europe/Berlin*)

**Examples**

```
M905 P2016-10-26 S00:23:12
M905 P2016-10-26 S00:23:12 T"Europe/Berlin" ^1^
```

Updates the machine's local date and time or reports them if no parameters are specified. The time should be specified in 24-hours format as in "13:45" instead of 1:45PM.

^1^ Only supported by Duets in SBC mode with DSF v3.3 or newer

## M906: Set motor currents

**Parameters**

* **Xnnn** X drive peak motor current^1,3^
* **Ynnn** Y drive peak motor current^1,3^
* **Znnn** Z drive peak motor current^1,3^
* **Ennn** E drive(s) peak motor current(s)^1,3^
* **Innn** Motor current idle factor (0..100)^2^

**Order dependency**

This command must be later in config.g than any M584 command.

**Examples**

```
M906 X300 Y500 Z200 E350:350
```

Sets the peak currents to send to the stepper motors for each axis. The values are in milliamps.

**Notes**

^1^Current setting on the various Duet boards are as follows:

* Duet 2 WiF/Ethernet is done in steps of 100mA and is rounded down.
* Duet Maestro is in steps of 50mA and rounded down.
* Duet 3 MB6HC and EXP3HC it is 26.2mA.
* Duet 3 Mini5+ it is 74mA (provisionally), rounded down.
* Duet 3 1LC tool board is 50ma, rounded down.

^2^This is the percentage of normal that the motor currents should be reduced to when the printer becomes idle but the motors have not been switched off. The default value is 30% and will always be at least 100mA - starting from RRF 2.02 setting it to 0 will disable the steppers after timeout like M18|M84 do and if an axis is related to the motor, throw out the "homing" of it, since it is likely that the position cannot be precisely determined anymore. Note that the idle current is applied globally for all motors and cannot be set per axis.

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour.

Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M906 Z200, not M906 Z200:200

^3^Divide by 1.414 for RMS current as used in Marlin implementations for Trinamic drivers

**NOTE**: As a rule of thumb, the recommendation is to set M906 to use 60-85% of the rated maximum current for the motor. Though you can go above or below as needed, and will have to tune for a balance of motor temperature, motor torque, and noise level. You can also go at it from the EMF calculator route ([reprapfirmware.org](https://www.reprapfirmware.org/) and click on EMF calculator) and play with different values to see how it changes behaviour.

## M911: Configure auto save on loss of power

**Firmware 1.20 and later:**

This code is supported in SBC mode in DSF v3.4-b2 and later (see [here](https://github.com/Duet3D/DuetSoftwareFramework/issues/55)).

**Parameters**

* Saaa Auto save threshold in volts. The print will be stopped automatically and resume parameters saved if the voltage falls below this value. Set to around 1V to 2V lower than the voltage that appears at the Duet VIN terminals at full load. A negative or zero value disables auto save.
* Raaa Resume threshold in volts. Must be greater than the auto save voltage. Set to a high value to disable auto resume.
* P"command string" GCode commands to run when the print is stopped.

**Examples**

```
M911 S19.8 R22.0 P"M913 X0 Y0 G91 M83 G1 Z3 E-5 F1000"
```

When the supply voltage falls below the auto save threshold while a print from SD card is in progress, all heaters will be turned off, printing will be stopped immediately (probably in the middle of a move), the position saved, and the specified command string executed. You should typically do the following in the command string:
* If possible, use M913 to reduce the motor current in order to conserve power. For example, on most printers except deltas you can probably set the X and Y motor currents to zero.
* Retract a little filament and raise the head a little. Ideally the retraction should happen first, but depending on the power reserve when low voltage is detected, it may be best to do both simultaneously.

M911 with no parameters displays the current enable/disable state, and the threshold voltages if enabled.

**Firmware 1.19:**

**Parameters**

* Saaa:bbb:ccc Shutdown threshold (aaa), pause threshold (bbb) and resume threshold (ccc) all in volts, with aaa < bbb < ccc

**Examples**

```
M911 S12.0:19.5:22.0
```

Enables auto-pause if the power voltage drops below the pause threshold. The firmware records the current state of the print so that it can be resumed when power is restored and executes the pause procedure to attempt to park the print head using the residual energy in the power supply capacitors. If the supply voltage continues to drop below the shutdown threshold, the firmware disables all heaters and motors and goes into the shutdown state until either the voltage exceeds the resume threshold or the board is reset. In either case, it may be possible to resume the paused print. If the supply voltage does not fall below the shutdown threshold but recovers and exceeds the resume threshold, then the print is resumed automatically.

If any of the three values is zero or negative, or the three values are not in ascending order, then auto-save is disabled.

M911 with no parameters displays the current enable/disable state, and the threshold voltages if enabled.

## M912: Set electronics temperature monitor adjustment

**Parameters**

* **Pnnn** Temperature monitor channel, default 0
* **Snnn** Value to be added to the temperature reading in degC

**Examples**

M912 P0 S10.5

Many microcontrollers used to control 3D printers have built-in temperature monitors, but they normally need to be calibrated for temperature reading offset. The S parameter specifies the value that should be added to the raw temperature reading to provide a more accurate result.

(Currently only for the CPU on-chip temperature sensor P0. Other P parameter could in the future be added for boards with multiple on-chip sensors)

## M913: Set motor percentage of normal current

**Parameters**

* **X, Y, Z, E** Percentage of normal current to use for the specified axis or extruder motor(s)

**Examples**

M913 X50 Y50 Z50 ; set X Y Z motors to 50% of their normal current

M913 E30:30 ; set extruders 0 and 1 to 30% of their normal current

This allows motor currents to be set to a specified percentage of their normal values as set by M906. It can be used (for example) to reduce motor current during course homing, to make homing quieter or to reduce the risk of damage to endstops, and to reduce current while loading filament to guard against the possibility of feeding too much filament. Use M913 again with the appropriate parameters set to 100 to restore the normal currents.

**Important!** When M913 is executed, it does not wait for all motion to stop first (unlike M906). This is so that it can be used in the M911 power fail script. When using M913 elsewhere, you will typically want to use M400 immediately before M913.

RepRapFirmware does not support individual motor settings where an axis has multiple motors connected to different stepper drivers. The first parameter specified will be used for all motors on the axis. You should use identical motors on any axis that has more than one motor to avoid unexpected behaviour.

Example: If you have two motors on your Z axis, physically connected to Z and E0 stepper drivers, configured with M584 Z2:3, set M913 Z50, not M913 Z50:50

## M914: Set/Get Expansion Voltage Level Translator

This command is supported in the Alligator build of RepRapFirmware only.

**Parameters**

* **Sn** Expansion voltage signal level, n must be 3 or 5

**Examples**

M914 S5 ; set expansion signal level to 5V

M914 ; report expansion signal voltage level

## M915: Configure motor stall detection

**Parameters**

* **Pnnn:nnn:...** Drive number(s) to configure
* **X,Y,Z,U,V,W,A,B,C** Axes to configure (alternative to using the P parameter)
* **Snnn** Stall detection threshold (-64 to +63, values below -10 not recommended). Lower values make stall detection more sensitive. S3 is a good starting point for many motors.
* **Fn** Stall detection filter mode, 1 = filtered (one reading per 4 full steps), 0 = unfiltered (default, 1 reading per full step)
* **Hnnn** (optional) Minimum motor full steps per second for stall detection to be considered reliable, default 200
* **Tnnn** (optional) Coolstep control register, 16-bit unsigned integer
* **Rn** Action to take on detecting a stall from any of these drivers: 0 = no action (default), 1 = just log it, 2 = pause print, 3 = pause print, execute /sys/rehome.g, and resume print

**Order dependency**

If this command refers to any axes other than X, Y and Z then it must appear later in config.g than the M584 command that creates those additional axes.

**Examples**

```
M915 P0:2:3 S10 F1 R0
M915 X Y S5 R2
```

This sets the stall detection parameters and optionally the low-load current reduction parameters for TMC2660, TMC2130 or similar driver chips. Use either the P parameter to specify which driver number(s) you want to configure, or the axis names of the axes that those motors drive (the parameters will then be applied to all the drivers associated with any of those axes).

If any of the S, F, T and R parameters are absent, the previous values for those parameters associated with the specified drivers will continue to be used. If all the parameters are absent, the existing settings for the specified drive(s) will be reported.

See the Trinamic TMC2660 and TMC2130 datasheets for more information about the operation and limitations of motor stall detection.

See here for more detailed information on [Stall Detection and Sensorless Homing](/User_manual/Connecting_hardware/Sensors_stall_detection).

## M916: Resume print after power failure

Supported in firmware 1.20 beta 2 and later.

**Parameters**

* none

**Examples**

```
M916
```

If the last print was not completed and resume information has been saved (either because the print was paused or because of a power failure), then the heater temperatures, tool selection, head position, mix ratio, mesh bed compensation height map etc. are restored from the saved values and printing is resumed.

RepRapFirmware also requires macro file **/sys/resurrect-prologue.g** to be present on the SD card before you can use M916. This file is executed after the heater temperatures have been set, but before waiting for them to reach the assigned temperatures. You should put commands in this file to home the printer as best as you can without disturbing the print on the bed. To wait for the heaters to reach operating temperature first, use command M116 at the start of the file.

Version 1.19 of RepRapFirmware does not support M916 but you can achieve the same effect using command **M98 P/sys/resurrect.g**.

## M917: Set motor standstill current reduction

Supported in firmware 1.20 and later for the Duet 2 Maestro, and 3.01 and later for Duet 3.

**Parameters**

* **X,Y,Z,E** Percentage of normal current to use when the motor is standing still or moving slowly, default 71

**Order dependency**

If this command refers to any axes other than X, Y and Z then it must appear later in config.g than the M584 command that creates those additional axes.

**Examples**

```
M917 X70 Y70 Z80 E70:70
```

Some motor drivers allow higher motor currents to be used while the motor is moving. This command sets the percentage of the current set by M906 that is to be used when the motor is stationary but not idle, or moving very slowly.

Standstill current reduction is not the same as idle current reduction. The standstill current must be high enough to produce accurate motion at low speeds. The idle current needs only to be high enough to hold the motor position well enough so that when the current is restored to normal, the position is the same as it was before the current was reduced to idle.

When M906 is used to set the motor current to 71% or more of the maximum permitted current, RRF will limit the maximum standstill current percentage so that the standstill current is no more than 71% of the maximum permitted motor current. This is to ensure that a single phase of the driver does not pass more than 71% of the maximum current continuously, which would risk overheating the output mosfets of that phase.

## M918: Configure direct-connect display

This command is used to tell RepRapFirmware about a directly-connected LCD such as a 12864 or compatible display. Supported on Duet 2 Maestro.

**Parameters**

* **Pn** Directly-connected display type: P1 = 128x64 pixel mono graphics display using ST7920 display driver chip, P2 = 128x64 display using ST7567 display driver chip (RRF 3.02 and later).
* **En** The number of pulses generated by the rotary encoder per detent. Typical values are 2 and 4. Negative values (e.g. -2 and -4) reverse the encoder direction.
* **F** (optional, supported in RRF 2.03 and later) SPI clock frequency in Hz, default 2000000 (i.e. 2MHz)
* **C** (optional, supported in RRF 3.2 and later) Display contrast, in range 0 to 100. Only used with ST7567-based displays. ST7920-based displays usually have a contrast potentiometer instead.
* **R** (optional, supported in RRF 3.2 and later) Display resistor ratio, in range 1 to 7. Only used with ST7567-based displays. The default value of 6 is suitable for the Fysetc Mini 12864 display. Some other displays need 3.

**Examples**

```
M918 P1 E2
```

## M929: Start/stop event logging to SD card

**Parameters**

* **P"filename"** The name of the file to log to. Only used if the S1 parameter is present. A default filename will be used if this parameter is missing.
* **Sn** S1 = start logging, S0 = stop logging (RRF <= 3.2.0-beta2)
* **Sn** S0 = stop logging, S1 = log level WARN, S2 = log level INFO, S3 = log level DEBUG (RRF >= 3.2.0-beta3)

**Examples**

```
M929 P"eventlog.txt" S1 ; start logging to file eventlog.txt
M929 S0 ; stop logging
```

When event logging is enabled, important events such as power up, start/finish printing and (if possible) power down will be logged to the SD card. Each log entry is a single line of text, starting with the date and time if available, or the elapsed time since power up if not. If the log file already exists, new log entries will be appended to the existing file.

Starting with RepRapFirmware 3.2.0-beta3 there is a more fine granular logging available that is not split into 3 log levels plus no logging. Each line in the log will have the log level of that message added right after the timestamp.

* WARN: All log messages from previous versions will fall into this log level
* INFO: G10, M117, M291 and M292 invocations will fall into this log level
* DEGUG: all output not listed above will be logged within this log level

Also see M118 .

**Caution**: do not rename or delete the current log file while logging is enabled!

## M950: Create heater, fan, spindle or GPIO/servo pin

Supported in RepRapFirmware 3.

M950 is used to create heaters, fans and GPIO ports and to assign pins to them. Each M950 command assigns a pin or pins to a single device. So every M950 command must have **exactly one** of the H, F, J, P or S parameters.

If a M950 command has C and/or Q parameters, then the pin allocation and/or frequency will be configured accordingly. Otherwise, the current configuration will be reported.

**Parameters**

* **Hnn** Heater number
* **Fnn** Fan number
* **Jnn** Input pin number (RRF 3.01RC2 and later only)
* **Pnn** or **Snn** Output/servo pin number. Servo pins are just GpOut pins with a different default PWM frequency.
* **Rnn** Spindle number (RRF 3.3beta2 and later only)
* **C"name"** Pin name(s) and optional inversion status, see [Pin Names](/User_manual/RepRapFirmware/Migration_RRF2_to_RRF3#pin-names). Pin name "nil" frees up the pin. A leading '!' character inverts the input or output. A leading '^' character enables the pullup resistor^1^. The '^' and '!' characters may be placed in either order.
* **Qnn** (optional) PWM frequency in Hz. Valid range: 0-65535, default: 500 for GpOut pins, 250 for fans and heaters
* **T** Temperature sensor number, required only when creating a heater. See M308.
* **Lbbb** or **Laaa:bbb** RPM values that are achieved at zero PWM and at maximum RPM. (optional and for spindles only - RRF 3.3beta2 and later)

^1^ Check the individual hardware pages, some IO pins have permanent pullups.

**Order dependency**

* When M950 is used to create a heater, the M950 command must come later in config.g than the M308 command that creates the sensor referred to in the T parameter
* M950 must come before any commands that refer to the device being created. For example, when M950 is used to create a heater, it must be earlier than the M307 command used to set the heater parameters, and earlier than any M563 commands that create tools that use that heater. When M950 is used to create a fan, it must come earlier than any M106 commands relating to that fan.

When using M950 to create a fan, the port name string may be either a single port, or two ports separated by the '+' sign. The second port is used to read the fan tacho. **Any CAN address at the start of the port name string applies to both port names.**

```
M950 F5 C"!out4+out4.tach" Q450 ; Create Fan 5 on the mainboard on OUT4 with a tacho input
M950 F0 C"!1.out3+out3.tach" Q450 ; Create Fan 0 on expansion board 1, output OUT3 with a tacho input
```

When using M950 to create a heater, RRF 3.4 allows multiple port names to be provided separated by the '+' sign. The maximum number of ports that may be used depends on the board. **Any CAN address at the start of the port name string applies to all the port names.**

When using M950 to create a spindle (from RRF 3.3) use the following format:

```
M950 R0 C"pwm_pin+forward_pin+reverse_pin" Qfff Laa:bb
```

* C can have 1, 2 or 3 pins. The first pin defined is a pwm capable pin to set the spindle speed. If a second pin is defined it is used as spindle on/of, if three pins are defined then the second pin is spindle forward and the third is spindle reverse.
* "fff" is the PWM frequency as usual
* "Laa:bb" sets the RPM range as "aa" to "bb". "Lbb" just sets the max RPM to "bb". Default RPM values are 60 min 10000 max

**Examples**

```
M950 H1 C"out1" Q100 T1 ; create heater 1
M950 H1 C"3.out0+out2" Q100 T1 ; create heater 1 using ports OUT0 and OUT1 on CAN board 3 (RRF 3.4 or later)
M950 H2 C"nil" ; disable heater 2 and free up the associated pin
M950 F3 C"heater2" Q100 ; Fan 3 is connected to heater 2 pin, PWM at 100Hz
M950 P0 C"exp.heater3" ; create output/servo port 0 attached to heater 3 pin on expansion connector
M950 F2 C"!fan2+^pb6" ; Fan 2 uses the Fan2 output, but we are using a PWM fan so the output needs to be inverted, also we are using PB6 as a tacho input with pullup resistor enabled
M950 J1 C"!^e1stop" ; Input 1 uses e1Stop pin, inverted, pullup enabled
M950 R0 C"!exp.heater3" L12000 ; Spindle 0 uses exp.heater3 as RPM pin and has a max RPM of 12000
```

## M951: Set height following mode parameters

Supported in RepRapFirmware 3.x

Height following mode allows the Z position of the tool to be controlled by a PID controller using feedback from a sensor. See also M594.

**Parameters**

* **Hnn** Sensor number
* **Pnn.n** Proportional factor, in mm per sensor unit
* **Inn.n** Integral factor, in mm per sensor unit per second
* **Dnn.n** Derivative factor, in mm per rate of change of sensor units (change in sensor unit per second)
* **Fnn.n** (optional) Sample and correction frequency (Hz), default 5Hz
* **Znn.n:nn.n** Minimum and maximum permitted Z values

If commanding the motors to increase Z causes the sensor value to increase, then all of P, I and D must be positive. If commanding the motors to increase Z causes the sensor value to decrease, then all of P, I and D must be negative.

## M952: Set CAN-FD expansion board address and/or normal data rate

**Parameters**

* **Bn** Existing CAN address of expansion board to be changed, 1 to 125.
* **An** New CAN address of that expansion board, 1 to 125.
* **Sn.n** Requested bit rate in Kbits/second (1K = 1000)
* **T0.n** Fraction of the bit time between the bit start and the sample point (optional)
* **J0.n** Maximum jump time as a fraction of the bit time (optional)

**Examples**

```
M952 B121 A20  ; change the CAN address of expansion board 121 to 20
M952 B20 S500  ; change the CAN bit rate or expansion board 20 to 500kbps
```

Some CAN-connected expansion boards are too small to carry address selection switches. Such boards default to a standard address, which can be changed using this command.

Note: the change of CAN address will not take place until the expansion board is restarted.

This command can also be used to change the normal data rate, for example if the printer has CAN bus cables that are too long to support the standard data rate (1Mbits/sec in RepRapFirmware). All boards in the system on the same CAN bus must use the same CAN data rate. The procedure for changing the data rate is:

* Use M952 to change the data rate on all the expansion boards, one at a time. After changing the data rate on each expansion board, you will no longer be able to communicate with it, and you may need to power it down or disconnect it from the CAN bus to prevent it interfering with subsequent CAN communications.
* Change the data rate of the main board last. Then the main board should be able to communicate with all the expansion boards again.

## M953: Set CAN-FD bus fast data rate

**Parameters**

* **Sn.n** Requested bit rate in Mbits/second. Ignored if it is lower than the bit rate for the negotiation phase.
* **T0.n** Fraction of the bit time between the bit start and the sample point (optional)
* **J0.n** Maximum jump time as a fraction of the bit time (optional)
* **Caa:bb** Transceiver delay compensation offset and minimum, in nanoseconds (optional)

**Examples**

M953 S4.0 T0.6 J0.2

This command allows the bandwidth of the CAN bus to be optimised, by increasing the data rate during transmission of CAN-FD data packets by means of the BRS (bit rate switch) feature. The maximum speed supported by CAN-FD is 8Mbits/sec but the practical limit depends on the cable length, cable quality, number of devices on the bus and CAN interface hardware used. The rate specified will be rounded down to the nearest achievable rate.

The optional C parameter allows fine-tuning of the transmitter delay compensation. The first parameter is the offset added to the measured transmitter delay. The optional second value, which must be greater than the first, is the minimum delay compensation applied. Glitches see by the receiver while the transceiver delay is being measured will be ignored if they would result in a transceiver delay compensation lower than this value. When CAN is implemented on Microchip SAME5x and SAMC21 processors, these values are converted from nanoseconds into time quanta and stored in the TDCO and TDCF fields of the transceiver delay compensation register.

## M954: Configure as CAN expansion board

**Parameters**

* **Ann** CAN address to use (required)

This command is used to reconfigure the board it is executed on as a CAN-connected expansion board. It would typically be the only command in the config.g file. When it is executed, the board changes its CAN address to the one specified in the A parameter, stops sending CAN time sync messages, and responds to requests received via CAN just like a regular expansion board. A few GCode commands can still be executed locally for diagnostic purposes, for example M111 and M122

## M955: Configure Accelerometer

To be supported in RRF 3.4 (limited support in 3.3beta3)

**Parameters (provisional)**

* **Pnn** or **Pbb.nn** Accelerometer to use (required)
* **Inn** Accelerometer orientation
* **Snnn** Sample rate (Hz)
* **Rnn** Resolution (bits), typically 8, 10 or 12
* **C"aaa+bbb"** Pins to use for CS and INT (in that order) when connecting the accelerometer via SPI
* **Q**nnn (RRF 3.3RC1 and later) SPI clock frequency (optional, default 2000000 i.e. 2MHz)

This command configures an accelerometer.

The P parameter selects which accelerometer to use and is mandatory. To use an accelerometer on a CAN-connected expansion board, use the form **P***board-address*.*device-number* for example **P22.0**. Use **P0** for an accelerometer connected locally via SPI.

If none of the other parameters are provided, the current configuration of the specified accelerometer is reported. Otherwise the configuration of that accelerometer is adjusted according to the I, S, and R parameters. These configuration settings persist until they are changed.

The I (orientation) parameter tells the firmware which of the 24 possible orientations the accelerometer chip is in relative to the printer axes. It is expressed as a 2-digit number. The first digit specifies which machine direction the Z axis of the accelerometer chip (usually the top face of the chip) faces, as follows: 0 = +X, 1 = +Y, 2 = +Z, 4 = -X, 5 = -Y, 6 = -Z. The second digit expresses which direction the X axis of the accelerometer chip faces, using the same code. If the accelerometer chip axes line up with the machine axis, the orientation is 20. This is the default orientation if no orientation has been specified.

The S and R parameters control how the accelerometer is programmed. The R parameter is ignored unless the S parameter is also provided. If S is provided but R is missing, a default resolution is used. The sensor resolution will be adjusted to be no greater than the value of the R parameter (or the minimum supported resolution if greater), then the sensor sampling rate will be adjusted to a value supported at that resolution that is close to the S parameter. The actual rate and resolution selected can be found by using M955 with just the P parameter.

## M956: Collect accelerometer data and write to file

To be supported in RRF 3.4 (limited support in 3.3beta3)

**Parameters (provisional)**

* **Pnn** or **Pbb.nn** Accelerometer to use (required)
* **Snnn** Number of samples to collect (required)
* **X** and/or **Y** and/or **Z** Machine axes to collect data for. If no axes are specified, data is collected for all three axes.
* **An** (required) 0 = activate immediately, 1 = activate just before the start of the next move, 2 = activate just before the start of the deceleration segment of the next move
* **F"filename.csv"** Name of the file to save the data in (optional, supported by RRF 3.4 and later). The default folder is 0:/sys/accelerometers. If not specified then the filename will be composed from the current date/time.

This command causes the specified number of accelerometer samples to be collected and saved to a .csv file.

The P parameter selects which accelerometer to use and is mandatory. To use an accelerometer on a CAN-connected expansion board, use the form **P***board-address*.*device-number* for example **P22.0**.

## M957: Raise event

Supported in RepRapFirmware 3.4 and later.

**Parameters**

* **E"type"** Event type name
* **Dnn** Device number to which the event relates
* **Bnn** (optional) CAN address of the board that the event should appear to originate from
* **Pnn** (optional) Additional data about the event (unsigned integer)
* **S"text"** (optional) Short test string to be appended to the event message

This command is used to raise an event internally as if the event had actually occurred, and execute any related handler macro for that event. Its main use is to test event handler macros.

**Examples**

```
M957 E"heaterFault" D1 B2
```

Raise a heater fault from expansion board at CAN address 2 on heater 1

**Notes**

The event type names are firmware-dependent. In RepRapFirmware they are: mainBoardPowerFail, heaterFault, driverError, filamentError, driverWarning, mcuTemperatureWarning.

The meaning of the device number depends on the event type. For a driver error it is the driver number. For a heater fault it is the heater number. For a filament error it is the extruder number.

The meaning of the optional additional parameter also depends on the event type. For example, for a driver error it is the driver status.

## M997: Perform in-application firmware update

**Parameters**

* **Snnn** Firmware module number(s), default 0
* **Bnnn** CAN address of the board to be updated (RRF3, Duet 3 only)
* **P"filename"** Filename of firmware binary to use (RRF 3.3beta2 and later)

**Examples**

```
M997 S0:1 - update firmware modules 0 and 1
```

**Notes**

This command triggers a firmware update if the necessary files are present on the SD card. In RepRapFirmware on the Duet series, module numbers are as follows:

* 0 - main firmware, filename sys/RepRapFirmware.bin (older Duets) or sys/Duet2CombinedFirmware (Duet 2). File sys/iap.bin (Duet) or sys/iap4e.bin (Duet 2) must also be present.
* 1 - web server firmware, filename sys/DuetWiFiServer.bin (Duet 2 WiFi only)
* 2 - web server file system, filename sys/DuetWebControl.bin (needed only when using RepRapFirmware 1.18 series and earlier for Duet 2 WiFi)
* 3 - put the WiFi module into bootloader mode, so that firmware can be uploaded directly via its serial port. Also used to update bootloader on CAN-connected Duet 3 expansion boards.
* 4 - PanelDue firmware (RRF 3.2-beta4.1 and later; see [PanelDue firmware update instructions](/User_manual/RepRapFirmware/Updating_PanelDue)).

On Duet 3 only this command take an optional B (board number) parameter which is the CAN address of the board to be updated, default 0 (i.e. main board).

The optional **P** parameter can be used to provide the filename of the file to be used for updating a module. This can either only be a filename in which case it will prepend directories.firmware to it (0:/firmware) or can be an absolute path to the file to be used. It is not allowed to use P parameter and multiple modules, e.g. S1:4. (RRF 3.3beta2 and later)

See [Installing and Updating Firmware](/User_manual/RepRapFirmware/Updating_firmware) for detailed documentation.

## M998: Request resend of line

**Parameters**

* **Pnnn** Line number

**Examples**

```
M998 P34
```

Request a resend of line 34. In some implementations the input-handling code overwrites the incoming G Code with this when it detects, for example, a checksum error. Then it leaves it up to the GCode interpreter to request the resend.

## M999: Restart

**Parameters**

* *This command can be used without any additional parameters.*
* **Bnnn** Optional CAN address of the board to restart (defaults to 0)^2^
* **Pnnn** Reset flags^1^

**Examples**

```
M999
```

Restarts the firmware using a software reset.

**Notes**

^1^This also puts the board into firmware upload mode (as if the Erase button had been pressed) if parameter P"ERASE" is present.

^2^ Starting from v3.3 this parameter may be set to -1 to reboot the attached SBC (DuetPi + SBC)

# T-commands

## T: Select Tool

**Parameters**

* **nnn**: Tool number to select. A negative number deselects all tools.
* **R1**: Select the tool that was active when the print was last paused (firmware 1.20 and later)
* **Pnnn**: Bitmap of all the macros to be run (dc42 build 1.19 or later and ch fork 1.17b or later)
* Tool number

**Examples**

```
T0 ; select tool 0
T1 P0 ; select tool 1 but don't run any tool change macro files
T-1 P0 ; deselect all tools but don't run any tool change macro files
T R1 ; select the tool that was active last time the print was paused
T ; report the current tool number
```

If T*n* is used to select tool *n* but that tool is already active, the command does nothing. Otherwise, the sequence followed is:

1. If another tool is already selected and all axes have been homed, run macro tfree#.g where # is the number of that tool. As of release 3.3, the tool change files will be actioned even if not homed, so that you can use conditional GCode to choose which commands to run.
1. If another tool is already selected, deselect it and set its heaters to their standby temperatures (as defined by the R parameter in the most recent G10/M568 command for that tool)
1. If all axes have been homed, run macro tpre#.g where # is the number of the new tool
1. Set the new tool to its operating temperatures specified by the S parameter in the most recent G10/M568 command for that tool
1. If all axes have been homed, run macro tpost#.g where # is the number of the new tool. Typically this file would contain at least a M116 command to wait for its temperatures to stabilise.
1. Apply any X, Y, Z offset for the new tool specified by G10/M568
1. Use the new tool.

Selecting a non-existent tool (49, say) just does Steps 1-2 above^1^. That is to say it leaves the previous tool in its standby state. You can, of course, use the G10/M568 command beforehand to set that standby temperature to anything you like.

After a reset tools will not start heating until they are selected. You can either put them all at their standby temperature by selecting them in turn, or leave them off so they only come on if/when you first use them. The M0, M1 and M112 commands turn them all off. You can, of course, turn them all off with the M1 command, then turn some back on again. Don't forget also to turn on the heated bed (if any) if you use that trick.

Tool numbering starts at 0 by default however M563 allows the user to specify tool numbers, so with them you can have tools 17, 29 and 48 if you want. Negative numbers are not allowed. The highest Tool number that can be defined from RRF3 onwards is 49

Starting from RRF 3.3beta2 both selecting as well as deselecting with a configured spindle will stop the spindle assigned to these tools. This is in accordance to NIST GCode standard that says "after a tool change is complete the spindle is stopped".

**Notes**

^1^ Selecting a non-existent tool also removes any X/Y/Z offset applied for the old tool.

^2^ Under special circumstances, the execution of those macro files may not be desired. RepRapFirmware 1.19 or later supports an optional P parameter to specify which macros shall be run. If it is absent then all of the macros above will be run, else you can pass a bitmap of all the macros to be executed. The bitmap of this value consists of tfree=1, tpre=2 and tpost=4.

^3^ You may wish to include a move to a parking position within the tfreeN.g GCode macro in order to allow the new extruder to reach temperature while not in contact with the print.

^4^ Tool offsets are applied whenever there is a current tool. So they are applied in tfree.g (for the outgoing tool) and in tpost.g (for the incoming tool), but not in tpre.g (because no tool is current at that point).

# GCode Background Information

Codes for print head movements follow the [NIST GCode Interpreter Version 3 standard](http://www.nist.gov/manuscript-publication-search.cfm?pub_id=823374), so RepRapFirmware should be usable for CNC milling and similar applications but be aware of the [GCodes not implemented](/User_manual/Reference/Gcodes_not_implemented). See also on [Wikipedia](https://en.wikipedia.org/wiki/G-code). For more information and background, along with the master list of all RepRap GCodes, check [RepRap GCode page](http://reprap.org/wiki/G-code).

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

Slicers will optionally add GCode scripts to the beginning and end of their output file to perform specified actions before and/or after a print such as z-probing the build-area, heating/cooling the bed and hotend, performing ooze free "nozzle wipe" startup routine, switching system power on/off, and even "ejecting" parts. For general information and examples (not specific to RepRapFirmware) there is more info on the [Start GCode routines](https://reprap.org/wiki/Start_GCode_routines) and [End GCode routines](https://reprap.org/wiki/End_GCode_routines) pages.