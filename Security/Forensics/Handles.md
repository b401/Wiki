---
title: Handles
author: i4
date: M10-24-2023
---

Every **\_EPROCESS** struct contains a ObjectTable (\_HANDLE\_TABLE) which stores references to process specific handles.  

- File Handles: Represent open files and directories.
- Event Handles: Used for event synchronization between threads or processes.
- Mutex Handles: Used for mutual exclusion in multi-threaded programming.
- Semaphore Handles: Used for limiting access to a resource.
- Job Handles: Represent a collection of processes as a single job object for resource allocation.
- Token Handles: Represent security tokens for user authentication and authorization.

## Finding handles with windbg
```
0:000> !handle
Handle 4
  Type         	Event
Handle 8
  Type         	Key
Handle c
  Type         	Event
Handle 10
  Type         	WaitCompletionPacket
Handle 14
  Type         	IoCompletion
Handle 18
  Type         	TpWorkerFactory
Handle 1c
  Type         	IRTimer
Handle 20
  Type         	WaitCompletionPacket
Handle 24
  Type         	IRTimer
Handle 28
  Type         	WaitCompletionPacket
Handle 38
  Type         	Directory
Handle 3c
  Type         	Event
Handle 40
  Type         	Event
Handle 44
  Type         	File
Handle 48
  Type         	File
Handle 4c
  Type         	File
Handle 50
  Type         	ALPC Port
Handle 54
  Type         	File
Handle 58
  Type         	File
Handle 5c
  Type         	File
Handle 64
  Type         	Mutant
Handle 68
  Type         	Directory
Handle 6c
  Type         	Semaphore
Handle 70
  Type         	Semaphore
Handle 80
  Type         	IRTimer
Handle 84
  Type         	TpWorkerFactory
Handle 88
  Type         	IoCompletion
Handle 8c
  Type         	WaitCompletionPacket
Handle 90
  Type         	IRTimer
Handle 94
  Type         	WaitCompletionPacket
Handle 98
  Type         	Key
Handle 9c
  Type         	Key
Handle a4
  Type         	File
33 Handles
Type           	Count
None           	4
Event          	4
File           	7
Directory      	2
Mutant         	1
Semaphore      	2
Key            	3
IoCompletion   	2
TpWorkerFactory	2
ALPC Port      	1
WaitCompletionPacket	5
```

Let's say we are interested in handle `a4` of type File.

```
0:000> !handle a4 f
Handle a4
  Type         	File
  Attributes   	0
  GrantedAccess	0x120196:
         ReadControl,Synch
         Write/Add,Append/SubDir/CreatePipe,WriteEA,ReadAttr,WriteAttr
  HandleCount  	2
  PointerCount 	32769
  No Object Specific Information available
```

as you can see, the information is kinda sparse. We don't know the exact filename or location of this handle.  
This is due to how Windows manages handles. `a4` in this example is the unique handle number that the **process** holds.  
Other processes know nothing about this handle number which the kernel translates as soon as you call functions on this handle `WriteFileW`.

To figure out which file `a4` is we can either attach a debugger in kernel mode, or use sysinternals `handle.exe`.

### Handle.exe

```
PS C:\Users\i4\source\repos\sanakan\x64\Release> C:\Users\i4\Downloads\SysinternalsSuite\handle.exe -p 5996

Nthandle v5.0 - Handle viewer
Copyright (C) 1997-2022 Mark Russinovich
Sysinternals - www.sysinternals.com

   A4: File          C:\Temp\test.txt

```
