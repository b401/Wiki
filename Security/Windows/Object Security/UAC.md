---
title: UAC
date: M02-02-2023
author: i4
---

- UAC is **not** considered a security feature by Microsoft.

UAC uses two different kind of prompts to allow system-wide actions:
- **Consent **
	- User is already part of Admin group and can make the changes
- **Credential**
	- User is not allowed to execute the action => Account with administrative rights needs to approve.

Even accounts with admin rights, for example the .\\Administrator account, has two different kind of tokens (See [Process Integrity][./Process%20Integrity.md]) .

Processes run with medium integrity by default. This can easily be seen by running the following commands:
```cmd
C:\Users\admin> whoami /groups
...
Mandatory Label\Medium Mandatory Level
```

If we now run `cmd.exe` elevated (CTRL+LMB) or with powershell `powershell -command "Start-Process cmd -verb runas"`
```cmd
C:\Users\admin> powershell -command "Start-Process cmd -verb runas"
		v
# UAC consent prompt
		v
C:\Users\admin> whoami /groups
...
Mandatory Label\High Mandatory Level
```


## Check UAC from CLI
```
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA

Enable LUA REG_DWORD 0x1 # means it is enabled
Enable LUA REG_DWORD 0x0 # means it is disabled
```

