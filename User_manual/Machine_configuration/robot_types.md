---
title: Robot types and their specifics
description: Supported robot types with description of properties and how to configure them, prototype recommendations
published: true
date: 2022-08-22T08:39:41.616Z
tags: robot
editor: markdown
dateCreated: 2022-08-14T06:59:05.328Z
---

This page is part of multiple pages about robot configuration and usage. Please choose the [robot tag](https://docs.duet3d.com/t/robot) to see an overview.

> This wiki is new and under construction
{.is-info}


# Introduction
Robot kinematics supports different kinds of robots. Following is a description of some of them, categorized by number of joints/axes and whether they are serial/open chain or parallel/closed chain.

# 6 axis industrial robot
Configuration is made by defining the Denavit-Hartenberg parameters, see the wiki about DH.

Especially important is the identification of singularity areas, which must be avoided.

Care must be taken when the robot is powered off: the arms need protection against falling down with damaging result. The following possibilities avoid it
* using brakes at the joints
* weight balance of every joint, e.g. by using counterweights, so the force downward is below the detent torque and friction of the steppers/gears (servos don't have much detent torque and cannot be used for this method)
* protection by using springs or dampers

# 5 axis CNC

5 axis CNC has subtypes head/head, head/table, table/table and AC, AB, BC.

A 5 axis CNC is built from three cartesian axes (including the possibility to double axes, like optionally for gantry) and two rotary axes.

Each of thehe rotary axes can be installed at the spindle or at the table, resulting in 4 combinations. The possibilities have different results in respect to workpiece size, stability etc., but it is not discussed here.

It is common practice to name the rotary axes ABC matching XYZ coordinates, so if AC is used, A is parallel to the X axis and C is parallel to the Z axis when they are in 0 degree positions.

In G-Code G0, G1 the XYZ letters are cartesian coordinates and AC (or BC, AB) are the rotary angles, i. e. different units of measurement is used. An alternative G-Code uses IJK tool vectors. They are converted into AC/BC/AB angles internally by firmware.

# 4 axis palletized robot

The endpoint's horizontal orientation is ensured by mechanical parallelogram (often two connected parallelograms) based parallel arms. ABB IRB 460, Fanuc M-410, EEZYbotARM (thingiverse 1454048), MK2 plus (thingiverse 2520572) and R290 3 axis are constructions following this principle. A similar construction, but with the stepper 3 directly connected with the arm, are handled with this construction as well, like the Kuka KR 700 and Borunte BRTIRPZ. The important property is axis 4, which is parallel to the base plate by mechanical means. The 4 axis robot is in fact 5 axis with actuators at axes 1, 2, 3 and 5. A robot like ABB IRB 8700 has only a parallelogram to place the motor far from arm 3 and can be modeled with the normal robot configuration (i. e. like a 6 axis robot, but maybe less axes configured).

Advantages and disadvantes probably are
* higher stiffness and precision than a pure serial scara, probably higher payload
* three actuators are near the base, so less weight toward the endpoint

Currently, 2 * 2 subtypes are defined
* endpoint like a hotend is assembled at the end of the robot arms
* endpoint is used as print bed or workpiece bed and moved by the robot arms, the hotend/drill is in fixed position. The XYZ movements of the robot are reverse to the G-Code movements.
* each of the two types can be with or without the 4th axis at the endpoint. Without the 4th axis, the hotend cannot be rotated, but for most tasks it may be sufficient. Should be called 3 axis palletized then, but the kinematics are very similar, so they are described (and implemented) together.

### Construction

As example a 3 axis palletize robot which can be found as R290 3 axis robot (a 4th actuator can be installed at the endpoint to be able to rotate the hotend):

![robot_3axispallet_measure_v2.png](/manual/configuration/robot_3axispallet_measure_v2.png)

The first stepper drives axis 1 and results in the polar direction. Stepper 2 drives the first big arm. Stepper 3 changes the second big arm by a parallelogram construction, so arm 3 is rotated remotely. Stepper 4, if it exists, allows rotation of the endpoint around the Z axis and will change polar direction also if the tool has an XY offset from axis 4. A 2-stage parallelogram based arm construction assures that the endpoint platform is always parallel to the base.

Axis 2 and 3 (steppers 2 and 3) are connected to their arms and the angles are independent, because the following construction is used:

![robot_3axispallet_axis2_3_parall_v2.png](/manual/configuration/robot_3axispallet_axis2_3_parall_v2.png)

Steppers 2 and 3 axes have the same axial position and right is a parallogram. Then a stepper 2 / arm2 angle changed doesn't change the arm3 angle and vice versa: from black with stepper 2 rotating to the red position, the angle3 doesn't change.

The endpoint plate stays horizontal by the following construction:

![robot_3axispallet_plate_parall_v2.png](/manual/configuration/robot_3axispallet_plate_parall_v2.png)

The right supporting arm is fix assembled to the base. The red endpoint plate stays parallel to the base, i. e. horizontal.

### Homing, arm lenghts

Axis 1 and if it exists axis 5 (4) is configured as usual: homing and setting the angle to the correct value in respect to the choosen coordinate system.

When being homed, axis 2 and axis 3 references are the two red long lines in the first image: the lines between the joints, not the arms themselves.

The short red line is the distance between the last joint and the assembly plate in Z direction. The same offsets can be set for X and Y direction. The offsets can be set at the G10 tool offsets alternatively. When using a tool changer, it will be easier to set them separately.

The arm lengths are the lengths of the red lines also, i. e. the distance of the joints, not the physical arm lengths. The arms could have different forms (e. g. curved), this doesn't change the configured values. Curved arms could have the advantage to give bigger min&max angles.

### Configuration

The 4 axis palletized is handled internally as 5 axis, with the 4th axis automatically rotated by the parallelogram. The setup can be made with A parameters, describing Denavit-Hartenberg (DH) parameters, or with B parameters. DH parameters allow finetuning axes, if they are not assembled perfectly.

M669 K13 A"4axisPall" defines the type with 4 actuators.
M669 K13 A"4axisPallReverse" defines the type with 4 actuators with the print object placed on the robot endpoint platform and the hotend stationary above. X, Y and Z movements will be reversed, hence the name.
Without the 4th stepper, the name is 3axis... each.

Default is axis 1 being vertical, axes 2 to 4 (hidden 4) being horizontal and parallel, axis 5 vertical again. Positive angles are CCW from above for axes 1 and 5, 0 degrees being in the X direction. For axes 2 to 4, positive angles are CCW seen from front, 0 degrees being the horizontal position (this can all be overritten by DH parameters).

The automatically set 4th axis can be simply calculated from axes 2 and 3: angle 2 + angle 3 + angle 4 = 0.

### Mesh compensation

When no 4th (in reality 5th) axis is installed, the endpoint rotates when axis 1 rotates, so mesh compensation with a probe with XY offset <> 0 is not possible by default. A solution is to define the probe as if it is a tool and the probe being at tool's position: defining G10 to point to the probe, and setting probe properties to XY 0,0 offset. Then measuring and storing the results with the correct coordinates. Then setting back the G10 properties to the true tool offsets, then print with mesh compensation activated.

### Documentation

A very good explanation if the IRB 460 robot is the article "17. Cristoiu and Nicolescu, New Approach For Forward Kinematics Modeling of Industrial Robots with Closed Kinematic Chain", which can be found as pdf.
