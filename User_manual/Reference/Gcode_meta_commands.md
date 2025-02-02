---
title: GCode meta commands
description: RepRapFirmware 3.01 introduced the concept of basic programming constructs (conditionals, loops and parameters) to GCode. This combined with the rich object model in RRF3 provides a powerful new layer of control customisation.
published: true
date: 2025-02-02T22:27:53.405Z
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

```
echo move.axes[0].homed, move.axes[1].homed, move.axes[2].homed
```

Starting with firmware 3.4 the output from an echo command can be redirected to a file. To create a file (deleting any existing file of the same name) containing the text resulting from the echo command, use this syntax:

`echo ><filename> <expression>, <expression>, ...`

where \<filename> is either a quoted string or an expression enclosed in { } that evaluates to a string.

To append a line to an existing file (or create a new file if it doesn't already exist), use:

`echo >><filename> <expression>, <expression>, ...`

To append to a file without adding a newline character at the end, so that multiple echo commands can be used to build a single long line, use the following (supported in firmware 3.5beta2 and later):

`echo >>><filename> <expression>, <expression>, ...`

There must be no spaces between the >,>> or >>> symbol and \<filename>. The default folder for the file is /sys.

Example:

```
echo >"mymacro.g" "G1 F3000 X{move.axes[0].max-10}" ; move to 10mm below axis max when the macro was generated
echo >>"mymacro.g" "G1 F3000 Y{move.axes[1].max-10}" ; move to 10mm below axis max when the macro is executed
```

Example writing a single long line:
(*Note that the line is started with a ">>>" echo, subsquent elements are added with ">>>" and the final element in the line is added with a ">>" which appends the newline character*)

```
echo >>>"data.csv" move.axes[0].machinePosition^","^move.axes[1].machinePosition^","^move.axes[2].machinePosition
echo >>>"data.csv" ","^heat.heaters[0].current^","^heat.heaters[2].current^","^heat.heaters[3].current
echo >>"data.csv" ","^sensors.filamentMonitors[0].position^","^sensors.filamentMonitors[1].position^","^sensors.filamentMonitors[2].position
```
If you needs to write a new file with multiple entries on a single line and the file may already exist, use M472 to delete it first. You can use the fileexists() condition to only delete the file if it exists.

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

Within the while-part and the body, the named constant *iterations* is the number of loop iterations already completed. So it is zero during the first iteration, 1 during the second, and so on.

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

If a while loop is used in a GCode job file (not in a macro), it should have [M400](/User_manual/Reference/Gcodes/M400) at the end, and immediately before any uses of 'continue' within the loop.

### Nested loops

Loops of this form

```
while <boolean-expression>
  ...
  while <boolean-expression>
    ...
```

Have an *iterations* counter at each nested level of the loop. Only the *iterations* counter of the loop that is being executed is accessible within that loop. If you want to access the iterations counter for an enclosing loop within an inner loop, you need to use a variable to save and retrieve that counter. For example:

```
var loopCounterOuter = 0
while <boolean-expression>
  ; outer loop
  ...
  set var.loopCounterOuter = iterations
  while <boolean-expression>
    ; inner loop
    ...
    echo iterations ; iterations for the inner loop
    echo var.loopCounterOuter ; iterations for the outer loop
```

## Variables

*Supported from RRF 3.3. Array-valued variables are supported from RRF 3.5*

### Local variable declaration

`var <new-variable-name> = <expression>`

This creates a new variable called *var.\<new-variable-name>* and initializes it to *\<expression>*. The name must not already be in use. The scope of a local name is the remainder of the block in which it is declared.

### Global variable declaration

`global <new-variable-name> = <expression>`

This creates a new variable called *global.\<new-variable-name>* and initializes it to *\<expression>*. The name must not already be in use.

Example:

`global T1heat=0`

### Variable assignment

`set var.<existing-local-variable-name> = <expression>`
`set global.<existing-global-variable-name> = <expression>`

This re-assigns an existing variable (which must be of the form *var.name* or *global.name*) to have the value *expression*.

Example:

`set global.T1heat=heat.heaters[1].active`

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

Using an expression to replace a parameter letter, or to replace the command number after the initial G or M, is not supported. (so  for example *G1 {global.extruder_axis}^2* is not supported)

If a parameter of a G- or M-command requires multiple values and you want to use expressions for some or all of them, then when using RRF 3.5 and later the whole parameter must be an array expression (see later); for example:
```
M201 E{var.e0Accel, var.e1Accel}
```
RRF 3.4 and earlier *in standalone mode only* supported this form instead:
```
M201 E{var.e0Accel}:{var.e1Accel}
```
  
# Expressions

## General

Tabs and space characters may be used freely between variable names, keywords, literals and other lexical elements to improve readability.

Sub-expressions may be enclosed in { } or in ( ). However, standard CNC GCode uses ( ) to enclose comments. So in CNC mode, RepRapFirmware treats ( ) as enclosing subexpressions when they appear inside { } and as enclosing comments when they do not. Therefore, when RepRapFirmware is running in CNC mode, any use of ( ) to enclose a subexpression or function parameter list must be within an expression enclosed in { }.

## Types

The available types of expressions and variables are: **bool**, **int**, **float**, **string**, **DateTime**, **object** and **array**. The only operations available on values of type **object** are comparison with **null** and taking a member. The only operations available on values of type **array** are taking the length (unary prefix operator #) and indexing (operator [ ] ).

## Type conversions

The following implicit type conversions will be performed when necessary:

* from type **int** to type **float**
* from any type to **string**

## Named constants

The following named constants are provided:

| Name | Type | Meaning |
|:---|:---|
| false | bool | Boolean false |
| input | (variable) | The most recent input from a message box with mode 4, 5, 6 or 7 (see M291 command)
| iterations | int | The number of completed iterations of the innermost loop |
| line | int | The current line number in the file being executed |
| null | object | The null object |
| pi | float | Pi (3.14159265...) |
| result | int | 0 if the last G-, M- or T-command on this input channel was successful, 1 if it returned a warning, 2 if it returned an error, or -1 if it was a blocking M291 message box command that had a Cancel button, and either the Cancel button was pressed or the message box timed out.^1^  Meta commands do not change 'result'. |
| true | bool | Boolean true |

^1^ In RRF 3.5.0-rc3 and earlier, pressing 'Cancel' or M291 timing out will cancel the current job/macro, and any subsequent lines in the job/macro are not processed, rather than set result to -1. See [this discussion on the Duet3D forums](https://forum.duet3d.com/topic/34945/meta-gcode-result-variable-inconsistent-with-docs) for a workaround.

## Literals

Integer literals may be expressed in decimal format (e.g. *4321*) or hexadecimal format (e.g. *0x3f*).

Floating point literals may be expressed in fixed-point simple format (e.g. *165.32*) or scientific format (e.g. *6.2e6*).

String literals are surrounded by double quote characters (e.g. *"Hello world"*). To include a double-quote character in a string iteral, use two double-quote characters (e.g. *"Here is some ""quoted text"""*). String literals are limited to 100 characters.

Character literals (supported in RRF 3.5.0-rc.1 and later) are surrounded by single quote characters (e.g. 'a').

There are no literals of other types, however the Boolean constants **true** and **false** are available.

## Object model properties

Expressions may use the values of any properties in the RepRapFirmware Object Model (OM). See [Object Model of RepRapFirmware](https://github.com/Duet3D/RepRapFirmware/wiki/Object-Model-Documentation) to see what is available. An object model property selector must evaluate to a single string, numeric or Boolean value, unless it is the operand of the unary # operator in which case it must evaluate to an array or a string.

## Variables

*These are supported from RRF 3.3 in standalone mode and from 3.4 in SBC mode*

The values of global variables that have been created using the '*global*' command can be retrieved using the syntax *global.\<variable name>*. Example:

```
global defaultSpeed=6000
...
G1 X0 Y0 F{global.defaultSpeed}
```

Similarly, the values of local variables created using the **var** command can be retrieved using the syntax *var.\<variable name>*

Use `exists(\<variable>)` to check if a variable is defined. e.g.
```
exists(global.defaultSpeed)
```
**Note**: In CNC mode, round brackets in a line are treated as enclosing comments. This means that if you use expressions in GCode, if you use round brackets within expressions then the whole expression must be enclosed in { }. This would normally be the case anyway when using expressions as parameters to regular GCode commands, so this only affects the use of round brackets in GCode meta commands such as if, while, var, global, echo and so on. See [here](/User_manual/Machine_configuration/Configuration_CNC#differences-in-firmware-behaviour-between-cnc-mode-and-fdm-mode) for more information.

## Macro parameters

*These are supported from RRF 3.3*

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

If you need the macro to handle situations where parameters that are expected are not passed, use the 'exists' function. E.g.
```
;macro.g
if exists(param.S)
    G1 X{param.S}
else
    echo "no move passed to macro.g"
```

You cannot use P as a parameter (as P is already used to reference the gcode file that is being called by M98)

When using a macro as custom gcode, do not use G, M, N, or T as parameters in a custom 'G' gcode file. Do not use G, M, or N as parameter in a custom 'M' gcode file.  There are no standard G or M commands that use these parameters and RRF will treat the parameter as being the start of the next command.

## Array expressions

*These are supported from RRF 3.5*

A sequence of expressions exclosed in { } and separated by commas is an array expression. A trailing comma before the closing } is required when constructing a 1-element array and optional when there are two or more elements. The types of the elements do not all need to be the same. Examples:

`{1,2,3}` is an array of three elements
`{1,2,3,}` is an array of three elements with the same value as the previous example
`{pi,}` is an array of one element
`{pi}` is not an array, it is a simple value
`{1,{2,3,4},5}` is an array of three elements, whose second value is itself an array

The unary prefix operator `#` can be applied to a value of array type to get the number of elements, and the indexing operator `[ ]` can be applied to extract a single element.

Note, once created arrays are fixed length. The array values must be reassigned to a new array to change its length.

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
| abs | float->float or int->int | Returns the absolute value of the operand |
| acos | float->float | Result is in radians |
| asin | float->float | Result is in radians |
| atan | float->float | Result is in radians |
| atan2 | (float, float)->float | Result is in radians |
| ceil | float->int or float->float | Result is **int** if it fits in a 32-bit signed integer, else float  (available in RRF 3.5.0 and later). |
| cos | float->float | Argument must be in radians |
| datetime | int->DateTime or string->DateTime | Converts a number of seconds from the datum to a **DateTime**, or a string with format "yyyy-mm-ddThh:mm:ss" to a **DateTime**. Available in RRF 3.4.0 and later. |
| degrees | float->float | Converts radians to degrees |
| drop | (string, int)->string or (array, int)->array | Returns all but the first N elements of the first argument, where N is the smaller of the second argument and the length of the first argument (available in RRF 3.6.0 and later)
| exists | name  -> bool | Yields true if 'name' is a valid variable or object model element name and is not null (available in RRF 3.3.0 and later). Especially useful for testing whether a particular parameter has been provided when a file macro was called. |
| exp | float->float | Returns *e* raised to the operand (supported in RRF 3.5.0 and later) |
| fileexists | string -> bool | Yields true if the string parameter is the name of a file in the file system (available in RRF 3.5.0 and later). |
| fileread | (string, int, int, char)  -> array | Returns an array of elements read from a single-line CSV or similar file (available in RRF 3.5.0 and later). The string parameter is the name of the file to read. The first integer parameter is the number of elements to skip; the second is the maximum number of elements to read; and the character is the field separator, typically ','. See note at the end of this table.|
| find | (string, char)->int or (string, string)->int | Returns the index of the first occurrence of the character in the string, or the index in the first string at which the first occrrence of the second string starts; or -1 if the second argument does not occur in the first argument (available in RRF 3.6.0 and later)
| floor | float->int or float->float | Result is **int** if it fits in a 32-bit signed integer, else float |
| isnan | float->bool | Returns true if the operand is a NaN (Not-a-Number) e.g. sqrt(-1) |
| log | float->float | returns the natural logarithm of the operand (supported in RRF 3.5beta3 and later) |
| max | (float, ...)->float or (int, ...)->int | Accepts 1 or more arguments. If any argument is NaN then the result is NaN. |
| min | (float, ...)->float or (int, ...)->int | Accepts 1 or more arguments. If any argument is NaN then the result is NaN. |
| mod | (int, int)->int or (float, float)->float | Returns the remainder from dividing the first operand by the second operand |
| pow | (float, float)->float or (int, int)->int | Returns the first operand to the power of the second operand  (supported in RRF 3.5beta3 and later). In RRF 3.5.0 and later the result type is **int** if the operatnds are int, the second operand is non-negative, and the result fits in an **int**. |
| radians | float->float | Converts degrees to radians |
| random | int->int | Operand must >= 1. Returns a pseudo-random integer in the range 0 to one less than the operand. |
| sin | float->float | Argument must be in radians |
| sqrt | float->float | Returns the square root of the operand |
| take | (string, int)->string or (array, int)->array | Returns the first N elements of the first argument, where N is the smaller of the second argument and the length of the first argument (available in RRF 3.6.0 and later)
| tan | float->float | Argument must be in radians |
| vector | (int, T) -> array of T | (RRF 3.5.0 and later) Returns an array with the number of elements equal to the first operand and each element a copy of the second operand

### Notes on the fileread function

Each element (including each skipped element) must be one of the following:
- a string in double quotes
- a number in any of the usual integer or floating point formats
- a character in single quotes
- empty (in which case a corresponding null element is included in the array).

Leading and trailing spaces and tabs around each element are ignored. If the file cannot be opened and read, or if any elements do not confirm to the above. then the command containing the fileread call will be aborted.

If the element is a quoted string, then characters within it that match the separator character are not treated as separators, and each consecutive pair of double-quote characters is replaced by a one double quote character.

The number of array elements returned will be one greater than the number of separator characters found; so an empty file will give rise to an array comprising a single null element.

# Notes

## Line Endings
* If you are writing macros in a windows OS, set the EOL to be Linux-style (LF only). Windows default (CR LF) written macros work, but in some versions of RRF error messages count the CR and LF as two lines, so all line numbers were multiplied by 2.

## daemon.g

The [macro file /sys/daemon.g](/User_manual/Tuning/Macros#daemong) can be used to execute regular tasks. The firmware looks for the file, if the file exists it executes it and once the end of file is reached it waits. If the file is not found it waits for 10s and then looks for it again. 


It is recommended to use a while loop inside the daemon.g file if you are using it to prevent the firmware having to open it every 10 seconds. E.g if you want a shorter update time then put a while loop inside the daemon.g with G4 S1 in it for 1 second repeats.

Caution must be taken not to start a loop that takes a long time to complete, without having a G4 P500 or similar command to hand control back to the main process every half a second or so.


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