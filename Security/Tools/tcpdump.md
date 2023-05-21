---
title: tcpdump
author: i4
date: M01-10-2023
---

## Filter

- `-n` skip dns lookups
- `-r` read from file
```
tcpdump -n -r file.pcap
```

Filtering can just be done by writing the filters on the commandline:
```
tcpdump -n dst host x.x.x.x -r file.pcap
```

## Inspect

- `-X` view hex dump in ascii format 

```
tcpdump -nX -r file.pcap
```

## Header Filter

- `PSH` flag enforces immediate delivery of a packet
- `ACK` is set in all 

Only show requests with `PSH` and `ACK` set

00011100

```
sudo tcpdump -A -n 'tcp[13] = 24' -r file.pcap
```

Show all URG packets:
- ` tcpdump 'tcp[13] & 32 != 0'`

Show all ACK packets:
- ` tcpdump 'tcp[13] & 16 != 0'`

Show all PSH packets:
- ` tcpdump 'tcp[13] & 8 != 0'`

Show all RST packets:
- `tcpdump 'tcp[13] & 4 != 0'`

Show all SYN packets:
- `tcpdump 'tcp[13] & 2 != 0'`

Show all FIN packets:
- `tcpdump 'tcp[13] & 1 != 0'`

Show all SYN-ACK packets:
- `tcpdump 'tcp[13] = 18`
