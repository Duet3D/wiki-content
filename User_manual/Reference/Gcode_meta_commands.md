---
title: GCode meta commands
description:  RepRapFirmware 3.01 introduced the concept of basic programming constructs (conditionals, loops and parameters) to GCode. This combined with the rich object model in RRF3 provides a powerful new layer of control customisation. 
published: true
date: 2021-12-16T23:16:35.787Z
tags: 
editor: markdown
dateCreated: 2021-12-03T20:03:05.882Z
---

# Introduction

RepRapFirmware 3.01 introduced the concept of basic programming constructs (conditionals, loops and parameters) to GCode. This combined with the rich object model in RRF3 provides a powerful new layer of control customisation.

RepRapFirmware 3.01 and later provide GCode programming constructs to allow conditional execution and iteration, and allow parameter values in GCode commands to be expressions instead of literals.

# Programming constructs

## Abort command

`abort <opt-expression>`

This causes all nested macros and the current print file (if any) to be terminated. The expression (if present) is converted to a string, which is included in the message presented to the user and written to the log file.

## Echo command

`echo  <expression>, <expression>, ...`

At least one expression must be provided. The expressions are converted to strings and written to the console, with a space character between each pair. Example:

`echo move.axes[0].homed, move.axes[1].homed, move.axes[2].homed`

Starting with firmware 3.4 the output from an echo command can be redirected to a file. To create a file (deleting any existing file of the same name) containing the text resulting from the echo command, use this syntax:

`echo ><filename> <expression>, <expression>, ...`

where \<filename> is either a quoted string or an expression enclosed in { } that evaluates to a string.

To append a line to an existing file (or create a new file if it doesn't already exist), use:

`echo >><filename> <expression>, <expression>, ...`

There must be no spaces between the > or >> symbol and \<filename>. The default folder for the file is /sys.

Example:

```
echo >"mymacro.g" "G1 F3000 X"^{move.axes[0].max-10} ; move to 10mm below axis max when the macro was generated
echo >>"mymacro.g" "G1 F3000 Y{move.axes[1].max-10}" ; move to 10mm below axis max when the macro is executed
```

## Conditional construct

The general form of the conditional block is:

```
if <boolean-expression>
  ...
elif <boolean-expression>
  ...
else
  ...
```

where as usual the “elif” part can be omitted or repeated, and the “else” part can be omitted. The bodies of the if, elif and else parts may contain ordinary GCode commands and/or program elements. Each line in the body must be indented from the corresponding keyword to indicate the extent of the body. The body ends just before the first line that is not indented.

## Loop

```
while <boolean-expression>
  ...
```

The body must be indented from the while keyword. The body ends just before the first line that is not indented.

Within the while-part and the body, the named constant *iterations *is the number of loop iterations already completed. So it is zero during the first iteration, 1 during the second, and so on.

A loop may contain one or more break statements, which would normally be inside if-constructs:

```
while <boolean-expression>
  ...
  if <boolean-expression>
    break
  ...
```

The break statement transfers control to the line following the end of the loop body.

A loop may also contain one or more continue statements, which would normally be inside if-constructs:

```
while <boolean-expression>
  ...
  if <boolean-expression>
    continue
  ...
```

The continue statement increments the iteration counter and transfers control back to the start of the loop, ready to evaluate the while-condition again.

## Variables

### Local variable declaration

*Implemented in RRF 3.3 and later*

`var <new-variable-name> = <expression>`

This creates a new variable called *var.\<new-variable-name>* and initializes it to *\<expression>*. The name must not already be in use. The scope of a local name is the remainder of the block in which it is declared.

### Global variable declaration

*Implemented in RRF 3.3 and later*

`global <new-variable-name> = <expression>`

This creates a new variable called *global.\<new-variable-name>* and initializes it to *\<expression>*. The name must not already be in use.

Example:

`set global.T1heat=0`

### Variable assignment

*Implemented in RRF 3.3 and later*

`set <existing-variable-name> = <expression>`

This re-assigns an existing variable (which must be of the form *var.name* or *global.name*) to have the value *expression*.

Example:

`set global.T1heat={heat.heaters[1].active}`

### Variable naming

Variables must conform to the following naming convention:

* The first character must be a letter
* The remaining characters must be letters, digits or the underscore character
* There is no specific limit on variable length, other than the total length of the expression that uses the variables must be <250 characters

# Use of expressions within GCode commands

This form:

`{ <expression> }`

may be used in place of any numeric or quoted string operand within a GCode command. Example:

```
G1 X{move.axes[0].max-10} Y{move.axes[1].min+10}
```

Using an expression to replace a parameter latter, or to replace the command number after the initial G or M, is not supported. (so  for example *G1 {global.extruder_axis}^2* is not supported)

If a parameter of a G- or M-command requires multiple values and you want to use expressions for some or all of them, then when using RRF 3.3 and later the parameter must be expressed like this:

`{ <expression_1>, <expression_2>, <expression_3> ... }`

For example:
```
M201 {var.e0Accel, var.e1Accel}
```
RRF 3.2 *in standalone mode only* supported this form instead:
```
M201 {var.e0Accel}:{var.e1Accel}
```
  
# Expressions

## General

Tabs and space characters may be used freely between variable names, keywords, literals and other lexical elements to improve readability.

Sub-expressions may be enclosed in { } or in ( ). However, standard CNC GCode uses ( ) to enclose comments. So in CNC mode, RepRapFirmware treats ( ) as enclosing subexpressions when they appear inside { } and as enclosing comments when they do not. Therefore, when RepRapFirmware is running in CNC mode, any use of ( ) to enclose a subexpression or function parameter list must be within an expression enclosed in { }.

Expression length is limited to 250 characters.

## Types

The available types of expressions and variables are: **bool**, **int**, **float**, **string**, **DateTime**, **object** and **array**. The only operations available on values of type **object** are comparison with **null** and taking a member. The only operations available on values of type **array** are taking the length (unary prefix operator #) and indexing (operator [ ] ).

## Type conversions

The following implicit type conversions will be performed when necessary:

* from type **int** to type **float**
* from any type to **string**

## Named constants

The following names constants are provided:

| Name | Type | Meaning |
|:---|:---|
| false | bool | Boolean false |
| iterations | int | The number of completed iterations of the innermost loop |
| line | int | The current line number in the file being executed |
| null | object | The null object |
| pi | float | Pi (3.14159265...) |
| result | int | 0 if the last G-, M- or T-command on this input channel was successful, 1 if it returned a warning, 2 if it returned an error. Meta commands do not change 'result'. |
| true | bool | Boolean true |

## Literals

Decimal integer, floating point and string literals may be used. There are no literals of other types, however the Boolean constants **true** and **false** are available.

## Object model properties

Expressions may use the values of any properties in the RepRapFirmware Object Model (OM). See [Object Model of RepRapFirmware](https://github.com/Duet3D/RepRapFirmware/wiki/Object-Model-Documentation) to see what is available. An object model property selector must evaluate to a single string, numeric or Boolean value, unless it is the operand of the unary # operator in which case it must evaluate to an array or a string.

## Variables

*These are supported in RRF 3.3 running in standalone mode.*

The values of global variables that have been created using the '*global*' command can be retrieved using the syntax *global.\<variable name>*. Example:

```
global defaultSpeed=6000
...
G1 X0 Y0 F{global.defaultSpeed}
```

Similarly, the values of local variables created using the **var** command can be retrieved using the syntax *var.\<variable name>*

Use exists{\<variable>} to check if a variable is defined (not yet implemented in SBC mode). e.g.
```
exists(global.defaultSpeed)
```
**Note**: In CNC mode, round brackets in a line are treated as enclosing comments. This means that if you use expressions in GCode, if you use round brackets within expressions then the whole expression must be enclosed in { }. This would normally be the case anyway when using expressions as parameters to regular GCode commands, so this only affects the use of round brackets in GCode meta commands such as if, while, var, global, echo and so on. See [here](/User_manual/Machine_configuration/Configuration_CNC#differences-in-firmware-behaviour-between-cnc-mode-and-fdm-mode) for more information.

## Macro parameters

*These are supported in RRF 3.3*

It is possible to add additional parameters when calling a macro using M98 or using a macro as a custom gcode.

e.g.

`M98 P"macro.g" S100 Y"string"`

Within a macro file, parameters that were passed to the macro can be retrieved using the syntax *param.\<parameter letter>* where *\<parameter letter>* is a single uppercase letter.

e.g. (using the macro call from above)

```
;macro.g
G1 X{param.S}
echo {param.Y}
```

When using a macro as custom gcode, do not use G, M, N or T as parameters in a custom 'G' gcode file. Do not use G, M or N as parameter in a custom 'M' gcode file.  There are no standard G or M commands that use these parameters and RRF will treat the parameter as being the start of the next command.

## Unary prefix operators

| Operator | Signature | Meaning |
|:---|:---|
| ! | bool->bool | Boolean not |
| + | int->int, float->float | Unary + |
| + | DateTime->int | Converts a date & time value to a number of seconds since the datum. Available in RRF 3.4 and later. |
| - | int->int,  float->float | Unary - |
| # | X[ ]->int, string->int | Number of elements in array, or number of characters in string |

## Binary infix operators

Where an expression has multiple binary operators of the same precedence and parentheses are not used to specify the order of evaluation, the operators are evaluated from left to right.

| Operator | Precedence | Signature | Meaning |
|:---|:---|
| * | 6 | (int,int)->int, (float,float)->float | Multiplication : **See CAUTION note below** |
| / | 6 | (float,float)->float | Division |
| + | 5 | (int,int)->int, (float,float)->float, (DateTime,int)->DateTime | Addition. When adding an int to a DateTime the second operand is in seconds. |
| - | 5 | (int,int)->int, (float,float)->float, (DateTime,DateTime)->int, (DateTime,int)->DateTime | Subtraction. When subtracting one DateTime from another the result is in seconds. When subtracting an int from a DateTime the second operand is in seconds. |
| = or == | 4 | (int,int)->bool, (float,float)->bool, (string,string)->bool | Equality |
| != | 4 | (int,int)->bool, (float,float)->bool, (string,string)->bool | Inequality |
| < | 4 | (int,int)->bool, (float,float->bool | Less than |
| <= | 4 | (int,int)->bool, (float,float)->bool | Less than or equal |
| > | 4 | (int,int)->bool, (float,float)->bool | Greater than |
| >= | 4 | (int,int)->bool, (float,float)->bool | Greater than or equal |
| & or && | 3 | (bool,bool)->bool | Boolean and |
| \| or \|\| | 3 | (bool,bool)->bool | Boolean or |
| ^ | 2 | (string,string)->string | String concatenation |

**Caution!** The multiplication operator * will work when it is used anywhere inside an expression or subexpression enclosed in { } but not otherwise. This is because the * character in a line of GCode normally introduces an end-of-line checksum.

## Ternary operator

The expression \<expr1> ? \<expr2> : \<expr3> evaluates \<expr2> if \<expr1> is true, otherwise \<expr3>. \<expr1> must be Boolean. \<expr3> may be another ternary expression. The ternary operator has precedence 1.

## Functions

The following functions are supported, with their conventional meanings:

(Please check the [changelog](https://github.com/Duet3D/RepRapFirmware/wiki/Changelog-RRF-3.x) to determine when a particular function was implemented, some functions are implemented in RRF3.1, or 3.2, or later)

| Function name | Signature | Notes |
|:---|:---|
| abs | float->float or int->int |  |
| acos | float->float | Result is in radians |
| asin | float->float | Result is in radians |
| atan | float->float | Result is in radians |
| atan2 | (float, float)->float | Result is in radians |
| cos | float->float | Argument must be in radians |
| datetime | int->DateTime or string->DateTime | Converts a number of seconds from the datum to a DateTime, or a string with format "yyyy-mm-ddThh:mm:ss" to a DateTime. Available in RRF 3.4 and later. |
| degrees | float->float | Converts radians to degrees |
| exists | name  -> bool | Yields true if 'name' is a valid variable or object model element name and is not null (available in RRF 3.3beta3 and later). Especially useful for testing whether a particular parameter has been provided when a file macro was called. |
| floor | float->int or float->float | Result is int if it fits in a 32-bit signed integer, else float |
| isnan | float->bool |  |
| max | (float, ...)->float or (int, ...)->int | Accepts 1 or more arguments. If any argument is NaN then the result is NaN. |
| min | (float, ...)->float or (int, ...)->int | Accepts 1 or more arguments. If any argument is NaN then the result is NaN. |
| mod | (int, int)->int or (float, float)->float |  |
| radians | float->float | Converts degrees to radians |
| random | int->int | Operand must >= 1. Returns a pseudo-random integer in the range 0 to one less than the operand. |
| sin | float->float | Argument must be in radians |
| sqrt | float->float |  |
| tan | float->float | Argument must be in radians |

# Examples of use

## Using conditional GCode commands in bed.g to calibrate a delta printer

Example bed.g file for calibrating a delta printer using conditional GCode. At the start, it homes the printer only if it hasn't already been homed. Then it calibrates the printer by probing a number of points, starting again if probing fails. if calibration yields a standard deviation that is above a limit (set at the end of the loop, in this case >0.03mm), it repeats the calibration process. If calibration fails 5 times for any reason, it quits.

**NOTE:** If you use this method to iterate the levelling of a bed/gantry mounted on leadscrews (eg Cartesian, CoreXY etc), the maximum deviation corrected is still limited by the S parameter of [M671](/User_manual/Reference/Gcodes/M671) (default 1mm). If the maximum deviation exceeds this limit, the script will exit with "Error: Some computed corrections exceed configured limit of 1.00mm", as it would if G30 bed levelling was called manually.

```
; Auto calibration routine for large delta printer
M561                    ; clear any bed transform

; If the printer hasn't been homed, home it
if !move.axes[0].homed || !move.axes[1].homed || !move.axes[2].homed
  G28

; Probe the bed and do auto calibration
G1 X0 Y140 Z10 F10000        ; go to just above the first probe point
while true
  if iterations = 5
    abort "Too many auto calibration attempts"
  G30 P0 X0.00 Y140.00 Z-99999
  if result != 0
    continue
  G30 P1 X70.00 Y121.24 Z-99999
  if result != 0
    continue
  G30 P2 X121.24 Y70.00 Z-99999
  if result != 0
    continue
  G30 P3 X140.00 Y0.00 Z-99999
  if result != 0
    continue
  G30 P4 X121.24 Y-70.00 Z-99999
  if result != 0
    continue
  G30 P5 X70.00 Y-121.24 Z-99999
  if result != 0
    continue
  G30 P6 X0.00 Y-134.85 Z-99999
  if result != 0
    continue
  G30 P7 X-65.57 Y-113.57 Z-99999
  if result != 0
    continue
  G30 P8 X-112.29 Y-64.83 Z-99999
  if result != 0
    continue
  G30 P9 X-130.59 Y-0.00 Z-99999
  if result != 0
    continue
  G30 P10 X-115.90 Y66.91 Z-99999
  if result != 0
    continue
  G30 P11 X-69.45 Y120.29 Z-99999
  if result != 0
    continue
  G30 P12 X0.00 Y70.00 Z-99999
  if result != 0
    continue
  G30 P13 X60.62 Y-35.00 Z-99999
  if result != 0
    continue
  G30 P14 X-52.28 Y-30.19 Z-99999
  if result != 0
    continue
  G30 P15 X0 Y0 Z-99999 S8
  if result != 0
    continue
  if move.calibration.initial.deviation <= 0.03
    break
  echo "Repeating calibration because deviation is too high (" ^ move.calibration.initial.deviation ^ "mm)"
; end loop
echo "Auto calibration successful, deviation", move.calibration.final.deviation ^ "mm"
G1 X0 Y0 Z150 F10000                ; get the head out of the way
```