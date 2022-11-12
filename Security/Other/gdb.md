---
title: GDB
author: i4
date: M11-02-2022
---
# GDB
## Control flow
### Single Step
``si``
### Print register
``i r``
### Change register
``set $edx = 0xff``
### Print address value
``x/6 &{varname}``

### Outputs
#### Formats
```
Syntax
x [Address expression]
x /[Format] [Address expression]
x /[Length][Format] [Address expression]
x
Parameters
Address expression
Specifies the memory address which contents will be displayed. This can be the address itself or any C/C++ expression evaluating to address. The expression can include registers (e.g. $eip) and pseudoregisters (e.g. $pc). If the address expression is not specified, the command will continue displaying memory contents from the address where the previous instance of this command has finished.
Format
If specified, allows overriding the output format used by the command. Valid format specifiers are:
o - octal
x - hexadecimal
d - decimal
u - unsigned decimal
t - binary
f - floating point
a - address
c - char
s - string
i - instruction
The following size modifiers are supported:

b - byte
h - halfword (16-bit value)
w - word (32-bit value)
g - giant word (64-bit value)
Length
Specifies the number of elements that will be displayed by this command.
```

### Examine specific registers
```
x/1xw $esp
```

### Hijacking execution flow without symbols
**Find .plt address of imported function**  
*system() in this example*
```
(gdb) info address system
> Symbol "system" is at 0x7f40d3e54120 in a file compiled without debugging.
```

**set breakpoint at this address**  
```
(gdb) br *0x7f40d3e54120
```

**Run until you hit this breakpoint and change $rip/$eip**
```
(gdb) set var $rip=0xfffffffffffff
```

## .PLT
*Procedure Linkage Table*  
Used to call external procedures/functions whose address isn't known in the time of linking and is left to be resolved by the dynamic linker at run time.
