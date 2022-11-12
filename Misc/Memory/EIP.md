---
title: EIP
author: i4
date: M11-02-2022
---
# Instruction Pointer Register
EIP is the most important register for reverse engineering as it contains the next instruction adress that the CPU will execute.


## Example
```
#include <stdio.h>
#include <stdlib.h>

void unreachableFunction(void) {
    printf("I´m hacked! Hidden function\n");
    exit(0);
}

int main(void) {
    printf("Hello world\n");
    return 0;
}

```
