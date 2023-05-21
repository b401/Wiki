---
title: ShellBag
author: i4
date: M01-12-2023
---

## What are shellbags?

Shellbags keep information about the position, size and views of folders on Windows systems.  
This gets mainly used to reconstruct the same view if you're starting an application. (Ever wondered why `Firefox.exe` starts at the same place with the same size?)


## Why are they important

Shellbags can demonstrate access to resources well after it is no longer available. It can demonstrate access to folders, files, external storage and network resources.


## Location

Shellbags can be found under the following registry keys:

- `HKCU\Software\Microsoft\Windows\Shell`
- `HKCU\Software\Microsoft\Windows\Shell\NoRoam`
- `HKCR\Local Settings\Software\Microsoft\Windows\Sell\Bags`
- `HKCR\Local Settings\Software\Microsoft\Windows\Sell\BagsMRU`
