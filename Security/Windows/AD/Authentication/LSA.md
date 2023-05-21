---
title: LSA
author: i4
date: M05-21-2023
---

## Description

Use: [Mimikatz](/wiki/Security/Windows/Hashes/Mimikatz.md)

LSA secrets can be used to decrypt certain stored credentials on a device.

Dumped LSA credentials have the following format:
```
[domain]/user:$DCC2$10240#[User]#[hash]
```

Hashcat can be used for [cracking](/wiki/Security/Password_Operations/Password-Cracking.md)
```
hashcat -m2100 [hash.file] [wordlist.file]
```

