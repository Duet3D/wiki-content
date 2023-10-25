---
title: Duet3D Magnetic Encoder
description: A Magnetic encoder for sensing motor position and rotation. Uses a hall effect sensor to detect the rotation of a diametrically-magnetised disc magnet attached to the motor shaft at the rear of the motor
published: true
date: 2023-10-25T08:18:31.818Z
tags: 
editor: markdown
dateCreated: 2023-04-11T17:51:43.791Z
---

# Introduction

The Duet3D magnetic encoder senses motor position and rotation using a hall effect sensor to detect the rotation of a diametrically-magnetised disc magnet attached to the motor shaft at the rear of the motor.

![magneticencoderv0.3_05_s.jpg](/hardware/magnetic_encoder/magneticencoderv0.3_05_s.jpg =400x)

# Features

## Specification

* 14 Bit On-Axis Magnetic Rotary Sensor
* Maximum sensor RPM supported 14500 ^1^
* Supplied with a 4mm thick, 6mm diameter magnet
* SPI output for communication with a Duet 3 Expansion 1HCL controller (supplied with a 150mm ribbon cable for this purpose)
* 3.3V operation - compatible with the Duet 3 Expansion 1HCL controller (Can be modified for 5V operation for other applications. 

^1^ Max system RPM possible depends on the Duet 3 Expansion 1HCL controller closed loop speed and the maximum motor RPM

## Supported hardware

### Duet 3 hardware
The Duet 3 Magnetic Encoder is designed to be paired with a [Duet 3 Expansion 1HCL controller](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL).

### Compatible motors

**NEMA17:** All NEMA17 motors with an exposed rear shaft no more than 3.5mm below the back of the motor should be compatible. If the exposed rear shaft projects from the back of the motor then a mounting solution that holds the sensor ~1mm from the magnet mounted on the shaft will be required.

**NEMA23 and other sizes:** An adaptor plate/mounting system would be required to use the Duet 3 Magnetic Encoder with other motors.


## Dimensions

[![Line drawing showing the outer dimensions of the Duet 3 Magnetic Encoder, along with the mounting hole positions](/hardware/magnetic_encoder/duet3_magnetic_encoder_v0.3_d1.1_dimensions.png =400x)](/hardware/magnetic_encoder/duet3_magnetic_encoder_v0.3_d1.1_dimensions.png){target=_blank}

### 3D Model

The STEP file is [hosted here](https://github.com/Duet3D/Duet3-Magnetic-Encoder/tree/main/Duet%203%20Magnetic%20Encoder%20v0.3){target=_blank}.


# Installation

## Required parts

| Qty | Item | Remarks |
|:---|:---|
| 1 | Magnetic Encoder PCB | Included |
| 1 | 6mm diameter x 4mm long diametrically-magnetised magnet | Included |
| 4 | 0.5mm M3 nylon washer | Included |
| 4 | 1mm M3 nylon washer | Included |
| 4 | 2mm M3 nylon washer | Included |
| 4 | 3mm M3 nylon washer | Included |
| 1 | 150mm ribbon cable 10 way| Included |
| 1 | LOCTITE 648, 435, a cyanoacrylate adhesive or equivalent| Not provided. Select an adhesive that is tolerant of slight oil contamination potentially present at the back of the motor and is heat tolerant enough for your application. The magnet is not under significant load, but is exposed to the motor vibrations. |
| 1 | Printed jig for centring the magnet | Not provided, see below |
| 2 | M3 bolts to mount PCB on rear of motor| Not provided. The length of bolt required depends on motor length, see step 3 below |

### Printed Jig

In order to ensure the magnet is centered on the motor shaft, a printed jig can assist with the magnet installation.
The STL and OpenSCAD design for the jig are [available here](https://github.com/Duet3D/Duet3-Magnetic-Encoder/tree/main/Magnet%20Alignment%20Jig){target=_blank}
The jig can be printed using a reasonably tuned FDM 3d printer with a 0.4mm nozzle.

![Render of the magnetic Encoder magnet alignment jig](/hardware/magnetic_encoder/mag_enc_v0.3_installation_jig.png)


## Step 1 - Print, test fit jig and magnet

* Test fit the printed jig to the motor, and test that the magnet can move freely in the central alignment hole without significant lateral movement

![Image showing a Nema17 motor, the 3d printed magnet alignment jig and the magnet](/hardware/magnetic_encoder/mag_enc_v0.3_installation_step1.png)

## Step 2 - Glue in magnet

* Apply adhesive to the magnet, glue to rear motor shaft and leave adhesive to cure. 
* Follow the manufacturer's instructions for the adhesive regarding curing time, temperature and any pressure required.

![Image showing the before and after of the magnet placed in the alignment jig on the back of the Nema17 motor](/hardware/magnetic_encoder/mag_enc_v0.3_installation_step2.png)

## Step 3 - Mount Magnetic Encoder PCB on motor

* Remove two of the four m3 threaded bolts from the motor from opposing corners.

![Image showing the Nema 17 motor with two of the 4 bolts through the back of the motor removes, also shown are the Magnetic encoder PCB, two long M3 bolts and 4 sets of nylon washers of different thicknesses.](/hardware/magnetic_encoder/mag_enc_v0.3_installation_step3.png)

* Fit the magnetic encoder board to the back of the motor, using the nylon washers as spacers so the encoder chip is ~1mm from the magnet.

![Image showing the encoder board fitted to the back of the motor using nylon spacers and the longer M3 bolts. the image shows that the Magnetic encoder chip mounted on the baord is ~1mm for the magnet.](/hardware/magnetic_encoder/mag_enc_v0.3_installation_step4.png)

## Step 4 - Connect to Duet 3 Expansion 1HCL

* Connect using the ribbon cable and a stepper motor cable to the 1HCL.
* Move on to configuration.

![mag_enc_v0.3_installation_step3.png](/hardware/magnetic_encoder/mag_enc_v0.3_installation_step5.png)

# Configuration

This should be read in conjunction with the [Duet 3 Expansion 1HCL documentation](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL#commissioning){target=_blank}

## Config.g changes

[M569.1](/User_manual/Reference/Gcodes/M569_1){target=_blank} is used to configure the closed loop driver. 

The T parameter specifies that the encoder used is the Duet 3 Magnetic encoder 

Here's an sample excerpt from a config.g file for RRF 3.5 to drive the X motors from 1HCL board configured at CAN addresses 50, with a Duet 3 Magnetic encoder.

```
M569.1 P50.0 T3 E1:2 R100 I0 D0 ; Configure the Duet 3 Expansion 1HCL board at CAN address 50 with a Duet 3 magnetic encoder, warn if 1 fullstep threshold exceeded, error if 2 full steps threshold exceeded. 
M569 P50.0 D4 S1 ; Configure the motor on the Duet 3 Expansion 1HCL controller at can address 50 as being in closed-loop drive mode (D4) and not reversed (S1) 
M584 X50.0; set X drivers
```
Note the initial PID values shown will need to be tuned to the particular motor.

## Magnetic Encoder Tuning.

The [M569.6](/User_manual/Reference/Gcodes/M569_6){target=_blank} command is used to tune the motor and encoder pair, e.g.
```
M569.6 P51.0 V2 ; conduct magnetic encoder calibration on move on closed loop driver on Duet 3 Expansion 1HCL board with magnetic encoder at address 50.
```

This can be checked with V3 and cleared with V4, see [Runtime Tuning](/User_manual/Tuning/Duet_3_1HCL_tuning#runtime-tuning ){target=_blank}.

It must be run once for a new 1HCL, motor, magnet or magnetic encoder board. The values are stored in flash memory, so it does not need to be run again unless the system changes (i.e. you replace the Duet 3 Expansion 1HCL controller, motor, magnet or magnet encoder board, or you remove and re-fit the magnetic encoder board or the magnet).

> Note: this initial tuning must be run with the motor unloaded for best results {.is-warning}

# Revision history

# Tabs {.tabset}

## v0.3

* Changed the ribbon cable header to 2x5 to match the SPI encoder input header on the 1HC
* Routed the A/B/N signals to he header (currently not used)

## v0.2 - Prototpye

Initial prototype