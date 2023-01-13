---
title: Persistence
author: i4
date: M01-04-2023
---

## Autostart Extension Points (ASEP)

ASEP refers to locations which can be used to achive peristance on a target.

### Run/RunOnce keys

Those two keys are often used by malware to achive user level persistence.

- `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run`
- `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce`

At system level the following keys can be used.

- `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`
- `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce`
- `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run`


### BookExecute key

[`smss.exe`](https://en.wikipedia.org/wiki/Session_Manager_Subsystem) launches before the Windows subsystem.  
It's used to load the hive at `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\hivelist`.

`smss.exe` will launch anything present in the `BootExecute` key at:
- `HKEY_LOCAL_MACHINE\SYSTEM\ControlSet00x\Control\Session Manager\BootExecute`

This key should always contain `autocheck autochk *` if there is another value, it was changed manually or by malware.

### WinLogon

#### Userinit

`WinLogon` processes uses the values specified in the `Userinit` key to launch login scripts and other processes.

- `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Winodws NT\CurrentVersion\Winlogon\UserInit`

Usually `userinit` points to `C:\Windows\system32\userinit.exe` but if this key can be altered then any other binary can be started 

#### Notify

`WinLogon` handles [Secure Attention Sequence (`CTRL+ALT+DELETE`)](https://en.wikipedia.org/wiki/Secure_attention_key) which loads the 
dll located at the following key:

- `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify`

This key does not exist per default and needs to be created.

#### Explorer

This key should point to `explorer.exe` (not the full path!).

- `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell` 

The boot key should point to the location under Winlogon only:

- `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini\boot` 

Default value should be:

`SYS:Microsoft\Windows NT\CurrentVersion\Winlogon`

## Startup Keys

There are multiple keys that can be altered to achive startup execution by pointing them to another location:

- `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders`
- `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders`
- `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders`
- `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders`

## Services

Services that are required to run at boot like drivers or event log are located at the following key:

- `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services`

Malicious binaries can also be configured in services to run if the service fails to start.

There are some other keys that are used to run background services like `Remote Registry`.

- `HKEY_LOCAL_MACHINE\Software\Microsoft\Window\CurrentVersion\RunServicesOnce`
- `HKEY_LOCAL_MACHINE\Software\Microsoft\Window\CurrentVersion\RunServices`

Those keys are not present by default.


## Browser Helper Objects (BHO)

Registry keys are used to tell IE which dlls to load. This might be no longer applicable.

- `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects`

Various subkeys can be used to define custom dlls.

## AppInit

-`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows\AppInit_DLLs`

Lists dlls that are loaded by `User32.dll`. As most applications load `User32.dll` it's an excellent way for malware persistence.

## File Association keys

- `HKEY_LOCAL_MACHINE\Software\Classes` 
- `HKEY_CLASSES_ROOT`

Define how certain file types should be loaded. 

## DLL Search Order Hijacking

After `Safe DLL search mode` was enabled (which is default on new systems) the OS will check whether the DLL is already loaded in memory
or is a part of the `Known DLLs` registry key located at:

- `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs` if the OS cannot find the DLL at either of these, the following search order will be used:

1. The directory from where the application was launched
2. `C:\Windows\System32`
3. `C:\Windows`
4. Current Working Directory
5. Directories defined in the PATH variable


## Misc registry

### Terminal Server

- `HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Utilities\query`

A new `REG_MULTI_SZ` entry with name `x` and a value pointing to an executable.

Executing `query x` will now execute the binary. 

*Terminal Services* does not need to be running.
