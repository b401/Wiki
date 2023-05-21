---
title: Evil-Winrm
author: i4
date: M05-21-2023
---

Be aware that users connecting over winrm(s) require local admin

#p5985 #p5985 #authentication-required #local-admin

## Kerberos

```
# Get TGT
impacket-getTGT -dc-ip 10.0.0.100 heimat.erde/i4
# Set KRB5 var
export KRB5CCNAME=./i4.ccache
# Copy to tmp
cp i4.ccache /tmp/krb5cc_0
```

Set kerberos realm

_/etc/krb5.conf_
```
[realms]
HEIMAT.ERDE = { # replace with your domain name
	kdc = falke.heimat.erde # Point to your DC
}
```

Now check if the TGT shows up
```
$ klist
Ticket cache: FILE:./i4.ccache
Default principal: i4@HEIMAT.ERDE

Valid starting       Expires              Service principal
04/23/2023 13:20:18  04/23/2023 23:20:18  krbtgt/HEIMAT.ERDE@HEIMAT.ERDE
        renew until 04/24/2023 13:20:17
```

This should enable you to connect to the target machines over winrm with kerberos auth.

```
evil-winrm -r heimat.erde -i elster.heimat.erde
```

## AMSI bypass
```
*Evil-WinRM* PS C:\> Bypass-4MSI
```

## Reflective Injection
[[Donut-Maker]]
```
Donut-Loader
```
