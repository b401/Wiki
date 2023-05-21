---
title: Insecure File Permissions
date: M02-02-2023
author: i4
---

Installed software that runs as higher privileged users or get invoked as services need to be looked at exceptionally closely.

## Service - Weak Permission executable

Powershell can be used to review currently running services.

```powershell
Get-WmiObject win32_service | Select Name,State,PathName | Where {$_.State -eq 'Running'}
```

Running `icacls.exe` || `Get-ACL` we can look at the individual permissions

|MASK| PERMISSIONS|
|--|--|
|F|Full access|
|M|Modify access|
|RX|Read and execute access|
|R|Read-only access|
|W|Write-only access|

```cmd
C:\> icacls "C:\Program Files\Serviio\bin\ServiioService.exe"
C:\Program Files\Serviio\bin\ServiioService.exe BUILTIN\Users:(I)(F)
```

This file can now just be replaced with a malicious binary.

### Check service startmode
```cmd
wmic service where caption="Serviio" get name,caption,state,startmode
```

If the startmode is auto, you can check if you're able to reboot the machine:
```cmd
whoami /priv
...
SeShutdownPrivilege << # Look for this privilege
```

