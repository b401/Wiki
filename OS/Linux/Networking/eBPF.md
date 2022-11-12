---
title: eBPF
date: M11-02-2022
author: i4
---
# eBPF

**Requirements**:
* linux headers (``apt install linux-headers-$(uname -r)``)
* bpfcc tools (``apt install bpfcc-tools``)

eBPF allows to run sandboxes programs in a privileged context inside the kernelspace.

## Hook Overview
eBPF programs are event-driven and run when the kernel or an application passes a certain hook point.
This includes system calls, function entry/exit, kernel tracepoints, network events and several others.

```
Process
  |
Syscall
  |
  |------- eBPF
  |
Scheduler
```

If a hook does not exist for a particular need, it is possible to create a kernel probe (kprobe) or user probe (uprobe) to attach eBPF programs almost anywhere in kernel or user applications.

## How are eBPF programs written?
Usually it's not written directly but indirectly through projects like Cilium, bcc, or bpftrace which offer an abstraction on top of eBPF.

If there is a need to compile eBPF directly clang can be used.

```
clang -target bpf
```

## Ensure safety
The following requirements need to be fulfilled:

* The process loading the eBPF program holds the required capabilities (privs). Unless unprivileged eBPF is enabled, only privileged processes can load eBPF programs.
* The program does not crash or otherwise harm the system.
* The program always runs to completion (i.e. the program does not sit in a loop forever, holding up further processing).

## Maps
eBPF programs are able to store data in maps which in turn can be accessed via other eBPF programs.

```
Process
  ^
  |                                   Syscall
Syscall                    |------------|
  |                        |          Sockets
  -------------------- eBPF Maps ---> TCP/IP
                                      Network Device
```

Example of data structures that can be used in eBPF maps:
* Hash tables, Arrays
* LRU (Least Recently Used)
* Ring Buffer
* Stack Trace
* LPM (Longest Prefix match)
* ...

## Helper calls
eBPF programs cannot call directly into arbitrary kernel functions since this would limit eBPF to certain kernel versions. Instead the kernel offers helper functions through a well-known and stable API.

Examples for helper functions:
* Generate random numbers
* Get current time & date
* eBPF map access
* Get process/cgroup context
* Manipulate network packets and forwarding logic

## Tail & Function Calls
Function calls allow defining and calling functions within an eBPF program.
Through tail calls it is possible to chain different eBPF programs and replace the execution context, similar to how the execve() system call operates.


## eBPF Safety

### Required Privileges
Unless unprivileged eBPF is enabled, all processes that intend to load eBPF programs into the GNU/Linux kernel must be running in privileged mode (root) or required the CAP_BPF capability. 

If unprivileged eBPF is enabled, unprivileged processes can load certain eBPF programs subject to a reduced functionality set and with limited access to the kernel.

### Verifier

Privileged and unprivileged eBPF programs still must adhere to the eBPF verifier which ensures the safety of the program itself.

Examples:
* Programs are validated to ensure they always run to completion, e.g. an eBPF program may never block or sit in a loop forever. eBPF programs may contain so called bounded loops but the program is only accepted if the verifier can ensure that the loop contains an exit condition which is guaranteed to become true.
* Programs may not use any uninitialized variables or access memory out of bounds.
* Programs must fit within the size requirements of the system. It is not possible to load arbitrarily large eBPF programs.
* Program must have a finite complexity. The verifier will evaluate all possible execution paths and must be capable of completing the analysis within the limits of the configured upper complexity limit.

### Hardening
After the verification completed successfully the program runs through a hardening process. This step includes:

* **Program execution protection**: The kernel memory holding an eBPF program is protected and made read-only. If for any reason, whether it is a kernel bug or malicious manipulation, the eBPF program is attempted to be modified, the kernel will crash instead.
* **Mitigation against Spectre**: Under speculation CPUs may mispredict branches and leave observable side effects that could be extracted through a side channel. To name a few examples: eBPF programs mask memory access in order to redirect access under transient instructions to controlled areas, the verifier also follows program paths accessible only under speculative execution and the JIT compiler emits Retpolines in case tail calls cannot be converted to direct calls.
* **Constant blinding**: All constants in the code are blinded to prevent JIT spraying attacks. This prevents attackers from injecting executable code as constants which in the presence of another kernel bug, could allow an attacker to jump into the memory section of the eBPF program to execute code.

### Abstracted Runtime Context
eBPF programs cannot access arbitrary kernel memory directly. The access is done through the exposed eBPF helpers.

