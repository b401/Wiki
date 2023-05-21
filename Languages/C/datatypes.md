---
title: Data types
author: i4
date: M12-08-2022
---

# Data Type

## Standard types

| **Type** | **32-Bit Storage Size (Bytes)** | **64-Bit Storage Size (Bytes)** |
|--|:--:|:--:|
| char | 1| 1|
|unsigned char | 1 | 1|
| signed char |1|1|
|int|4|4|
|unsigned int |4|4|
|short|2|2|
|unsigned short|2|2|
|long|4|Win: 4, Unix: 8|
|unsigned long|4|Win: 4, Unix: 8|
|long long | 8 |8|
|unsigned long long|8|8|
|float|4|4|
|double|8|8|
|pointer|4|8|


## Windows specific

|**Type**|**32-Bit Size (Bytes)**| **64-Bit Size (Bytes)**|**Purpose/Native Type**|
|--|:--:|:--:|--|
|DWORD| 4| 4| Unsigned long|
|HMODULE|4|8|Pointer/handle to a module|
|FARPROC|4|8|Pointer to a function|
|LPSTR|4|8|Pointer to a character string|
|LPCWSTR|4|8|Pointer to a Unicode string|

# Arrays

## One-dimensional array

Collection of `<index, element>` pairs, in which the elements are of a homogenous data type.

```
Index   |0|1|2|3|4|
Element |A|B|C|D|B|
```

Assuming that an array is stored at base address `X` and is storing elements of size `S`, you can calculate the address of the element at index
`I` using the floowing equation:

```
Address(I) = X + (I*S)
```

This is referred to as _random access_ because the time to access an element does not depend on what element you are accessing.

```
Address              Memory     Variable Name

base_address      [0xfa00b4be] MajorFunction[0]
base_address + 4  [0x805031b3] MajorFunction[1]
base_address + 8  [0xfa00b588] MajorFunction[2]
base_address + 12 [0x805031be] MajorFunction[3]
base_address + 16 [0x804031be] MajorFunction[4]
```

# Bitmaps
An array variant used to represent sets is the bitmap, also known as the bit vector or bit array.  
Each value stores a boolean value {0/1}.

The windows kernel for example uses a bitmap to maintain allocated network ports.  
Network ports are represented as an unsigned short, which is 2 bytes, and provides ((2^16)-1) / 65535 possibilities.  

This is represented in a 65535-bit (~8KB) bitmap.

`4E` at 8192 bytes would indicate  that ports 1,2,3 and 6 are in use.

```
INDEX:  7 6 5 4 3 2 1 0

4E      0 1 0 0 1 1 1 0
``` 

# Records / Structure

A record / structure type can contain heterogenous elements of `<name, element>` pairs.  
In C structers have a fixed size at runtime:
```
struct Connection {     # Offsets
    short id;           # 0-1 bytes
    short port;         # 2-3 bytes
    unsigned long addr; # 4-8 bytes
    char hostname[32];  # 9-39 bytes
}
```

```
- offset -       0 1 2 3  4 5  6 7  8 9  A B  C D  E F  0123456789ABCDEF
0x7ffe85d422f0  0100 3905 0000 0000 2000 0000 0000 0000  ..9..... .......
0x7ffe85d42300  7561 7574 682e 696f 0000 0000 0000 0000  uauth.io........
0x7ffe85d42310  0000 0000 0000 0000 0000 0000 0000 0000  ................
```


# Strings

## \_UNICODE_STRING
- Stores characters as either 2- or 4-byte values
- Stores the length explicitly
- Contains only metadata 
- The buffer is a pointer to the real string address

| **Byte Range** | **Name** | **Type** | **Description**|
|--|--|--|--|
| 0-1 | Length | unsigned short | Current string length|
| 2-3 | MaximumLength | unsigned short | Maximum string length|
| 8-15 | Buffer | \* unsigned short | String address|

# Linked Lists
## Singly Linked List

Each element of the singly linked list is connected by a single link to its neighbor and, as a result, the list can be traversed in only one direction.
```
HEAD                                                    TAIL
[ELEMENT] -LINK-> [ELEMENT] -LINK-> [ELEMENT] -LINK-> [ELEMENT]
```

## Double Linked List
Stores a link to the previous and next element.

## Circular Linked List
List goes around so there is no HEAD and TAIL element.


## Embedded Doubly Linked List

Windows implementation uses a `_LIST_ENTRY64` data structure.

| **Byte Range** | **Name** | **Type** | **Description** |
|--|--|--|--|
| 0-7 | Flink | \* \_LIST\_ENTRY64 | Pointer to successor |
| 8-15 | Blink | \* \_LIST\_ENTRY64 | Pointer to predecessor |

### Lists in Physical and Virtual Memory

When analyzing memory, you frequently encounter a variety of linked-list implementations.  
For example, you can scan the physical address space looking for data that resembles the elements stored within a particular list.

Unfortunately, you cannot determine whether the data you find is actually a current member of the list from physical address space analys alone.

Dynamic data structures, such as linked lists, are a frequent target of malicious modifications because they can be easily manipulated by simply updating a few links.

# Hash Tables

Tables are often used in circumstances that require efficient insertions and searches where the data being stored is in `<key, element>` pairs.



