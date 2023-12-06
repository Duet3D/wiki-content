---
title: Community Projects
description: Hardware and software projects started by community members to extend the Duet ecosystem. 
published: true
date: 2023-12-06T13:48:01.849Z
tags: 
editor: markdown
dateCreated: 2021-12-03T21:54:58.958Z
---

The [Duet3D community](https://forum.duet3d.com/){target=_blank}  has created a wide range of DIY software and hardware add-ons to extend the capabilities of the Duet hardware and RepRapFirmware.

Here you'll find a listing (in no particular order) of the various projects with a short description and link to further information.

If you know of a project that is not on the list, but should be, [post about it here.](https://forum.duet3d.com/topic/14269/community-projects-wiki){target=_blank} 

# Machine configurations and macros

Below are a number of repositories for example machine configurations. These are suggestions only; please use carefully, and understand that they may need changes to work with your machine, and may damage it if used incorrectly.

* [Duet3D machine configs](https://github.com/Duet3D/RRF-machine-config-files) Configurations for: Big Fast Delta, Creality Ender 5, Creality Ender 3 Pro, Creality Ender 5 Plus, DBot CoreXY, E3D Tool Changer, FlashForge Creator Pro, Jubilee, Kossel, Prusa MK3s, RailCore II 300ZL, RepRapPro Ormerod 1, Robotdigg SCARA, Vivedino Troodon 300
* [Team Gloomy user configurations](https://github.com/TeamGloomy/LPC-STM32-RRFUserConfigs/tree/master) Configurations for machines running non-Duet3D boards (SKR, BTT, Mellow, MKS etc), but can be used as a guide. Configurations for: Anycubic i3 Mega S, AnyCubic Predator, Caribou MK3S, Cetus MK2 Extended, Creality Ender 3, Creality Ender 5 Plus, Da Vinci Jr, FLSUN Q5, IDEX, K40 CO2 Laser, Micron120, Micron120, Prusa MK2S, SecKit-SK-GO, Sovol SV 01, Tenlog D3, V-Minion, Voron Trident 350, Voron V0.1
* [pfn's Voron 2.4 config](https://github.com/pfn/voron2-rrf-config)
* [jay_s_uk's Voron 2.4 config](https://github.com/jaysuk/jays_voron_0.2) Triple boot (Klipper, Marlin, RRF) Voron configuration files.
* [OwenD's macro library](https://github.com/owendare/RepRapFirmware-Macros) A large collection of macros and conditional g-code examples.

# Hardware Projects

* [DuetBuddy](https://forum.duet3d.com/topic/14134/){target=_blank}  by zapta. *Uses an  M5Stack to remotely monitor and display the print progress and changes colors as the duet state changes (in this case from print to pause, cancel and idle).*
* [Indirect bearing laser filament monitor](https://forum.duet3d.com/topic/10042/){target=_blank}  by brunofporto. *A new casing for the Duet3D Laser Filament Monitor to hold two bearings with a compliant mechanism spring to try indirect reading of filament extrusion. The sensor will read the bearing surface instead of the filament and I hope this should end the issues with different filament colors and types.*
* [PrintEye](https://forum.duet3d.com/topic/12197/){target=_blank}  by EasyTarget. *PrintEye is a minimalist information panel for Duet boards; it plugs into the PanelDue UART port, taking power from there and sending M408 S0 once a second, and otherwise waiting for a reply and updating the display based on their contents.*
* [JogBox](https://forum.duet3d.com/topic/14204/){target=_blank}  by CJD. *Connects to the PanelDue Serial port connector on the Duet 2 board and provides a rotary encoder which can be used to jog the CNC machine with the control needed. It also includes 8 buttons which, when pressed, call macros to do any user defined tasks required.*
* [RepPanel](https://forum.duet3d.com/topic/15198/){target=_blank}  by seeul8er. *RepPanel is a user interface for Duet based 3D printers. It is a cheaper alternative to the already available PanelDue. It uses the ESP32 and is optimized for 3.5" displays. If you source the hardware yourself it costs around 25€-30€.*
* [Stratadue](https://forum.duet3d.com/topic/15482/){target=_blank}  by AJ Quick. *Stratadue is an adapter board that connects a Duet 2 (Wifi/Ethernet) to a Stratasys 3D printer such as a uPrint (SE/Plus), Dimension 768 or 1200 (BST, SST, Elite) and possibly some Fortus machines.*
* [Laser Filament Monitor with Endstop](https://forum.duet3d.com/topic/15799/){target=_blank}  by oseiler. *A microswitch is easily added to the laser sensor pcb and the laser sensor can be turned off for filaments that the laser doesn't detect that reliably while the switch will still detect when running out of filament.*
* [Soft ATXvPower Control Modifications](https://forum.duet3d.com/topic/17933/){target=_blank}  by mitch. *Two hardware modifications and included scripts to enable software control and automatic shutdown of the Duet 2 boards.*
* [Wireless PanelDue](https://forum.duet3d.com/topic/10005/){target=_blank}  by AndreS. *Module to make the PanelDue wireless to the Duet*
* [Arduino Nano based 12864 LCD controller for RRF](https://forum.duet3d.com/topic/19107/){target=_blank}  by deathbydeltabot. *Due to the limitations of the LPC port of RRF, I wrote some software that runs on an Arduino (Mini Pro and Nano tested) to drive a RepRap Discount full graphics LCD in a similar in a similar manner as PanelDue.*
* [12864 Display ST7920 Level Shifter for D3 Mini5+](https://forum.duet3d.com/topic/23034/){target=_blank}  by DIY-O-Sphere. *Level Shifter to allow a 12864 display to work with the mini5+*
* [DIY Fan Smoother](https://forum.duet3d.com/topic/26615/){target=_blank}  by zapta. *Converts the PWM power to proportional DC voltage that is more compatible with the requirements of brushless DC fans.*
* [Stepper motor analyzer](https://forum.duet3d.com/topic/23461/){target=_blank}  by zapta. *Senses the current of the stepper motor coils, performs a simple analysis in realtime and display useful information and graphs on a small screen. Uses a Raspberry Pico. Based on an [earlier version](https://forum.duet3d.com/topic/12049/){target=_blank} , also by zapta.*

# Software Projects

* [DueUI](https://forum.duet3d.com/topic/13970/){target=_blank}  by gtj0. *DueUI is an alternate web-based user Interface for the Duet family of products. It's not meant to replace the full fledged DuetWebControl (DWC) interface but rather to complement it.*
* [DuetRRF-Timelapse](https://forum.duet3d.com/topic/6058/){target=_blank}  by resam. *A tool to create timelapse videos with pictures on every layer change, using a Duet controller, a Telnet connection from a Raspberry Pi (or other PC), and a webcam (to get still pictures over a URL)*
* [PythonDSF](https://forum.duet3d.com/topic/13612/){target=_blank}  by Danal. *module/class to allow Python programs on the Pi attached to a Duet3 to interact with the printer.*
* [MultiDuetWebMonitor](https://forum.duet3d.com/topic/4117/){target=_blank}  by Danal. *Monitor multiple Duet printer statuses from a single browser tab.*
* [Timelapse Implementation for Pi Zero](https://forum.duet3d.com/topic/12050/){target=_blank}  by doryo. *This is my implementation for automated layer lapse using the awesome duetwifi board* 
* [RepRapNotify](https://forum.duet3d.com/topic/11088/){target=_blank}  by Torin. *solution for missing notifications/actions for Duet upon change of print status with built in support for pushbullet/pushover/email*
* [RepRapFirmwareFileManager](https://forum.duet3d.com/topic/10880/){target=_blank}  by wilriker. *Command line tool used to perform all file and directory actions available via RepRapFirmware's HTTP interface*
* [Maximum Acceleration Calculator](https://forum.duet3d.com/topic/6853/){target=_blank}  by wilriker. *enter your details and the calculator will provide the maximum possible acceleration that your motor could handle*
* [Extruder Microstep Calculator](https://forum.duet3d.com/topic/6156/){target=_blank}  by wilriker. *calculate the optimal/minimum extruder microstep setting to avoid missed steps*
* [goduetapiclient (DSF)](https://forum.duet3d.com/topic/12351){target=_blank}  by wilriker. *Port of DuetAPICient to Go*
* [Exec On MCode DSF Extension](https://forum.duet3d.com/topic/13194){target=_blank}  by wilriker. *A small DSF extension to listen for a user-defined M-Code (e.g. M7722) to then run a system command (e.g. poweroff).*
* [ObjectSkip DSF Extension](https://forum.duet3d.com/topic/12352/){target=_blank}  by wilriker. *DuetSoftwareFramework extension to skip objects in a print because e.g. they are ripped off the build plate or whatever reason you might have to just skip an object.*
* [Tuning Macros Menu System](https://forum.duet3d.com/topic/6181/){target=_blank}  by Phaedrux. *Complete set of menus for tuning acceleration, jerk, pressure advance, and firmware retraction values on the fly.*
* [Duet Telegram Bot](https://forum.duet3d.com/topic/7745/){target=_blank}  by Martinj. *Telegram bot for controlling and receiving notifications from a Duet electronics board.*
* [RJ TextEditor Gcode Syntax Highlighting](https://forum.duet3d.com/topic/14637/){target=_blank}  by OwenD. *RJ Texted now has a syntax file available I did that contains the RRF object model and GCode references.*
* [UltimateDuetMenuSystem](https://forum.duet3d.com/topic/14774/){target=_blank}  by JadonM. *Fully-featured menu system for the Reprap Discount 12864 display on Duet Maestro*
* [Tool Alignment Machine Vision](https://forum.duet3d.com/topic/15026/){target=_blank}  by Danal. *This script is intended to allow Duet V2 and V3 tool-changing printers to align any number of tools that have a recognizable circular nozzle via full automation. It will ultimately generate the correct G10 commands and write them to the printer in a file that can be M98 included in config.g.*
* [Send GCode to PanelDue Port with Checksums](https://forum.duet3d.com/topic/15134/){target=_blank}  by Bearer. *Generate valid checksums for sending gcodes over the PanelDue port.*
* [DuetLapse](https://forum.duet3d.com/topic/15297/){target=_blank}  by Danal. *Time Lapse camera support for Duet based 3D printers. Works with firmware V2 or V3 (including stand alone V3 or Pi based V3). Triggers images based on time, layer change, or pause.*
* [DuetLapse3](https://forum.duet3d.com/topic/20932/){target=_blank}  by stuartofmt. *A continuation and extension of DuetLapse by Danal*
* [PrusaSlicer Tool Changer Post Processor](https://forum.duet3d.com/topic/15589/){target=_blank}  by mkudzia. *The script includes the following enhancements: Robust prime tower generation, Smart Active/Idle tool-head temperature management, PCF speed management, Validation/stripping of the GCode (i.e. Merlin M900, mapping of the fan ranges etc.)*
* [SMS Notifier with Image](https://forum.duet3d.com/topic/15733/){target=_blank}  by keyz182. *a script to notify on print completion. It runs on a Pi (though should run on any \*nix connected to the Duet via USB), and uses Twilio to send SMS, and AWS S3 to store images.*
* [Cura/PrusaSlicer Script to Automatically Probe Only Printed Area](https://forum.duet3d.com/topic/15302/){target=_blank}  by CCS86. *You pass it a single parameter (mesh spacing value), and it parses the first layer gcode for min/max X and Y coordinates, and then replaces the M557 line in your start gcode.*
* [DuetTestSPI Tool](https://forum.duet3d.com/topic/15853/){target=_blank}  by Danal. *where SPI problems are suspected, I decide to whip together a utility that would combine all the suggested tests, give "Duet relevant" messages to be more useful to an end user, and etc.*
* [RepRapFirmware Configurable Printer Profile for IceSL Slicer](https://forum.duet3d.com/topic/16563/){target=_blank}  by bot. *IceSL is a pretty neat slicer that has some incredible features. It also allows total customization of GCode output with LUA scripting.*
* [3D Gcode Viewer Integrated with DWC](https://forum.duet3d.com/topic/11907/){target=_blank}  by Sindarius. *View real time 3D gcode preview in DWC*
* [M104 to G10 post processing script](https://forum.duet3d.com/topic/17001/){target=_blank}  by Luke'sLaboratory. *Script to change the M104 heater commands generated by the slicer into G10 heater commands more suitable for tool changers and multi extrusion printers.*
* [Fusion 360 FDM/FFF Profile for RRF](https://forum.duet3d.com/topic/14872/fusion-360-fdm-fff-slicing?_=1593292741797){target=_blank}  by littlehobbyshop. Post-processing profile for Fusion 360 slicer to work with RepRapFirmware syntax.
* [Single Macro Filament Handling with Conditional Gcode](https://forum.duet3d.com/topic/17566/) by Kolbi. *a single user selectable macro to handle all filament loading/unloading/changing.*
* [Cura-DuetRRFPlugin](https://github.com/Kriechi/Cura-DuetRRFPlugin){target=_blank}  by resam. *Plugin for Cura that adds output devices for a RepRapFirmware printer running a Duet 2 Wifi, Duet 2 Ethernet, Duet 2 Maestro, or Duet 3 motion controller: "Print", "Simulate", and "Upload".*
* [Bash script file dump](https://forum.duet3d.com/topic/17980/){target=_blank}  by Kolbi. *simple bash script that outputs the contents of each file within the designated directory to terminal. This can be useful for troubleshooting, code verification, sharing, etc.*
* [Duet Tool Change Script](https://forum.duet3d.com/topic/18205/creating-vitual-tools-for-color-change?_=1599145618879){target=_blank}  by Jacotheron. *Post Processing python script to facilitate single extruder multi color filament swapping*
* [Node-Red Mobile Sized Status Dashboard](https://forum.duet3d.com/topic/18520/){target=_blank}  by MintyTrebor. *I wanted to be able to view both my 3d printers at a glance, control my enclosure temperature, and integrate the power control of my printers through my existing node-red home automation dashboard .*
* [Timelapse video through execonmcode](https://forum.duet3d.com/topic/19225/){target=_blank}  by oozeBot. *script to add timelapse video when using a Duet3 and Raspberry Pi.*
* [Upload GCODE to RRF(Standalone) in KISSlicer](https://forum.duet3d.com/topic/19760/){target=_blank}  by PCR. *I like the ability to upload .gcode files within a slicer. Most slicers have the feature built in. KISS does not. But with only some lines of code it is possible.*
* [MQTT4DSF](https://forum.duet3d.com/topic/19250/){target=_blank}  by MintyTrebor. *A python service for SBC's running DSF, which enables MQTT integration with DSF.*
* [NodeDSF](https://forum.duet3d.com/topic/21342/){target=_blank}  by MintyTrebor. *DSF interface nodes for Node-Red + DWC plugin [SBC]. set of custom nodes for Node-red which interface with DSF (on SBC) and enable you to create your own event driven actions.*
* [Duet Object Model to MQTT publisher](https://forum.duet3d.com/topic/21982/){target=_blank}  by keyz182. *A very basic service for DSF that'll publish the object model to MQTT. Can be consumed then by e.g. Home Assistant, NodeRED, etc.*
* [BLTouch Fast Probing Setup](https://forum.duet3d.com/topic/14544/){target=_blank}  by Hernicz. *Allow the BLTouch pin to stay deployed during the mesh creation to reduce dive height and time spent probing.*
* [VideoStreamer](https://forum.duet3d.com/topic/23759/){target=_blank}  by stuartofmt. *VideoStreamer is a simple, lightweight video streamer for USB cameras, integrated Pi cameras. It is particularly useful when you want to same video feed to be consumed by more than one application. For example you want to use a timelapse recording recording program and at the same time monitor in real time.*
* [Duet Upload plug-in for CamBam](https://forum.duet3d.com/topic/24159/){target=_blank}  by cjm. *If you use CamBam to generate g-code .nc files, you might like to try a new plug-in that allows these .nc files to be uploaded directly to a Duet3D board from CamBam’s Tools menu*
* [gLapse](https://forum.duet3d.com/topic/24566/){target=_blank}  by DanS79. *a Python based timelapse application designed to run on a Raspberry Pi, fitted with an official camera module. It differs from most timelapse applications, in that it doesn't generate a video as its output. Instead, it stores a sequence of still images that can be downloaded and used to generate a video using a 3rd party application.*
* [CNC Jogger for Android](https://forum.duet3d.com/topic/24460/){target=_blank}  by chimaeragh. *Turn your Android smartphone into a CNC pendant to jog the axis around.*
* [RRF configuration file mirroring and management](https://forum.duet3d.com/topic/25644/){target=_blank}  by pfn. *Downloads all files from a DWC URL under /sys and /macros to a specified directory. Creates a metadata file that remembers the DWC URL and digests of all files downloaded*.
* [rrgit](https://github.com/adammhaile/rrgit){target=_blank}  by adammhaile. *Allow you to clone, pull, push, and diff between the controller and local copies of the files.*

# DWC/DSF Plugins

* [Official Duet Plugin directory](https://plugins.duet3d.com/) by various. *Repository for RRF/DSF/DWC plugins, including Duet3D plugins and those by third party developers.*
* [Official Duet Plugins](https://github.com/Duet3D/DSF-Plugins/releases){target=_blank}  by Duet3D. *Various plugins that come with the main DWC release.*
* [BTN-CMD](https://forum.duet3d.com/topic/22776/){target=_blank}  by MintyTrebor. *allows you to create simple dashboard layouts with info panels, webcams, & custom action buttons. It also has a simple status event monitoring system to alert you to machine status changes - for example you can configure BtnCmd to send a telegram msg when the printer changes to "paused" to remind you to change the filament colour mid print.*
* [CNC probe functionality for DWC-CNC](https://forum.duet3d.com/topic/23830/){target=_blank}  by raymondstone. *first stab at adding in the probe functions used by workbee owners that use the ooznest (ancient) version of the DUET UI. This is intended for 3.3 and above firmware versions it will not work below that.*
* [Filament spool load cell weight measurement plugin](https://forum.duet3d.com/topic/24221/){target=_blank}  by resam. *It contains a DCS plugin to expose an HTTP endpoint, which returns a simple weight measurement in the style of approx. 762g filament left and displays that via a DWC plugin in the top menu bar in the browser - which makes it easy to prevent out-of-filament pauses or failed prints.*