---
title: Process Integrity
date: M02-02-2023
author: i4
---

Every object (process, user, etc.) gets assigned an [access token](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-tokens) by the [Local Security Authority](https://learn.microsoft.com/en-us/windows/win32/secauthn/lsa-authentication)

From Windows Vista on-wards, processes run on four integrity levels:
- **System integrity** process: SYSTEM rights
- **High integrity** process: administrative rights
- **Medium integrity** process: standard user rights
- **Low integrity** process: very restricted rights often used in sandboxed processes