---
title: Heap
author: i4
date: M11-02-2022
---
# HEAP
While the stack grows downward the heap grows upward towards the stack.

```
  ┌───────────────┐
  │               │
  │               │   Larger Addresses
  │               │         │
  │               │         │  The Stack Grows
  │    Stack      │         │     DOWNWARD
  │               │         │
  │               │         │
  │               │         ▼
  │               │
  ├───────────────┤  0x00001000 (Stack Pointer)───────── Top of the Stack
  │               │
  │               │
  │               │
  │               │
  │    Heap       │
  │               │
  │               │
  │               │
  │               │
  └───────────────┘
```

The heap is the region of your computer´s memory that is not managed automatically for you, and is not as tightly managed by the CPU. It is *free-floating region of memory* and is __larger__ than the stack allocation of memory.


Programs can call =malloc()= or =calloc()= to allocate memory on the heap. Heap memory needs to be freed after using it with =free()=. If this step is not done, we´ll end up with a **memory leak**.

Unlike the stack, the heap does not have size restrictions on variable size. Variables created on the heap are **accessible by any function. Making them essentially global in scope**.

## Memory Leak
Memory on the heap will still be set aside and won´t be available to other processes that need it.
