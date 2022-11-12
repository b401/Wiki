---
title: CPU
author: i4
date: M11-02-2022
---
# CPU

A x86 (32bit) CPU fetches a double word (4 bytes - 32 bit) from a specific address in memory.  
Each binary pattern gets examined and if there´s a valid double word (32bit) instruction is found it gets executed.

1. Control Unit - Retrieves and decodes instructions from the CPU and then storing and retrieving them to and from memory.
2. Execution Unit - Where the execution of fetching and retrieving instruction occurs.
3. Registers - Internal CPU memory location used a temporary data storage.
4. Flags - Indicate events when execution occurs.


# Registers
## EIP - Execution instruction pointer
Points to the address of the next instruction.
