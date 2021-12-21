---
title: Events
description: in RRF3.4b7 the first version of a new event handling system has been introduced. An “event” is an occurrence that occurs during a job and may require the normal printing process to be paused and some manual or automatic action to be performed.
published: true
date: 2021-12-17T14:46:20.949Z
tags: 
editor: markdown
dateCreated: 2021-12-17T14:46:17.569Z
---

# Introduction

Prior to RRF 3.4, conditions that now trigger an event on a mainboard were handled in a range of ways, depending on the issue. similar conditions on CAN-FD connected expansion boards did not cause the same handling to be invoked. The event system is designed to unify the handling of these (often erroneous) conditions across Duet 2 and Duet3 mainboards, and CAN-FD expansion boards.

*Note that the event system is still under development,  Currently, RRF does not attempt to turn off power to the whole machine if the user does not respond to the heater fault. We plan to reinstate this or a similar function in release 3.4.0RC1.*

# Events

An event is one of the following:

* Heater fault
* Filament error     
* Driver stall     
* Driver error (e.g. over temperature shutdown, short-to-ground, or closed loop driver error failure to achieve the requested position)
* Driver warning (e.g. over temperature warning, or phase disconnected)

Other event types may be added in future, for example MCU over-temperature on a main board or expansion board, or over- and under-voltage.

The following are not currently treated as events:

* Triggers (so that a trigger does not block an event)     
* Main board power failure (because it requires immediate action even if an event is already being processed)

# Event properties

An event has the following properties:

* The type of the event, e.g. heater_fault, filament_error.
* The device number concerned, e.g. the heater number, extruder number, or driver number.
* The CAN address of the board that detected the event .
* For some event types, an additional parameter that may indicate the event subtype, for example the type of heater fault.
* For some event types, a short text string giving additional details. This is limited to 56 characters so that it can be sent as part of a CAN message.

Events also have the following derived properties:

* A full text string can be generated that takes account of all the properties of that event
* A priority, which is a fixed function of the event type.

# Processing events

Events are added to an even queue running on the mainboard and processed sequentially, and inserted according to their priority. Higher priority events can jump the queue, but do not jump ahead of an event that's part way through processing. Once an event is processed its removed from the queue.

The normal action taken when processing an event is to attempt to run the macro for that event. The handler macro must be in the system files folder (i.e. “/sys” or as set by M505) and have the same name as the event type, with a ‘g’ extension added. Parameters are passed as follows:

* D parameter (non-negative integer): the device number associated with the event. For events relating to stepper drivers this is just the local driver number, not including the CAN address of the board.
* B parameter  (non-negative integer): the CAN address of the board that the device is on
* P parameter  (non-negative integer): additional information about the event, e.g. the subtype of a heater fault or a filament error
* S parameter: the full text string describing the fault (the same string that is written to the log file, if the event is logged). This is intended to be suitable to show to the user.

If the macro file is not found then default processing occurs as shown in the table. Where "Pause print" is shown, if the print is already paused or pausing then no additional pause is added.


| Event type & macro file name | D macro parameter | P macro parameter | B macro parameter | Default action if macro file not found | Log level |
|:---|:---|
| heater_fault | Heater # | Heater fault type code | CAN address of board controlling the heater | Pause print using pause.g and inform user via message box | Error |
| driver_error | Local driver # | Lower 16 bits of driver status word | CAN address of board with driver | Pause print without running pause.g and inform user vis message box | Error |
| filament_error | Extruder # | Filament error type code | CAN address of board hosting the filament monitor | Pause print using pause.g and inform user via message box | Error |
| driver_stall | Local driver # | 0 | CAN address of board with driver | Inform user via console and continue | Warning |
| driver_warning | Local driver # | Lower 16 bits of driver status word | CAN address of board with driver | Inform user via console and continue | Warning |

Once processing is completed the event is removed from the queue. If an event of a particular type in in the queue and that condition happens again on the same device, a second event of the same type/device is not added to the queue.

# Changes in behaviour since RRF 3.3

The following behaviour changes may require configuration and macro file changes to be made when upgrading from RRF 3.3 to 3.4.

* In RRF 3.3 when a filament error occurred RRF would first look for filament_error#.g where # is the extruder number, and fall back to filament_error.g if that was not found. RRF 3.3 would pause the print before running the filament_error file. RRF 3.4 only looks for filament_error.g but it passes the extruder number in the D parameter, and it does not pause the print.
* In RRF 3.3 if the M915 R2 parameter was given then on a motor stall RRF would pause the print, and if R3 was given it would pause the print, run rehome.g, then resume the print. In RRF 3.3 both R2 and R3 do the same thing: they create an event. File rehome.g is not used. Therefore to achieve the effect of R3 you need to rename rehome.g to driver_stall.g. To achieve the effect of R2 you need to create driver_stall.g, put a M25 command in it, and use the parameters to create a message to the user.
* RRF 3.3 reports driver errors (e.g. over temperature or short-to-ground) to the user but does not stop the print. RRF 3.4 attempts to run driver_error.g instead. If it is not found then it pauses the print **without** running pause.g and notifies the user via a message box.
* RRF 3.3 reports driver warnings (e.g. over temperature, or phase disconnected) to the user. RRF 3.4 attempts to run driver_warning.g. If that file is not found then it just notifies the user via the console.