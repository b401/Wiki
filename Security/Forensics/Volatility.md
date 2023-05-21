---
title: Volatility
author: i4
date: M12-10-2022
---

## Tool

### Why volatility?

- **A single, cohesive framework**
    - Supports Windows, GNU/Linux and Mac
- **Open Source**
    - Read, Learn, Expand
- **Python**
    - Easy to customize
- **Scriptable**
- **Fast and efficient**
- **Great community**
- Focused on forensics, IR and malware

### What volatility is not

- **System memory acquisition tool**
    - Use another tool to dump memory
- **no GUI**
- **not bug free**

## Install

### Windows
- [C++ build tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/)
    - Desktop development with C++
    - MSVC 
    - Windows xx SDK
```
git clone https://github.com/volatilityfoundation/volatility3.git
```

## Framework

### VTypes
Vtypes is volatillity's structure definition and parsing language.

For example the following C struct would be built in VTypes as follows:

```
struct {
    int pid;
    int parent_pid;
    char name[10];
    char * command_line;
    char * ptv;
};
```

```
'process' : [ 26, {
    'pid' : [ 0, ['int']],
    'parent_pid' : [ 4, ['int']],
    'name' :[ 8, ['array', 10, ['char']]],
    'command_line' : [ 18, ['pointer', ['char']]],
    'ptv' : [ 22, ['pointer', ['void']]],
}]
```
#### Generating VTypes

Thanks to [pdbparse](https://code.google.com/p/pdbparse) proprietary binary PDB files formats can be changed into the open VType language that Volatitily supports.

Kernel symbols can be found in the specific NT kernel modules (`ntoskrnl.exe`, `ntkrnlpa.exe` etc.)

### Overlays

Overlays allow the automatically generation for structure definitions.  
For example for a `void *` void pointer there is no way to to now what datatype the pointer points to at the time of allocation.

An overlay can be applied with the following syntax:

Example from _VTypes_ struct.
```
'process' : [ None, {
    'ptv' : [ None, ['pointer'], ['process']]],
}
```
`None` is being used since we don't want to change the size. The only thing we want the overlay to change is the type of ptv from `void *` to `process *`.

### Objects and Classes
A Volatility object is an instance of a structure that exists at a specific address within an address space.

Example code which creates an object called `_EPROCESS`
```
import volatility.obj as obj

class _EPROCESS(obj.CType):
    def is_suspicious(self):
        // to things
        if self.ImageFileName == "fakeav.exe":
            return True
```


### Profiles

A profile is a collection of the VTypes, voerlays, and object classes for a specific OS version and architecture.  
A profile may also include:
- Metadata
    - build, kernel versions etc.
- System call information
    - Indexes and names of system calls
- Constants
    - Global variables
- Native types
    - low-level types for native languages, including sizes
- System map
    - Addresses of critical global variables and functions (Linux and Mac only)

### Address Spaces (AS)
An AS is an interface that provides consistent access to data in RAM and handles virt-to-phys-address translation when necessary.

### Plugins
To write an analysis plugin, a Python class that inherits from commands.Command and overrides a few of the base methods needs to be created.

Example:
```
import volatility.utils as utils
import volatility.commands as commands
import volatility.win32.tasks as tasks

class ExamplePlugin(commands.Command):
    def calculate(self):
        // do most work here
        addr_space = utils.load_as(self._config)
        for proc in tasks.pslist(addr_space):
            yield proc
    def render_text(self, outfd, data):
        for proc in data:
            outfd.write(f"Process: %{proc.ImageFileName\n")
```

Plugins need to be saved in the `volatillity/plugins` directory (or any other subdirectory) or point to them with `--plugins=DIR`

