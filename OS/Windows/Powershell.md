---
title: Powershel
author: i4
date: M01-23-2023
---

## Stuff

### Listing Event ID

```
(Get-WinEvent -ListProvider {ProviderName}).Events | Format-Table Id, Description
```

Example:
```
(Get-WinEvent -ListProvider Microsoft-Windows-Sysmon).Events | Format-Table Id, Description
```
