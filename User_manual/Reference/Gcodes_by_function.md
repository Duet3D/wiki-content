---
title: GCodes by function
description: 
published: true
date: 2026-04-01T13:44:35.692Z
tags: 
editor: markdown
dateCreated: 2021-07-06T15:58:12.677Z
---

# Alternate machine modes

## 3D scanner

[M750](/User_manual/Reference/Gcodes/M750): Enable 3D scanner extension

[M751](/User_manual/Reference/Gcodes/M751): Register 3D scanner extension over USB

[M752](/User_manual/Reference/Gcodes/M752): Start 3D scan

[M753](/User_manual/Reference/Gcodes/M753): Cancel current 3D scanner action

[M754](/User_manual/Reference/Gcodes/M754): Calibrate 3D scanner

[M755](/User_manual/Reference/Gcodes/M755): Set alignment mode for 3D scanner

[M756](/User_manual/Reference/Gcodes/M756): Shutdown 3D scanner

## CNC/Laser

[M3](/User_manual/Reference/Gcodes/M3): Spindle On, Clockwise (CNC specific)/ Laser on (Laser specific)

[M4](/User_manual/Reference/Gcodes/M4): Spindle On, Counterclockwise (CNC specific)

[M5](/User_manual/Reference/Gcodes/M5): Spindle Off (CNC specific), laser off (Laser specific)

[M452](/User_manual/Reference/Gcodes/M452): Select Laser DeviceMode

[M453](/User_manual/Reference/Gcodes/M453): Select CNC Device Mode

## NanoDLP

[M650](/User_manual/Reference/Gcodes/M650): Set peel move parameters

[M651](/User_manual/Reference/Gcodes/M651): Execute peel move

# Calibration

## Bed levelling and compensation

[G29](/User_manual/Reference/Gcodes/G29): Mesh bed probe

[G32](/User_manual/Reference/Gcodes/G32): Run bed.g macro

[M374](/User_manual/Reference/Gcodes/M374): Save height map

[M375](/User_manual/Reference/Gcodes/M375): Load height map

[M376](/User_manual/Reference/Gcodes/M376): Set bed compensation taper

[M556](/User_manual/Reference/Gcodes/M556): Axis skew compensation

[M557](/User_manual/Reference/Gcodes/M557): Set Z probe point or define probing grid

[M561](/User_manual/Reference/Gcodes/M561): Set Identity Transform

[M671](/User_manual/Reference/Gcodes/M671): Define positions of Z leadscrews or bed levelling screws

[M673](/User_manual/Reference/Gcodes/M673): Align plane on rotary axis

## Probe

[G30](/User_manual/Reference/Gcodes/G30): Single Z-Probe

[G31](/User_manual/Reference/Gcodes/G31): Set or Report Current Probe status

[G38.2](/User_manual/Reference/Gcodes/G38_2): Probe toward workpiece, stop on contact, signal error if failure

[G38.3](/User_manual/Reference/Gcodes/G38_3): Probe toward workpiece, stop on contact

[G38.4](/User_manual/Reference/Gcodes/G38_4): Probe away from workpiece, stop on loss of contact, signal error if failure

[G38.5](/User_manual/Reference/Gcodes/G38_5): Probe away from workpiece, stop on loss of contact

[M401](/User_manual/Reference/Gcodes/M401): Deploy z-probe

[M402](/User_manual/Reference/Gcodes/M402): Retract z-probe

[M558](/User_manual/Reference/Gcodes/M558): Set Z probe type

[M585](/User_manual/Reference/Gcodes/M585): Probe Tool

[M672](/User_manual/Reference/Gcodes/M672): Program Z probe

[M851](/User_manual/Reference/Gcodes/M851): Set Z-Probe Offset (Marlin Compatibility)

# Communication

## Comms configuration

[M260](/User_manual/Reference/Gcodes/M260): i2c Send and/or request Data

[M575](/User_manual/Reference/Gcodes/M575): Set serial comms parameters

[M576](/User_manual/Reference/Gcodes/M576): Set SPI comms parameters

## Debug

[M111](/User_manual/Reference/Gcodes/M111): Set Debug Level

[M122](/User_manual/Reference/Gcodes/M122): Diagnose

[M929](/User_manual/Reference/Gcodes/M929): Start/stop event logging to SD card

[M957](/User_manual/Reference/Gcodes/M957): Raise event

## Messaging

[M117](/User_manual/Reference/Gcodes/M117): Display Message

[M118](/User_manual/Reference/Gcodes/M118): Send Message to Specific Target

[M261](/User_manual/Reference/Gcodes/M261): i2c Request Data

[M291](/User_manual/Reference/Gcodes/M291): Display message and optionally wait for response

[M292](/User_manual/Reference/Gcodes/M292): Acknowledge message

[M300](/User_manual/Reference/Gcodes/M300): Play beep sound

## Reporting

[M115](/User_manual/Reference/Gcodes/M115): Get Firmware Version and Capabilities

[M119](/User_manual/Reference/Gcodes/M119): Get Endstop Status

[M408](/User_manual/Reference/Gcodes/M408): Report JSON-style response

[M409](/User_manual/Reference/Gcodes/M409): Query object model

[M450](/User_manual/Reference/Gcodes/M450): Report Printer Mode

# Control

## Input/Output and external peripherals

[M42](/User_manual/Reference/Gcodes/M42): Switch I/O pin

[M150](/User_manual/Reference/Gcodes/M150): Set LED colours

[M280](/User_manual/Reference/Gcodes/M280): Set servo position

[M577](/User_manual/Reference/Gcodes/M577): Wait until endstop is triggered

[M578](/User_manual/Reference/Gcodes/M578): Fire inkjet bits

[M581](/User_manual/Reference/Gcodes/M581): Configure external trigger

[M582](/User_manual/Reference/Gcodes/M582): Check external trigger

[M670](/User_manual/Reference/Gcodes/M670): Set IO port bit mapping

[M955](/User_manual/Reference/Gcodes/M955): Configure Accelerometer

[M956](/User_manual/Reference/Gcodes/M956): Collect accelerometer data and write to file

## Macro

[M98](/User_manual/Reference/Gcodes/M98): Call Macro/Subprogram

[M99](/User_manual/Reference/Gcodes/M99): Return from Macro/Subprogram

[M120](/User_manual/Reference/Gcodes/M120): Push

[M121](/User_manual/Reference/Gcodes/M121): Pop

## Print control

[M37](/User_manual/Reference/Gcodes/M37): Simulation mode

[M73](/User_manual/Reference/Gcodes/M73): Set remaining print time

[M110](/User_manual/Reference/Gcodes/M110): Set Current Line Number

[M486](/User_manual/Reference/Gcodes/M486): Object cancellation

[M998](/User_manual/Reference/Gcodes/M998): Request resend of line

## Stop/pause

[M0](/User_manual/Reference/Gcodes/M0): Stop or Unconditional stop

[M1](/User_manual/Reference/Gcodes/M1): Sleep or Conditional stop

[M112](/User_manual/Reference/Gcodes/M112): Emergency Stop

[M226](/User_manual/Reference/Gcodes/M226): Synchronous Pause

[M999](/User_manual/Reference/Gcodes/M999): Restart

# File management

[M20](/User_manual/Reference/Gcodes/M20): List SD card

[M21](/User_manual/Reference/Gcodes/M21): Initialize SD card

[M22](/User_manual/Reference/Gcodes/M22): Release SD card

[M23](/User_manual/Reference/Gcodes/M23): Select SD file

[M24](/User_manual/Reference/Gcodes/M24): Start/resume SD print

[M25](/User_manual/Reference/Gcodes/M25): Pause SD print

[M26](/User_manual/Reference/Gcodes/M26): Set SD position

[M27](/User_manual/Reference/Gcodes/M27): Report SD print status

[M28](/User_manual/Reference/Gcodes/M28): Begin write to SD card

[M29](/User_manual/Reference/Gcodes/M29): Stop writing to SD card

[M30](/User_manual/Reference/Gcodes/M30): Delete a file on the SD card

[M32](/User_manual/Reference/Gcodes/M32): Select file and start SD print

[M36](/User_manual/Reference/Gcodes/M36): Return file information

[M36.1](/User_manual/Reference/Gcodes/M36_1): Return embedded thumbnail data

[M38](/User_manual/Reference/Gcodes/M38): Compute SHA1 hash of target file

[M39](/User_manual/Reference/Gcodes/M39): Report SD card information

[M470](/User_manual/Reference/Gcodes/M470): Create Directory on SD-Card

[M471](/User_manual/Reference/Gcodes/M471): Rename File/Directory on SD-Card

[M559](/User_manual/Reference/Gcodes/M559): Upload file

[M560](/User_manual/Reference/Gcodes/M560): Upload file

# Heating process

[G10](/User_manual/Reference/Gcodes/G10): Tool Temperature Setting

[M104](/User_manual/Reference/Gcodes/M104): Set Extruder Temperature

[M105](/User_manual/Reference/Gcodes/M105): Get Extruder Temperature

[M106](/User_manual/Reference/Gcodes/M106): Fan On

[M107](/User_manual/Reference/Gcodes/M107): Fan Off

[M108](/User_manual/Reference/Gcodes/M108): Cancel Heating

[M109](/User_manual/Reference/Gcodes/M109): Set Extruder Temperature and Wait

[M116](/User_manual/Reference/Gcodes/M116): Wait

[M135](/User_manual/Reference/Gcodes/M135): Set PID sample interval

[M140](/User_manual/Reference/Gcodes/M140): Set Bed Temperature (Fast) or Configure Bed Heater

[M141](/User_manual/Reference/Gcodes/M141): Set Chamber Temperature (Fast) or Configure Chamber Heater

[M143](/User_manual/Reference/Gcodes/M143): Maximum heater temperature

[M144](/User_manual/Reference/Gcodes/M144): Bed Standby

[M190](/User_manual/Reference/Gcodes/M190): Wait for bed temperature to reach target temp

[M191](/User_manual/Reference/Gcodes/M191): Wait for chamber temperature to reach target temp

[M301](/User_manual/Reference/Gcodes/M301): Set PID parameters

[M303](/User_manual/Reference/Gcodes/M303): Run heater tuning

[M304](/User_manual/Reference/Gcodes/M304): Set PID parameters - Bed

[M305](/User_manual/Reference/Gcodes/M305): Set temperature sensor parameters

[M307](/User_manual/Reference/Gcodes/M307): Set or report heating process parameters

[M309](/User_manual/Reference/Gcodes/M309): Set or report heater feedforward

[M562](/User_manual/Reference/Gcodes/M562): Reset temperature fault

[M570](/User_manual/Reference/Gcodes/M570): Configure heater fault detection

[M573](/User_manual/Reference/Gcodes/M573): Report heater PWM

# Machine configuration

## Machine configuration

[G20](/User_manual/Reference/Gcodes/G20): Set Units to Inches

[G21](/User_manual/Reference/Gcodes/G21): Set Units to Millimeters

[M308](/User_manual/Reference/Gcodes/M308): Set or report sensor parameters

[M451](/User_manual/Reference/Gcodes/M451): Select FFF Printer Mode

[M500](/User_manual/Reference/Gcodes/M500): Store parameters

[M501](/User_manual/Reference/Gcodes/M501): Read stored parameters

[M502](/User_manual/Reference/Gcodes/M502): Revert stored parameters

[M503](/User_manual/Reference/Gcodes/M503): Print settings

[M505](/User_manual/Reference/Gcodes/M505): Set configuration file folder

[M555](/User_manual/Reference/Gcodes/M555): Set compatibility

[M574](/User_manual/Reference/Gcodes/M574): Set endstop configuration

[M579](/User_manual/Reference/Gcodes/M579): Scale Cartesian axes

[M595](/User_manual/Reference/Gcodes/M595): Set movement queue length

[M665](/User_manual/Reference/Gcodes/M665): Set delta configuration

[M666](/User_manual/Reference/Gcodes/M666): Set delta endstop adjustment

[M667](/User_manual/Reference/Gcodes/M667): Select CoreXY or related mode

[M669](/User_manual/Reference/Gcodes/M669): Set kinematics type and kinematics parameters

[M905](/User_manual/Reference/Gcodes/M905): Set local date and time

[M912](/User_manual/Reference/Gcodes/M912): Set electronics temperature monitor adjustment

[M914](/User_manual/Reference/Gcodes/M914): Set/Get Expansion Voltage Level Translator

[M916](/User_manual/Reference/Gcodes/M916): Resume print after power failure

[M918](/User_manual/Reference/Gcodes/M918): Configure direct-connect display

[M950](/User_manual/Reference/Gcodes/M950): Create heater, fan, spindle or GPIO/servo pin

[M952](/User_manual/Reference/Gcodes/M952): Set CAN-FD expansion board address and/or normal data rate

[M953](/User_manual/Reference/Gcodes/M953): Set CAN-FD bus fast data rate

[M954](/User_manual/Reference/Gcodes/M954): Configure as CAN expansion board

[M997](/User_manual/Reference/Gcodes/M997): Perform in-application firmware update

## Network

[M540](/User_manual/Reference/Gcodes/M540): Set MAC address

[M550](/User_manual/Reference/Gcodes/M550): Set Name

[M551](/User_manual/Reference/Gcodes/M551): Set Password

[M552](/User_manual/Reference/Gcodes/M552): Set IP address, enable/disable network interface

[M553](/User_manual/Reference/Gcodes/M553): Set Netmask

[M554](/User_manual/Reference/Gcodes/M554): Set Gateway and/or DNS server

[M586](/User_manual/Reference/Gcodes/M586): Configure network protocols

[M587](/User_manual/Reference/Gcodes/M587): Add WiFi host network to remembered list, or list remembered networks

[M588](/User_manual/Reference/Gcodes/M588): Forget WiFi host network

[M589](/User_manual/Reference/Gcodes/M589): Configure access point parameters

## Power

[M80](/User_manual/Reference/Gcodes/M80): ATX Power On

[M81](/User_manual/Reference/Gcodes/M81): ATX Power Off

[M911](/User_manual/Reference/Gcodes/M911): Configure auto save on loss of power

# Motion

## Drives

[G0](/User_manual/Reference/Gcodes/G0): Rapid move

[G1](/User_manual/Reference/Gcodes/G1): Controlled *(linear) move

[G2](/User_manual/Reference/Gcodes/G2): Clockwise arc move

[G3](/User_manual/Reference/Gcodes/G3): Counter-clockwise arc move

[G4](/User_manual/Reference/Gcodes/G4): Dwell

[G28](/User_manual/Reference/Gcodes/G28): Home

[M17](/User_manual/Reference/Gcodes/M17): Enable all stepper motors

[M18](/User_manual/Reference/Gcodes/M18): Disable all stepper motors

[M84](/User_manual/Reference/Gcodes/M84): Stop idle hold

[M92](/User_manual/Reference/Gcodes/M92): Set axis steps per unit

[M201](/User_manual/Reference/Gcodes/M201): Set max acceleration

[M201.1](/User_manual/Reference/Gcodes/M201_1): Set reduced acceleration for special move types

[M203](/User_manual/Reference/Gcodes/M203): Set maximum feedrate

[M204](/User_manual/Reference/Gcodes/M204): Set printing and travel accelerations

[M205](/User_manual/Reference/Gcodes/M205): Set max instantaneous speed change in mm/sec

[M208](/User_manual/Reference/Gcodes/M208): Set axis max travel

[M220](/User_manual/Reference/Gcodes/M220): Set speed factor override percentage

[M221](/User_manual/Reference/Gcodes/M221): Set extrude factor override percentage

[M290](/User_manual/Reference/Gcodes/M290): Baby stepping

[M350](/User_manual/Reference/Gcodes/M350): Set microstepping mode

[M400](/User_manual/Reference/Gcodes/M400): Wait for current moves to finish

[M564](/User_manual/Reference/Gcodes/M564): Limit axes

[M566](/User_manual/Reference/Gcodes/M566): Set allowable instantaneous speed change

[M569](/User_manual/Reference/Gcodes/M569): Set motor driver direction, enable polarity and step pulse timing

[M569.1](/User_manual/Reference/Gcodes/M569_1): Stepper driver closed loop configuration

[M569.2](/User_manual/Reference/Gcodes/M569_2): Read or write stepper driver register

[M569.3](/User_manual/Reference/Gcodes/M569_3): Read Motor Driver Encoder

[M569.4](/User_manual/Reference/Gcodes/M569_4): Set Motor Driver Torque Mode

[M569.5](/User_manual/Reference/Gcodes/M569_5): Closed loop data collection

[M569.6](/User_manual/Reference/Gcodes/M569_6):  Execute closed loop tuning move

[M569.7](/User_manual/Reference/Gcodes/M569_7):  Configure motor brake port

[M584](/User_manual/Reference/Gcodes/M584): Set drive mapping

[M593](/User_manual/Reference/Gcodes/M593): Configure Input Shaping

[M594](/User_manual/Reference/Gcodes/M594): Enter/Leave Height Following mode

[M675](/User_manual/Reference/Gcodes/M675): Find center of cavity

[M906](/User_manual/Reference/Gcodes/M906): Set motor currents

[M913](/User_manual/Reference/Gcodes/M913): Set motor percentage of normal current

[M915](/User_manual/Reference/Gcodes/M915): Configure motor stall detection

[M917](/User_manual/Reference/Gcodes/M917): Set motor standstill current reduction

[M951](/User_manual/Reference/Gcodes/M951): Set height following mode parameters

## Extrusion

[G10](/User_manual/Reference/Gcodes/G10): Retract

[G11](/User_manual/Reference/Gcodes/G11): Unretract

[M82](/User_manual/Reference/Gcodes/M82): Set extruder to absolute mode

[M83](/User_manual/Reference/Gcodes/M83): Set extruder to relative mode

[M101](/User_manual/Reference/Gcodes/M101): Un-retract (Simplify3D compatibility)

[M102](/User_manual/Reference/Gcodes/M102): Turn extruder on (ignored, Simplify3D compatibility)

[M103](/User_manual/Reference/Gcodes/M103): Retract  (Simplify3D compatibility)

[M200](/User_manual/Reference/Gcodes/M200): Set filament diameter

[M207](/User_manual/Reference/Gcodes/M207): Set retract length

[M302](/User_manual/Reference/Gcodes/M302): Allow cold extrudes

[M404](/User_manual/Reference/Gcodes/M404): Filament width and nozzle diameter

[M571](/User_manual/Reference/Gcodes/M571): Set output on extrude

[M572](/User_manual/Reference/Gcodes/M572): Set or report extruder pressure advance

[M591](/User_manual/Reference/Gcodes/M591): Configure filament sensing

[M592](/User_manual/Reference/Gcodes/M592): Configure nonlinear extrusion

[M600](/User_manual/Reference/Gcodes/M600): Filament change pause

[M701](/User_manual/Reference/Gcodes/M701): Load filament

[M702](/User_manual/Reference/Gcodes/M702): Unload filament

[M703](/User_manual/Reference/Gcodes/M703): Configure filament

# Tool settings and coordinates

[T](/User_manual/Reference/Gcodes/T): Select tool

[G10](/User_manual/Reference/Gcodes/G10): Set workplace coordinate offset or tool offset

[G17](/User_manual/Reference/Gcodes/G17): Select XY plane for arc moves

[G18](/User_manual/Reference/Gcodes/G18): Select XZ plane for arc moves

[G19](/User_manual/Reference/Gcodes/G19): Select YZ plane for arc moves

[G53](/User_manual/Reference/Gcodes/G53): Use machine coordinates

[G54](/User_manual/Reference/Gcodes/G54): selects coordinate system 1

[G55](/User_manual/Reference/Gcodes/G55): selects coordinate system 2

[G56](/User_manual/Reference/Gcodes/G56): selects coordinate system 3

[G57](/User_manual/Reference/Gcodes/G57): selects coordinate system 4

[G58](/User_manual/Reference/Gcodes/G58): selects coordinate system 5

[G59](/User_manual/Reference/Gcodes/G59): selects coordinate system 6

[G59.1](/User_manual/Reference/Gcodes/G59_1): selects coordinate system 7

[G59.2](/User_manual/Reference/Gcodes/G59_2): selects coordinate system 8

[G59.3](/User_manual/Reference/Gcodes/G59_3): selects coordinate system 9

[G60](/User_manual/Reference/Gcodes/G60): Save current position to slot

[G68](/User_manual/Reference/Gcodes/G68): Coordinate rotation

[G69](/User_manual/Reference/Gcodes/G69): Cancel coordinate rotation

[G90](/User_manual/Reference/Gcodes/G90): Set to Absolute Positioning

[G91](/User_manual/Reference/Gcodes/G91): Set to Relative Positioning

[G92](/User_manual/Reference/Gcodes/G92): Set Position

[M114](/User_manual/Reference/Gcodes/M114): Get Current Position

[M206](/User_manual/Reference/Gcodes/M206): Offset axes

[M563](/User_manual/Reference/Gcodes/M563): Define or remove a tool

[M567](/User_manual/Reference/Gcodes/M567): Set tool mix ratios

[M568](/User_manual/Reference/Gcodes/M568): Set Tool Settings

[M674](/User_manual/Reference/Gcodes/M674): Set Z to center point