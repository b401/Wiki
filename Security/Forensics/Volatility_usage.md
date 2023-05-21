---
title: Volatility3
author: i4
date: M12-10-2022
---

## Usage

_Install: [Plaso](https://github.com/log2timeline/plaso) for timeline creation_

### Basic Commands
```
vol -f <FILENAME> --profile=<PROFILE> <PLUGIN> [ARGS]
```

__Example:__
```
vol -f ./memory.dmp windows.pslist
```

Plugins are always prefixed with for which platform it can be used on.

```
windows.**
linux.**
```

### Output

Output needs to be done with shell redirect.

```
vol -f ./memory.dmp windows.pslist >> pslist.txt
```

Sometimes plugins have their own output setting
```
python3 vol.py -f mem.dump -o analysis timeliner --create-bodyfile
```
This will create a bodyfile under `./analysis/volatility.body`

### Extract running process (Binary)

Get a list of all running processes and their commandlines
```
python3 vol.py -f $dump windows.cmdline
```

Dump a specific PID
```
python3 vol.py -f $dump windows.pslist --pid 1136 --dump
```

###  Extract process memory

```
python3 vol.py -f $dump windows.memmap --pid {PID} --dump
```

### Extract registry value

```
python3 vol.py -f $dump windows.registry.printkey -K "Software\Microsoft\Windows\CurrentVersion\Run"
```

### Extract File
```
python3 vol.py -f $dump windows.dumpfiles --virtaddr {addr}
```

### Pool Table scan
Pooltag.txt: https://github.com/jjzhang166/windbgtool/blob/master/Dependecies/x64/triage/pooltag.txt

__Vol2__
```
vol.exe -f $dump --profile=xxx pooltracker --tags=Proc,File,Driv,Thre
```

__Vol3__
```
python3 vol.py -f $dump windows.poolscanner 
```

## Network

__Vol2__
```
volatility –profile WinXPSP2x86 -f cridex.vmem sockets
volatility –profile WinXPSP2x86 -f cridex.vmem connscan
```

