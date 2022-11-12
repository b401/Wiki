---
title: Assembly
date: M11-02-2022
author: i4
---
# Sections
## Data Section
This section is used for declaring initialized data or constants as this data does not ever change at runtime. You can declare constant values, buffer sizes, file names, etc.
## BSS Section
This section is used for declaring uninitialized data or variables.
## Text Section
This section is used for the actual code sections as it begins with a global _start which tells the kernel where execution begins.

## Difference between AT&T and Intel
The main difference between ATT & Intel is that the destination comes before the source.  
For example:
```
#INTEL
mov edx,eax                     #eax gets moved to edx
#ATT
movl %eax,%edx                  #eax gets moved to edx
```

## Opcodes
``cat /usr/include/asm/unistd_32.h``

## Moving data between registers
Moving data between registers is the fastest way to move it.
```
# ATT flavor
movl %edx,%eax
```

## Program
### AT&T
Compile: 
```
as -32 -o moving_immediate_data.o moving_immediate_data.s
ld -m elf_i386 -o moving_immediate_data
```

```
# moving_immediate_data: mov immediate data between registers & memory

.section .data

.section .bss
  .lcomm buffer 1

.section .text
  .global _start

_start:
  nop         # used for debugging purposes

mov_immediate_data_to_register:
  movl $100, %eax                 # mov 100 into the EAX register
  movl $0x50, buffer              # mov 0x50 into buffer memory location

exit:
  movl $1, %eax                   # sys_exit system call
  movl $0, $ebx                   # exit code 0 successful execution
  int $0x80                       # call sys_exit
```


