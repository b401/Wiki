---
title: Deep Packet Inspection evasion
date: M02-12-2023
author: i4
---

#tunnel #portfun

## Tools
- `httptunnel`
- [stunnel](https://www.stunnel.org/)

## Example with httptunnel
- "Internet" deep inspects traffic and only allows http
- "Internal Compromised" is a GNU/Linux system where httptunnel is installed on.

1. Create ssh local port forwarding `ssh -L 8888:xxx:3389 user@127.0.0.1` where xxx is the IP of the back-end system
2. Create a httptunnel server on the compromised host `hts --forward-port localhost:8888 1234`
3. Create a httptunnel from the attacker machine `htc --forward-port 8080 [ip of compromised host]:1234`

All traffic sent to `localhost:8080` on the attacker machine now gets redirected through the httptunnel.

![Network Flow](/w/images/dpi.png)
