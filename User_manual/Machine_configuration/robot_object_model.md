---
title: Robot Object Model
description: description of the object model, explaining some internal workings also
published: true
date: 2022-10-15T05:16:12.719Z
tags: robot
editor: markdown
dateCreated: 2022-10-15T05:16:12.719Z
---

# Object Model

(this section may be moved to a dedicated page later)

The following description allows manipulating parameters of robot kinematics directly. Care should be taken for
* parameters which are connected
* parameters which change other parameters in normal operation
* values which are cached

Those special cases will be mentioned when describing the object model.

The object model is built with the structure of a double list: main topics and sublists each. The following structure follows this list structure.

First, there are values which are not part of the object model, but are constants which need recompilation of firmware when changed:

