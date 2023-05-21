---
title: Dynamic Port Forwarding
date: M02-08-2023
author: i4
---

## Create a socks proxy with SSH
- Creates a local SOCKS4 proxy listening on 127.0.0.1:1337
```
ssh -N -D 127.0.0.1:1337 <username>@<ssh server>
```

