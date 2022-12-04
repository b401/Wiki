---
title: Translating Virtual to Physical
author: i4
date: M12-04-2022
---

- Application: `svchost.exe`  
- PID: `1024`
- Virtual address: `0x10016270` 
- CR3 for PID 1024: `0x7401000`
    - contains physical address of the initial structure

![](/w/images/paging_structure.png)

## Step One
Convert the virtual address (`0x10016270`) from hex to binary because we need to work with ranges of address bits:

```
| Page Dir | Page Table|Address offset|   
|          |           |              |
0001 0000 0000 0001 0110 0010 0111 0000
```

|Paging Structure|VA Bits|Binary|Hex|
|---|---|---|---|
| Page directory Index | Bits 31:22 | 0001000000 | 0x40|
| Page table index | Bits 21:12 | 0000010110 | 0x16 |
| Address offset | Bits 11:0 | 0001001110000| 0x270|

By multiplying the page directory index by the size of the entry (4bytes) and then adding the page directory base, `0x7401000` (CR3) we get the physical address of the _PDE_ (Page Directory Entry)

```
PDE address = 0x40 * 4 + 0x7401000 = 0x7401100
> rax2 =16 0x40*4+0x7401000
0x7401100
```

## Step Two (Getting the PDE)
Next we need to read the value stored at the PDE address (`0x7401100`).  
This value is stored in little endian. 

```
Value at 0x7401100 => 0x17bf9067
```

## Step Three (Getting the PTE)
Bits 31:12 of the PDE provide the physical address fot the base of the page table.
Bits 21:12 of the virtual address provide the page table index because the page table is composed of 1024 entries. (`0000010110 => 0x16`)

You can calculate the physical address of the PTE by multiplying the size of the entry (4 bytes) by the page table index and then adding the value of the page table base.

```
PTE address = 0x16 *4 + 0x17bf9000 = 0x17bf9058
> rax2 =16 0x16*4+0x17bf9000 
0x17bf9058
```

The value stored at `0x17bf9058` is `0x170b6067`.

## Step Four (Getting the physical address)

Bits 31:12 (from `0x170b6067`) of the physical address are from the PTE and bits 11:0 are from the virtual address.

```
# 31:12 of PTE
0001 0111 0000 1011 0110 0000 0000
0x170B600
# 11:00 of PTE (VA)
0010 0111 0000
0x270

Physical address = 0x170b6000 + 0x270 = 0x170b6270
> rax2 =16 0x170b6000 + 0x270 
0x170b6270
```

![](/w/images/adress_translation_example.png)

# PAE

For 32bit systems with PAE enabled it's a little different:

![](/w/images/address_translation_example_pae.png)
