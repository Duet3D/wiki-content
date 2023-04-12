---
title: Duet3D Magnetic Encoder
description: A Magnetic encoder for sensing motor position and rotation. Uses a hall effect sensor to detect the rotation of a diametrically-magnetised disc magnet attached to the motor shaft at the rear of the motor
published: true
date: 2023-04-12T12:25:56.632Z
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
* Supplied with a 4mm thick, 6mm dia magnet
* SPI output for communication with a 1HCL (supplied with a 150mm ribbon cable for this purpose)
* 3.3V operation - compatible with the 1HCL (Can be modified for 5V operation for other applications. 

^1^ Max system RPM possible depends on the 1HCL closed loop speed and the maximum motor RPM

## Supported hardware

### Duet 3 hardware
The Duet 3 Magnetic Encoder is designed to be paired with a [Duet 3 1HCL controller](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL).

### Compatible motors

**NEMA17** All nema 17 motors with an exposed rear shaft no more than 3.5mm below the pack of the motor should be compatible. If the exposed rear shaft projects from the back of the motor then a mounting solution that holds the sensor ~1mm fromm the magnet mounted on the shaft will be required.

**NEMA23 and other sizes** An adaptor plate/mounting system would be required to use the Duet3 Magnetic encoder with other motors.


## Dimensions

[![Line drawing showing the outer dimensions of the Duet 3 Magnetic Encoder, along with the mounting hole positions](/hardware/magnetic_encoder/duet3_magnetic_encoder_v0.3_d1.0_dimensions.png =400x)](/hardware/magnetic_encoder/duet3_magnetic_encoder_v0.3_d1.0_dimensions.png){target=_blank}

### 3D Model

(coming soon)

# Installation

## Required parts

| Qty | Item | Remarks |
|:---|:---|
| 1 | Magnetic Encoder PCB | included |
| 1 | 4mm thick, 6mm diameter magnet | included |
| 4 | 0.5mm M3 nylon washer | included |
| 4 | 1mm M3 washer | included |
| 4 | 2mm M3 washer | included |
| 4 | 3mm M3 washer | included |
| 1 | 150mm ribbon cable 10 way| included |
| 1 | LOCTITE 648, 435, a cyanoacrylate adhesive or equivalent| not provided, select an adhesive that is tolerant of slight oil contamination potentially present at the back of the motor and is heat tolerant enough for your application. The magnet is not under significant load, but is exposed to the motor vibrations. |
| 1 | Printed jig for centering the magnet | not provided, see below |
| 2 | M3 bolts to mount PCB on rear of motor| not provided, lenght of bolt required depends on motor length, see step 3 below|

### Printed Jig

In order to ensure the magnet is centered on the motor shaft, a printed jig can assist with the magnet installation.

The STL and OpenSCAD design for the hig are avalable here:
https://github.com/Duet3D/Duet3-Magnetic-Encoder/tree/main/Magnet%20Alignment%20Jig

The jig can be printed using a reasonably tuned FDM 3d printer with a 0.4mm nozzle.

![Render of the magnetic Encoder magnet alignment jig](/hardware/magnetic_encoder/mag_enc_v0.3_installation_jig.png)


## Step 1 - Print, test fit jig and magnet

Test fit the rpinted jig to the motor, and test that the magnetcan move freely in the central alignment hole without significant lateral movement

![Image showing a Nema17 motor, the 3d printed magnet alignment jig and the magnet](/hardware/magnetic_encoder/mag_enc_v0.3_installation_step1.png)

## Step 2 - Glue in magnet

Apply adhesive to the magnet, glue to rear motor shaft and leave adhesive to cure. follow the manufacturer's instructions for the adhesive regarding curing time, temperature and any pressure required.

![Image showing the before and after of the magnet placed in the alignment jig on the back of the Nema17 motor](/hardware/magnetic_encoder/mag_enc_v0.3_installation_step2.png)

## Step 3 - Mount Magnetic Encoder PCB on motor

* Remove two of the four m3 threaded bolts from the motor from opposing corners.


![Image showing the Nema 17 motor with two of the 4 bolts through the back of the motor removes, also shown are the Magnetic encoder PCB, two long M3 bolts and 4 sets of nylon washers of different thicknesses.](/hardware/magnetic_encoder/mag_enc_v0.3_installation_step3.png)

* fit the magnetic encoder board to the back of the motor, using the nylon washers as spacers to the encoder chip is ~1mm from the magnet.

![Image showing the encoder board fitted to the back of the motor using nylon spacers and the longer M3 bolts. the image shows that the Magnetic encoder chip mounted on the baord is ~1mm for the magnet.](/hardware/magnetic_encoder/mag_enc_v0.3_installation_step4.png)

## Step 4 - Connect to 1HCL

Connect using the ribbon cable and a stepper motor cable to the 1HCL.

![mag_enc_v0.3_installation_step3.png](/hardware/magnetic_encoder/mag_enc_v0.3_installation_step5.png)

Move on to configuration.

# Configuration

This should be read in conjuction with the [1HCL documentation](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL#commissioning){target=_blank}

## Config.g changes

[M569.1](/User_manual/Reference/Gcodes/M569_1){target=_blank} is used to configure the closed loop driver. 

The T parameter specifies that the encoder used is the magnetic 

Here's an sample excerpt from a config.g file for RRF 3.4 to drive the X and Y motors from 1HCL  boards configured at CAN addresses 50 and 51, with quadrature encoders. **Note, some parameters have changed in RRF 3.5**.

```
M569.1 P50.0 T2 C5 R100 I0 D0 ; Configure the 1HCL board at CAN address 50 with a quadrature encoder on the motor shaft that has 5 steps per motor full step. 
M569.1 P51.0 T2 C5 R100 I0 D0  ; Configure the 1HCL board at CAN address 51 with a quadrature encoder on the motor shaft that has 5 steps per motor full step. 
M569 P50.0 D4 S1 ; Configure the motor on the 1HCL at can address 50 as being in closed-loop drive mode (D4) and not reversed (S1) 
M569 P51.0 D4 S1 ; Configure the motor on the 1HCL at can address 51 as being in closed-loop drive mode (D4) and not reversed (S1) 
M584 X50.0 Y51.0 ; set X and Y drivers
M917 X0 Y0 ; Set the closed loop axes to have a holding current of zero
M350 X32 Y32 ; set steps/mm to 32 to make full use of the encoder resolution
M92 X160 Y160 ; steps/mm for a 20 tooth gt2 pulley
```
Note the initial PID values show will need to be tuned to the particular motor.


# Revision history

# Tabs {.tabset}

## v0.3

* Changed the ribbon cable header to 2x5 to match the SPI encoder input header on the 1HC
* Routed the A/B/N signals to he header (currently not used)

## v0.2 - Prototpye

Initial prototype