---
title: enumeration
author: i4
date: M11-02-2022
---

## DNS

- Replace ADDC with the IP of a known domain controller
- Replace DOMAIN.NAME with the domain you're targeting

### Getting all domain controllers


```
dig -t SRV _ldap._tcp.dc._msdcs.DOMAIN.NAME @ADDC
```

### Getting the PDC

```
dig -t SRV _ldap._tcp.pdc._msdcs.DOMAIN.NAME @ADDC`
```

### Getting all ADDC that are running a KDC (Kerberos Distribution Center) service
```
dig -t SRV _kerberos._tcp.DOMAIN.NAME @ADDC
```

### Getting the domain with DNS (TCP/UDP)
Search for a system with port 88 and/or 389, 636 open
```
dig -t ptr 0.in-addr.arpa @ADDC
```

### Finding ADCS systems

**Certificate Enrollment Policy Web Service (CEP)**
```
dig -t SRV _cep._tcp.DC.DOMAIN.NAME @ADDC
```
**Certificate Enrollment Web Service (CES)
```
dig -t SRV _ces._tcp.DC.DOMAIN.NAME @ADDC
```

## HTTP

### Show urls
```
curl 10.11.1.71 -s -L | grep "title\|href" | sed -e 's/^[[:space:]]*//'
```

### Download pages
```
curl 10.11.1.71 -s -L | html2text -width '99' | uniq
```

# ActiveDirectory enumeration

There are a lot of information that you can get out of LDAP.

- **IMPORTANT**: Always use ldaps if possible 
    -  Use `LDAPTPLS_REQCERT=never`

## Unauthenticated requests
### AD Information

```
ldapsearch -LLL -x -H ldaps://domainname -b '' -s base '(objectclass=*)'
```
[](/w/images/ad/ad_functional_level.png)



## Authenticated requests
The following requests will need an account.

```powershell
PS C:\> [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

Forest                  : heimat.erde
DomainControllers       : {falke.heimat.erde}
Children                : {}
DomainMode              : Unknown
DomainModeLevel         : 7
Parent                  :
PdcRoleOwner            : falke.heimat.erde
RidRoleOwner            : falke.heimat.erde
InfrastructureRoleOwner : falke.heimat.erde
Name                    : heimat.erde
```

## LDAP Searcher Object (Powershell)
- Creates an LDAP connection string `LDAP://[PDC]/DN`
- Create this if the system is missing RSAT
```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($domainObj.PdcRoleOwner).Name
$SearchString = "LDAP://"
$SearchString += $PDC + "/"
$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"
$SearchString += $DistinguishedName
$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString)
$objDomain = New-Object System.DirectoryServices.DirectoryEntry($SearchString, "[DOMAIN]\[user]", "[password]")
$Searcher.SearchRoot = $objDomain
```


### Getting all users
```
$Searcher.filter="samAccountType=805306368" # See top of site for different values
$Result=$Searcher.FindAll()
$Result | %{
	foreach($prop in $_.Properties) {
        $output = $prop | Select @{n='Name'; e={$_.name}}, @{n='whenchanged'; e={$_.whenchanged}}, @{n='serviceprincipalname'; e={$_.serviceprincipalname}}, @{n='memberof'; e={$_.memberof}}
	    $output
    }
}
```

### Getting users with SPNs
- See: [Service Account Attacks](/wiki/Security/Windows/AD/Service%20/Account%20/Attacks.md)
```
$Searcher.filter="(&(samAccountType=805306368)(ServicePrincipalName=*))" # See top of site for different values
$Result=$Searcher.FindAll()
$Result | %{
	foreach($prop in $_.Properties) {
        $output = $prop | Select @{n='Name'; e={$_.name}}, @{n='whenchanged'; e={$_.whenchanged}}, @{n='serviceprincipalname'; e={$_.serviceprincipalname}}, @{n='memberof'; e={$_.memberof}}
	    $output
    }
}
```

```
setspn.exe -F -Q */*
```


### Only members of DA
```
$Searcher.filter="memberof=CN=Domain Admins,CN=Users,DC=[domain],DC=[tld]"
```

### Members of a group
```
$Searcher.filter="(&(objectClass=Group)(name=[group_name]))"
$Searcher.FindAll() | %{
    $_.Properties.member
}
```

### Get a list of all domain users
```
ldapsearch -LLL -x -H ldaps://heimat.erde -D "user@heimat.erde" -w Welcome2015 -b dc=moon,dc=lab "(objectClass=user)" sAMAccountName userPrincipalName memberOf
```

### Get a list of all domain groups

```
ldapsearch-LLL -x -H ldaps://heimat.erde -D "user@heimat.erde" -w Welcome2015 -b dc=moon,dc=lab "(objectClass=group)" sAMAccountName member memberOf
```

### Get a list of all domain joined systems
```
ldapsearch-LLL -x -H ldaps://heimat.erde -D "user@heimat.erde" -w Welcome2015 -b dc=moon,dc=lab "(objectClass=computer)" name dNSHostname operatingSystem operatingSystemVersion lastLogonTimestamp servicePrincipalName
```

### Recursively lookup all members of a group
```
ldapsearch-LLL -x -H ldaps://heimat.erde -D "user@heimat.erde" -w Welcome2015 -b dc=moon,dc=lab "(&(objectClass=user)(memberof:1.2.840.113556.1.4.1941:=CN=Domain-Admins,CN=Users,dc=moon,DC=LAB))" | grep sAMAccountName| cut -d" " -f2
```

### Show all groups a user is memberOf

```
ldapsearch-LLL -x -H ldaps://heimat.erde -D "user@heimat.erde" -w Welcome2015 -b dc=moon,dc=lab "(sAMAccountName=user)" sAMAccountName userPrincipalName memberOf | grep memberOf|  cut -d "=" -f2 | cut -d"," -f1
```

## Currently logged on Users
- Examples with Powerview
```powershell
Import-Module .\PowerView.ps1
```
### NetWkstaUserEnum
- [win32API](https://learn.microsoft.com/en-us/windows/win32/api/lmwksta/nf-lmwksta-netwkstauserenum?redirectedfrom=MSDN)
- Requires local admin on the machine
```powershell
Get-NetLoggedon -ComputerName [server]
```

```
Add-Type @"
using System;
using System.Runtime.InteropServices;
public class Netapi32 {
    [DllImport("netapi32.dll")]
        public static extern int NetWkstaUserEnum(string servername, uint level, IntPtr bufptr, uint prefmaxlen, ref uint entriesread, ref uint totalentries, ref uint resumehandle);
};

[StructLayout(LayoutKind.Sequential, CharSet = CharSet.Unicode)]
public struct WKSTA_USER_INFO_0
{
    public string wkui0_username;
}
"@
#"dc01.corp.com"
$wkstaUserInfo = New-Object WKSTA_USER_INFO_0
$entriesRead = 0
$totalEntries = 0
$resumeHandle = 0

$buf = [System.Runtime.InteropServices.Marshal]::AllocHGlobal([System.Runtime.InteropServices.Marshal]::SizeOf($wkstaUserInfo))
$result = [Netapi32]::NetWkstaUserEnum(0, 0, $buf, $null, [ref]$entriesRead, [ref]$totalEntries, [ref]$resumeHandle)
if ($result -eq 0){
    $wkstaUserInfo = [System.Runtime.InteropServices.Marshal]::PtrToStructure($buf, [WKSTA_USER_INFO_0])
    $wkstaUserInfo.wkui0_username
}

```
### NetSessionEnum
- [win32API](https://learn.microsoft.com/en-us/windows/win32/api/lmshare/nf-lmshare-netsessionenum)
- Does **not** require local admin
```
Get-NetSession -ComputerName [server]
```

## Services

```
$Searcher.filter="serviceprincipalname=*http*"
```

## LAPS
### Attributes
Stores the administrator password in clear text
```
ms-Mcs-AdmPwd
```

```bash
ldapsearch -x -H "ldaps://[domain]" -D "username" -w -b "dc=domain,dc=tld" "(ms-MCS-AdmPWD=*)" ms-MCS-AdmPwd 
```

Save the timestamp of password expiration
```
ms-Mcs-AdmPwdExpirationTime
```

## RPC
```
rpcclient //heimat.erde -U heimat.erde/USERNAME%NTLM --pw-nt-hash
```

### Without creds
```
rpcclient -N //heimat.erde
```

## ID Table
  
- SAM_DOMAIN_OBJECT 0x0
- SAM_GROUP_OBJECT 0x10000000
- SAM_NON_SECURITY_GROUP_OBJECT 0x10000001
- SAM_ALIAS_OBJECT 0x20000000
- SAM_NON_SECURITY_ALIAS_OBJECT 0x20000001
- SAM_NORMAL_USER_ACCOUNT 0x30000000 (805306368)
- SAM_MACHINE_ACCOUNT 0x30000001 (805306369)
- SAM_TRUST_ACCOUNT 0x30000002
- SAM_APP_BASIC_GROUP 0x40000000
- SAM_APP_QUERY_GROUP 0x40000001
- SAM_ACCOUNT_TYPE_MAX 0x7fffffff
