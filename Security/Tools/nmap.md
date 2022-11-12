---
title: nmap
author: i4
date: M11-02-2022
---
# nmap

## Usage
Scanning specific ports (first for open ports and afterwards for version + scripts invocation) 
```
 ports=$(nmap -p- --min-rate=1000 -T4 10.10.10.27 | grep ^[0-9] | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//)
 nmap -sC -sV -p$ports 10.10.10.27 
```

## Cool things
Scanning kerberos with the -sV gives you the server time
```
nmap -sV -p 88 
88/tcp  open  kerberos-sec  Microsoft Windows Kerberos (server time: 2022-10-24 03:53:28Z)
```

