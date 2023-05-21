---
title: Port Forwarding
date: M02-07-2023
author: i4
---
## rinetd
- `apt install rinetd`
```/etc/rinetd.conf
# bindadress    bindport  connectaddress  connectport
0.0.0.0 80 216.58.207.142 80
```
Forwards all traffic received on port 80 to 216.58.207.142:80 