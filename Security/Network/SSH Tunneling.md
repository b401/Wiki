---
title: SSH Tunneling
date: M02-07-2023
author: i4
---
- `-L` local port that will be forwarded to a remote address and port
- `-N` Do not execute a remote command
- **Syntax**: `ssh -N -L [bind_address:]port:host:hostport [username@address]`

## Example
```
ssh -N -L 127.0.0.01:445:445:10.0.0.1 user@kolibri.heimat.erde
```
Forwards all traffic from 445 through kolibri over ssh to 10.0.0.1

![SSH Forwarding](/w/images/ssh_forwarding.png)

## Remote Port Forwarding
- `-R` can be used for the other way around (Bind remote port to local port)
- Syntax: `ssh -N -R [bind_address:]port:host:hostport [remote]`
- elster is the attacker
- kolibri is already compromised and can ssh to elster 

The idea is to port forward a local port to a remote machine ( attacker ) this happens in case a machine doesn't expose a port remotely

```
ssh -N -R 192.168.119.162:1337:127.0.0.1:3306 user@kolibri
```

![Remote Forwarding](/w/images/remote_forwarding_ssh.png)
