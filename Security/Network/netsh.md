---
title: netsh
date: M02-12-2023
author: i4
---

`netsh` can be used to create a `portproxy` similar to [SSH Tunneling](./SSH%20Tunneling.md).

## Requirements
- `nt authority\SYSTEM` or `admin` + [UAC bypass](/wiki/Security/Windows/Privilege%20Escalation/UAC%20Bypass.md)
- `IP Helper` _service_ needs to be running
	- `sc query iphlpsvc`
	- `Get-Service iphlpsvc`
- `IPv6` needs to be enabled ?? WORKS WITHOUT
	- `Get-NetAdapterBinding -ComponentID ms_tcpip6`
	- `wmic nicconfig where IPEnabled=true get IPAddress` (For v4)
	- `wmic nicconfig where IPEnabled=true get IPv6Address` (For v4) -> Will throw an error if there is no ipv6

## Local Port To Remote Port forwarding
```
netsh interface portproxy add v4tov4 listenport=[localport] listenaddress=[publicIP] connectport=[remotePort] connectaddress=[remoteIP]
```

### Example
```
C:\Windows\system32>netsh interface portproxy add v4tov4 listenport=1337 listenaddress=192.168.213.10 connectport=445 172.16.213.5 
netsh interface portproxy add v4tov4 listenport=1337 listenaddress=192.168.213.10 connectport=445 172.16.213.5


C:\Windows\system32>netstat -anp TCP | find "1337"
netstat -anp TCP | find "1337"
  TCP    192.168.213.10:1337    0.0.0.0:0              LISTENING
  TCP    192.168.213.10:64475   192.168.45.213:1337    ESTABLISHED

```

- This does not mean that the local FW allows incoming connections to 1337/TCP

**Adding FW rule to allow it**
```
netsh advfirewall firewall add rule name="forward 1337 to 445 remote" protocol=TCP dir=in localip=192.168.213.10 localport=1337 action=allow
```


```
$ nmap -p 1337 192.168.213.10
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-12 21:33 CET
Nmap scan report for 192.168.213.10
Host is up (0.032s latency).

PORT     STATE SERVICE
1337/tcp open  waste
```
