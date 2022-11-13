---
title: enumeration
author: i4
date: M11-02-2022
---

# ActiveDirectory enumeration


There are a lot of information that you can get out of LDAP.

- IMPORTANT: Always use ldaps if possible 
  Use `LDAPTPLS_REQCERT=never`

## Unauthenticated requests

```
ldapsearch -LLL -x -H ldaps://domainname -b '' -s base '(objectclass=*)'
```
[](/w/images/ad/ad_functional_level.png)

## Authenticated requests
The following requests will need an account.

### Get a list of all domain users
```
ldapsearch-LLL -x -H ldaps://moon.lab -D "user@moon.lab" -w Welcome2015 -b dc=moon,dc=lab "(objectClass=user)" sAMAccountName userPrincipalName memberOf
```

### Get a list of all domain groups

```
ldapsearch-LLL -x -H ldaps://moon.lab -D "user@moon.lab" -w Welcome2015 -b dc=moon,dc=lab "(objectClass=group)" sAMAccountName member memberOf
```

### Get a list of all domain joined systems
```
ldapsearch-LLL -x -H ldaps://moon.lab -D "user@moon.lab" -w Welcome2015 -b dc=moon,dc=lab "(objectClass=computer)" name dNSHostname operatingSystem operatingSystemVersion lastLogonTimestamp servicePrincipalName
```

### Recursively lookup all members of a group
```
ldapsearch-LLL -x -H ldaps://moon.lab -D "user@moon.lab" -w Welcome2015 -b dc=moon,dc=lab "(&(objectClass=user)(memberof:1.2.840.113556.1.4.1941:=CN=Domain-Admins,CN=Users,dc=moon,DC=LAB))" | grep sAMAccountName| cut -d" " -f2
```

### Show all groups a user is memberOf

```
ldapsearch-LLL -x -H ldaps://moon.lab -D "user@moon.lab" -w Welcome2015 -b dc=moon,dc=lab "(sAMAccountName=darth.vader)" sAMAccountName userPrincipalName memberOf | grep memberOf|  cut -d "=" -f2 | cut -d"," -f1
```

## DNS

### Getting the domain with DNS (TCP/UDP)
Search for a system with port 88 and/or 389, 636 open
```
dig ptr 0.in-addr.arpa @{DNS}
```

## LAPS
### Attributes
Stores the administrator password in clear text
```
ms-Mcs-AdmPwd
```

Save the timestamp of password expiration
```
ms-Mcs-AdmPwdExpirationTime
```

