---
title: Error codes and software reset codes
description: The M122 gcode will produce a report which contains a report with a lot of information. Included in this is the current error code and the last software reset code.
published: true
date: 2021-12-05T23:19:41.528Z
tags: 
editor: markdown
dateCreated: 2021-12-05T23:19:38.899Z
---

# Introduction

The M122 gcode will produce a report which contains a report with a lot of information. Included in this is the current error code and the last software reset code.

# Error codes

The error status in the M122 report should look something like this:

```
Error status: 0
```

The status is a bit map with the following meaning for each bit:

```
0x01 Watchdog turned heater off temporarily
0x02 One or more step errors occurred
0x04 Output buffer starvation
0x08 Output stack overflow
0x10 An SD card operation timed out
```

# Software reset codes

The full reset status included in the M122 report should look something like this:

```
Last reset 00:00:11 ago, cause: software
Last software reset at 2020-05-05 09:06, reason: User, spinning module GCodes, available RAM 9048 bytes (slot 1)
Software reset code 0x0003 HFSR 0x00000000 CFSR 0x00000000 ICSR 0x0441f000 BFAR 0xe000ed38 SP 0xffffffff Task 0x4e49414d
```

The last reset cause is the important bit.

The software reset code only applies to the last reset commanded by software. If the reason is User, that means the reset was commanded by the user, for example because an Emergency Stop was requested, or the M999 command was used, or the firmware was updated. If the reset was caused by a firmware crash then the reason will usually be one of Hard Fault, Memory protection Fault, or Watchdog. In this case there will also be a stack trace:

```
Last reset 00:00:09 ago, cause: software
Last software reset at 2020-05-05 09:11, reason: deliberate Hard fault, spinning module GCodes, available RAM 8720 bytes (slot 0)
Software reset code 0xc063 HFSR 0x40000000 CFSR 0x00008200 ICSR 0x0441f803 BFAR 0x20800000 SP 0x2000503c Task 0x4e49414d
Stack: 004389a7 0044978a 61000000 c0600000 0044e225 0044e205 40080000 20006744 0045b68d 0045b611 00000000
```

The fields HFSR, CFSR, ICSR, BFAR and SP are the values ARM processor core registers at the time of the reset. See https://developer.arm.com/documentation/dui0553/latest/ for details. The Task field is the first 4 characters of the task name. The first Stack entry is the Link Register, which usually contains the return address from the current function, plus one. The second stack entry is the address at which the exception occurred. The remaining stack entries are what was on the stack at the time, which may include further return addresses.