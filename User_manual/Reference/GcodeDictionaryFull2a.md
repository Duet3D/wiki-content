---
title: GDF2a
description: 
published: true
date: 2021-12-21T02:25:02.990Z
tags: 
editor: markdown
dateCreated: 2021-12-21T02:25:02.990Z
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
