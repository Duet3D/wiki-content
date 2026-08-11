---
title: Duet3D Magnetic Encoder
description: A Magnetic encoder for sensing motor position and rotation. Uses a hall effect sensor to detect the rotation of a diametrically-magnetised disc magnet attached to the motor shaft at the rear of the motor
published: true
date: 2026-08-11T14:59:29.451Z
tags: 
editor: markdown
dateCreated: 2023-04-11T17:51:43.791Z
---

# Introduction

The Duet3D magnetic encoder senses motor position and rotation using a hall effect sensor to detect the rotation of a diametrically-magnetised disc magnet attached to the motor shaft at the rear of the motor.

![magneticencoderv0.3_05_s.jpg](/hardware/magnetic_encoder/magneticencoderv0.3_05_s.jpg =400x)

# Features

## Specification

* 14 Bit On-Axis Magnetic Rotary Sensor: AS5047D or AS5047P^1^
* Maximum sensor RPM supported 14500 ^2^
* Supplied with a 4mm thick, 6mm diameter magnet
* SPI output for communication with a Duet 3 Expansion 1HCL controller (supplied with a 150mm ribbon cable for this purpose)
* (v2.0 and later) 5V operation - compatible with the Duet 3 Expansion 1HCL controller via the use of the supplied SPI Buffer board.
* (v1.0 and earlier) 3.3V operation - compatible with the Duet 3 Expansion 1HCL controller (Can be modified for 5V operation for other applications. 
* Maximum ambient temperature: 85°C

^1^ AS5047D/P sensors are interchangeable in this application.
^2^ Max system RPM possible depends on the Duet 3 Expansion 1HCL controller closed loop speed and the maximum motor RPM


## SPI Buffer

V2.0 Magnetic Encoder boards are supplied with an SPI buffer board, see here mounted on the 1HCL box header
![1hcl_and_mag_enc_v2.0_buffer_board.jpg](/hardware/magnetic_encoder/1hcl_and_mag_enc_v2.0_buffer_board.jpg =600x)

The v2.0 magnetic encoders have a buffer on SPI_MISO, and expect 5V signals on MOSI_SCK and CS lines. the buffer board provides this and drops the MISO signal back to 3.3V

> The SPI Buffer board **MUST** be used with v2.0 Magnetic encoders. It **MUST NOT** be used with v1.0 Magnetic encoders.{.is-warning}


### Limitations on use

* Keep away from strong external magnetic sources, as they may cause errors in sensor readings. 

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
| 1 | SPI Buffer PCB, v2.0 only | Included |
| 1 | 6mm diameter x 4mm long diametrically-magnetised magnet | Included. A 6mm magnet was chosen because it fits even in recessed shaft NEMA 17 motors. |
| 4 | 0.5mm M3 nylon washer | Included |
| 4 | 1mm M3 nylon washer | Included |
| 4 | 2mm M3 nylon washer | Included |
| 4 | 3mm M3 nylon washer | Included |
| 1 | 150mm ribbon cable 10 way| Included |
| 1 | LOCTITE 648, 435, a cyanoacrylate adhesive or equivalent| Not provided. Select an adhesive that is tolerant of slight oil contamination potentially present at the back of the motor and is heat tolerant enough for your application. The magnet is not under significant load, but is exposed to the motor vibrations. |
| 1 | Printed jig for centring the magnet | Not provided, see below |
| 2 | M3 screws to mount PCB on rear of motor| Not provided. The length of bolt required depends on motor length, see step 3 below |

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

### Notes on magnet placement

There are three main factors that affect the strength and linearity of the magnetic field that the sensor detects, listed below. These notes are based on the sensor manufacturer's [application note here](https://look.ams-osram.com/m/1f9fec31c21d8f4d/original/AnglePositionOnAxis-AN000271.pdf){target=_blank}.

<details>
<summary>Off-axis magnet placement and encoder errors</summary>
  The linearity of the detected magnetic field changes with mechanical displacements in the x and y direction (ie if the magnet is off-centre). Ideally, the magnet should rotate over the sensor chip such that the centre of the magnet, the centre of rotation, and the centre of the chip, are in one vertical line. The better the magnet is centred on the shaft, the lower the encoder errors will be. See section 1.5 and 1.6 of the application note.

  > The integral non linearity (INL) is one of the important parameters for position sensors in general. This parameter specifies the effective angle error from the total system. The magnetic position sensor system performance is mainly dependent on magnetic and mechanical constraints. Electrical errors from position sensor IC play mostly a minor role.
  
  [![Figure 7 Non Linearity of the angle output](/hardware/magnetic_encoder/mag_enc_installation_notes_04.png)](/hardware/magnetic_encoder/mag_enc_installation_notes_04.png){target=_blank}

  > The non-linearity parameter represents the difference between the measured and the ideal line. Offset angle components are not considered in this calculation. (Best-Line-Fit method).

  [![Figure 12 Non-Linearity change over horizontal misalignment](/hardware/magnetic_encoder/mag_enc_installation_notes_03.png)](/hardware/magnetic_encoder/mag_enc_installation_notes_03.png){target=_blank}
  
  A slightly wider diameter magnet will work slightly better for the same flux density, up to about 10mm. Duet3D chose a 6mm magnet because that fits even in recessed shaft NEMA 17 motors.
  
  [![Figure 13 Non-Linearity error over displacement](/hardware/magnetic_encoder/mag_enc_installation_notes_06.png)](/hardware/magnetic_encoder/mag_enc_installation_notes_06.png){target=_blank}
  
</details>

<details>
<summary>Distance from magnet to sensor</summary>
  The vertical distance from the sensor surface to the magnet surface (air gap) is an
important parameter for the linearity of the system. The strength of the magnetic field reduces as the distance between magnet and sensor increases. For the supplied 6mm magnet, there should be an air gap of no more than 1mm between magnet and sensor. See section 1.6.1 of the application note.
  
  [![Figure 9 shows the difference between 6 and 8 mm diameter magnet](/hardware/magnetic_encoder/mag_enc_installation_notes_01.png)](/hardware/magnetic_encoder/mag_enc_installation_notes_01.png){target=_blank}
  
  [![Figure 10 Nonlinearity and Automatic Gain Control (AGC) value over air gap. D6H2.5 magnet.](/hardware/magnetic_encoder/mag_enc_installation_notes_02.png)](/hardware/magnetic_encoder/mag_enc_installation_notes_02.png){target=_blank}

  Figure 10 shows the tendency of the non-linearity choosing different air gaps. In addition the automatic gain control (AGC) value is shown. This value is increasing with increasing distance, reaching the limit at larger air gaps. The magnetic position sensor is still operating in this area, but with increased noise output.
  
</details>

<details>
<summary>Magnets and temperature</summary>
  The temperature will also affect the magnetic field, with higher temperatures reducing the strength of the magnetic field. Check the maximum temperature the motor will get to and check the specification of the magnet. Duet3D supplies high temperature magnets for mounting on the motor shaft, which should not be needed in most cases, but could help if the motor is running hot. See section 2.6 of the application note.

  [![Figure 18: Magnetic flux density Bz of N35H magnet at different temperature (same magnet)](/hardware/magnetic_encoder/mag_enc_installation_notes_05.png)](/hardware/magnetic_encoder/mag_enc_installation_notes_05.png){target=_blank}

</details>

## Step 3 - Mount Magnetic Encoder PCB on motor

* Remove two of the four m3 threaded bolts from the motor from opposing corners.

![Image showing the Nema 17 motor with two of the 4 bolts through the back of the motor removes, also shown are the Magnetic encoder PCB, two long M3 bolts and 4 sets of nylon washers of different thicknesses.](/hardware/magnetic_encoder/mag_enc_v0.3_installation_step3.png)

* Fit the magnetic encoder board to the back of the motor, using the nylon washers as spacers so the encoder chip is ~1mm from the magnet.

![Image showing the encoder board fitted to the back of the motor using nylon spacers and the longer M3 bolts. the image shows that the Magnetic encoder chip mounted on the baord is ~1mm for the magnet.](/hardware/magnetic_encoder/mag_enc_v0.3_installation_step4.png)

## Step 4 - Connect to Duet 3 Expansion 1HCL

# Tabs {.tabset}

## v2.0 Magnetic encoder

### Duet 3 Expansion 1HCL v2.0 and later

Connect the encoder board to the Duet 3 Expansion 1HCL to the Magnetic Encoder v2.0 Buffer board using a 10-way straight-through ribbon cable. Fit the buffer board into the 10-way box connector on the 1HCL.

![A Duet 3 1HCL v2.0 with the SPI buffer board fitted, connected to a Magnetic Encoder board via a 20cmm ribbon cable](/hardware/magnetic_encoder/1hcl_and_mag_enc_v2.0.jpg =800x)

>Ensure that the SPI buffer board is placed centrally in the socket on the 1HCL. It is possible to insert it shifted left or right by a pair of pins.{.is-warning}

>The buffer board must be mounted on the 1HCL box header and not on the Magnetic Encoder board.{.is-warning}

### Duet 3 Expansion 1HCL v1.0 and earlier

As above, but note that the SPI Encoder Input connector on the 1HCL v1.0 is orientated differently from the 1HCL v2.0, and the SPI buffer board should be connected 180° rotated compared to the picture above. See the [1HCL wiring diagrams here](https://docs.duet3d.com/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL#wiring-diagram){target=_blank}.

## v1.0, v0.3 Magnetic encoder

* Connect using the ribbon cable and a stepper motor cable to the 1HCL.
* The ribbon cable and connector are keyed, so cannot be inserted the wrong way around. 
  Note there is a difference between the 1HCL v1.0 and v2.0; the SPI Encoder Input connector is rotated 180°.
* Move on to configuration.

![mag_enc_v0.3_installation_step3.png](/hardware/magnetic_encoder/mag_enc_v0.3_installation_step5.png)

The above picture shows a 1HCL v1.0 connected to a magnetic encoder v1.0.

# Configuration

This should be read in conjunction with the [Duet 3 Expansion 1HCL documentation](/Duet3D_hardware/Duet_3_family/Duet_3_Expansion_1HCL#commissioning){target=_blank}

## Config.g changes

[M569.1](/User_manual/Reference/Gcodes/M569_1){target=_blank} is used to configure the closed loop driver. 

The T parameter specifies that the encoder used is the Duet 3 Magnetic encoder 

Here is a sample excerpt from a config.g file for RRF 3.5 to drive the X motors from 1HCL board configured at CAN addresses 50, with a Duet 3 Magnetic encoder.

```
M569.1 P50.0 T3 E1:2 R100 I0 D0 ; Configure the Duet 3 Expansion 1HCL board at CAN address 50 with a Duet 3 magnetic encoder, warn if 1 full step threshold exceeded, error if 2 full steps threshold exceeded. 
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

## v2.0

* Connected the 5V pin from the 1HCL header (pin 10 on box header).
* Removed the "cut for 5V operation" jumper - The AS5047x now operates in 5V mode.
* Added a 5V buffer to the SPI_MISO line
* Requires the use of the "SPI Buffer" board supplied with v2.0 Magnetic Encoder boards which buffer the MOSI,SCLK,and CS signals from the 1HCL to 5V and handles the BUFFERED MISO line back to the 1HCL.

## v1.0

* Added a footprint for the ABN signals on their own header (not populated) for alternative uses/testing.

## v0.3

* Changed the ribbon cable header to 2x5 to match the SPI encoder input header on the 1HC
* Routed the A/B/N signals to the header (currently not used)

## v0.2 - Prototype

Initial prototype