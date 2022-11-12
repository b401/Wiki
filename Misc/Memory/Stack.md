---
author: i4
title: Stack
date: M11-02-2022
---
# Stack
The stack pointer is a register that contains the top of the stack. The stack pointer contains the smallest address, lets say for example 0x00001000, such that any address smaller than 0x00001000 is considered garbage and any address greater than 0x00001000 is considered valid.

The above address is random and is not an absolute where you will find the stack pointer from program to program as it will vary.

```
  ┌───────────────┐
  │               │  Larger Addresses
  │  0x00001001   │
  │               │   │
  │  Valid        │   │
  │               │   │ The stack Grows (Down)
  │               │   │
  │               │   │
  │               │   ▼
  ├───────────────┤   0x00001000 (Stack Pointer) --------- Top of the Stack
  │               │
  │               │
  │               │
  │               │  Smaller Addresses
  │  Garbage      │
  │               │
  │               │
  │               │
  │   0x00000fff  │
  │               │
  └───────────────┘
```

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

```

                    ┌───────────────┐
                    │               │
                    │               │
                    │               │
                    │               │
                    │               │
                    │               │   Stack pointer (ESP)
                ┌▲  ├───────────────┤ ◄────────
                ││  │  return value │
                ││  ├───────────────┤
                └┤  │┼┼┼┼┼┼┼┼┼┼┼┼┼┼┼│  args
  int main(void) │  ├───────────────┤
   Stack Frame  ┌┤  │               │
                ││  │ int main(void)│
                ││  │               │
                ▼┘  ├───────────────┤
                    │               │
                    │               │
                    │               │
                    │               │
                    └───────────────┘
```

After we arrive at the =int addMe(int a, int b)= instruction, the stack gets rearranged.

```

                    ┌───────────────┐
                    │               │
                    │               │   Stack pointer (ESP)
                 ▲  ├───────────────┤ ◄────────
                 │  │               │
                 │  │ int addMe(a,b)│
                 │  │               │   Frame pointer (FP)
                 │  ├───────────────┤ ◄────────
                 │  │  return value │
                 │  ├───────────────┤
                 │  │┼┼┼┼┼┼┼┼┼┼┼┼┼┼┼│  args
  int main(void) │  ├───────────────┤
   Stack Frame   │  │               │
                 │  │ int main(void)│
                 │  │               │   Stack pointer (EBP)
                 ▼  ├───────────────┤ ◄────────
                    │               │
                    │               │
                    │               │
                    │               │
                    └───────────────┘
```

FP is the frame pointer and points to the location where the stack pointer was just before =int addMe(int a, int b)= moved the stack pointer or SP for =int addMe(int a, int b)=´s own local variables.
