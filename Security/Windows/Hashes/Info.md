---
title: Info
date: M02-05-2023
author: i4
---
On windows, hashes are stored in the Security Accounts Manager file `C:\Windows\System32\config\SAM` this file can't be moved or copied through regular means since it's locked by the system.

- It can be copied through shadow backups
- Hashes stored in `SAM` are not salted (same with AD accounts)
