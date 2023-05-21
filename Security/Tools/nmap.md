---
title: nmap
author: i4
date: M11-02-2022
---
# nmap

By default nmap will scan first 1000 ports.  
This by itself will create traffic of up to *73620* bytes - depending on how many ports are responding.

Scanning all ports (1-65535) will create up to *4MB* of traffic.

## Usage
Scanning specific ports (first for open ports and afterwards for version + scripts invocation) 
```
ports=$( sudo nmap  --min-rate=1000 -T4 10.10.10.27 | grep ^[0-9] | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//)
nmap -sC -sV -p$ports 10.10.10.27 
```

## Ping sweep
just sends ICMP packets to see if hosts are online
```
$ sudo nmap -sn 10.11.1.0/24 -oG
Starting Nmap 7.70 ( https://nmap.org ) at 2019-03-04 11:27 EST
Nmap scan report for 10.11.1.5
Host is up (0.026s latency).
MAC Address: 00:50:56:89:70:15 (VMware)
Nmap scan report for 10.11.1.7
Host is up (0.026s latency).
MAC Address: 00:50:56:89:36:32 (VMware)
...
```

## TCP & UDP scan

To create a more complete picture of the target using both protocols could be beneficial

```
sudo nmap -sS -sU IP
```

## Cool things
Scanning kerberos with the -sV gives you the server time
```
nmap -sV -p 88 
88/tcp  open  kerberos-sec  Microsoft Windows Kerberos (server time: 2022-10-24 03:53:28Z)
```


## NSE

Worth to create an entry for itself.

- Location: `/usr/share/nmap/scripts/`

There is also a db that you can query: `/usr/share/nmap/scripts/script.db`

Getting information about a script:
```
nmap --script-help dns-zone-transfer
```

ARGS example:
```
nmap --script=smb-vuln-ms08-067 --script-args=unsafe=1
```

# Using NC instead
```
nc -nvv -w 1 -z 10.0.0.2 3388-3390
```
