---
title: Duet3D Magnetic Encoder
description: A Magnetic encoder for sensing motor position and rotation. Uses a hall effect sensor to detect the rotation of a diametrically-magnetised disc magnet attached to the motor shaft at the rear of the motor
published: true
date: 2023-04-11T17:51:43.791Z
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

# Configuration

# Revision history

# Tabs {.tabset}

## v0.3

* Changed the ribbon cable header to 2x5 to match the SPI encoder input header on the 1HC
* Routed the A/B/N signals to he header (currently not used)

## v0.2 - Prototpye

Initial prototype