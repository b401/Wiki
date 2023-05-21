---
title: Windows memory objects
author: i4
date: M12-12-2022
---

| **Object Name** | **Structure** | **Description** |
|--|--|--|
| File | \_FILE\_OBJECT | An instance of an open file that represents a process or kernel module's access into a file. Includes permissions, regions of memory that store portions of the file's content and the name.|
| Process | \_EPROCESS | A container that allows threads to execute within a private virtual address space and maintains open handles to resources |
| SymbolicLink | \_OBJECT\_SYMBOLIC\_LINK | Created to support aliases that can help map network share paths and removable media devices to drive letters.|
|Token |\_TOKEN| Stores security context informations (SIDs and privileges) for processes and threads.|
|Thread |\_ETHREAD| An object that represents a scheduled execution entity within a process and its associated CPU context|
|Mutant|\_KMUTANT | An object that represents mutual exclusion and is typically used for synchronization purposes or to control access for a resource|
|WindowStation| tagWINDOWSTATION | A security boundary for processes and desktops, which also contains a clipboard and atom tables|
|Desktop|tagDESKTOP|An obejct that represents the displyable screen surface anc contains user objects such as windows, menus, and buttons.|
|Driver|\_DRIVER\_OBJECT| Represents the image of a loaded kernel-mode driver and contains addresses of the driver's input/output handler functions|
|Key|\_CM\_KEY\_BODY| An instance of an open registry key that contains information about the key's values and data.|
|Type | \_OBJECT\_TYPE | An object with metadata that describes the common properties of all other objects.|


To view a current up-to-date view of all available objects use winobj from sysinternals.


## \_OBJECT\_HEADER

Every execute object types are prefixed with an object header and zero or more optional headers.

```
'_OBJECT_HEADER' is (56 bytes)
0x0 : Pointer Count
0x8 : HandlerCount
0x8 : NextToFree
0x10: Lock
0x18: TypeIndex
0x19: TraceFlags
0x1a: InfoMask
0x1b: Flags
0x20: ObjectCreateInfo
0x20: QuotaBlockCharged
0x28: SecurityDescriptor
0x30: Body
```

- **PointerCount**
    - Contains the total number of pointers to the object, including kernel-mode references
- **Handlecount**
    - Contains the number of open handles to the object.
- **TypeIndex**
    - This value tells you waht type of object you're dealing with (process, thread, file).
- **InfoMask**
    - This value tells you which of the optional headers are present
- **SecurityDescriptor**
    - Stores information on the security restrictions


