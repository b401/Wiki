---
title: Kerberos
date: M02-15-2023
author: i4
---

![Simplified Kerberos](/w/images/simplified_kerberos.png)

\*1: Needs to be fulfill:
- [ ]  The TGT must have a valid timestamp (no replay detected and the request has not expired)
- [ ] The username from the TGS_REQ has to match the username from the TGT.
- [ ] The client IP address needs to coincide with the TGT IP address

\*2:  Contains the following
- [ ] The SPN to which access has been granted
- [ ] A session key to be used between the client and the SPN.
- [ ] A service ticket containing the username and group memberships along iwth the newly created session key.
-  SPN & session key are encrypted using the session key associated with the TGT
- service ticket is encrypted using the password hash of the service account (SPN associated account)

![Simplified Kerberos2](/w/images/simplified_kerberos_2.png)

1. AS-REQ (Authentication Service Request 
	- Client to KDC
	- Contains:
		- REALM (Domain)
		- Username (i4@heimat[.]erde)
  2. AS-REP 
	  - KDC to Client
	  -  Contains:
		  - encrypted ticket (TGT)
		  - encrypted client blob
			  - contains metadata & session key

## Info
- DES got replaced in 2006 (Should not be used anymore)
- ADDC acts as **KDC** (Key distribution center) & **AS** (Authentication Server)
- TGT is encrypted by a secret key that is only known to the KDC (krbtgt)
- Default TGT lifetime - 10h
- A TGT can be used to request new TGS (Access to other services)
- Stealing a TGS only allows access to that particular resource.

## Linux Usage
### Getting a TGT
`impacket-getTGT` can be used to get a TGT for a certain user for which the credentials are known or where we have the NTHash for an overpass-the-hash attack.

```
impacket-getTGT -hashes aad3b435b51404eeaad3b435b51404ee:24d8c932c6cdee6a67d723fd42ac2892 heimat.erde/i4@falke.heimat.erde
```

- To use `klist` the `REALM` in `/etc/krb5.conf` needs to be set correctly

```
$ impacket-getTGT heimat.erde/i4@falke.heimat.erde
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

Password:
[*] Saving ticket in i4@falke.ccache

$ export KRB5CCNAME=./i4@falke.ccache 
$ cp ./i4@falke.ccache /tmp/krb5cc_1000
$ klist 
Ticket cache: FILE:/tmp/krb5cc_1000
Default principal: i4@HEIMAT.ERDE

Valid starting       Expires              Service principal
04/23/2023 19:32:20  04/24/2023 05:32:20  krbtgt/HEIMAT.ERDE@HEIMAT.ERDE
        renew until 04/24/2023 19:32:19
```

### Getting an ST
Now that we have a TGT we can request ST with it. 

- This can also be done without a TGT but maybe will create more noise (overpass-the-hash)
- The `KRB5CCNAME` variable needs to point to your ccache ticket

```
$ impacket-getST -spn cifs/elstr -k -no-pass heimat.erde/i4
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Getting ST for user
[*] Saving ticket in i4.ccache
$ export KRB5CCNAME=./i4.ccache
$ cp ./i4.ccache /tmp/krb5cc_1000
$ klist
Ticket cache: FILE:./i4.ccache
Default principal: i4@HEIMAT.ERDE

Valid starting       Expires              Service principal
04/23/2023 19:39:27  04/24/2023 05:32:11  cifs/elstr@HEIMAT.ERDE
        renew until 04/24/2023 19:32:10
```




