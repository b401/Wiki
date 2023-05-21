---
title: Privilege Escalation
date: M02-02-2023
author: i4
---

- [Basics](https://fuzzysecurity.com/tutorials/16.html)

### Manual
#### CMD
##### Version
```cmd
systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
```
##### Installed drivers
```cmd
driverquery /v
# Check C:\Windows\System32\DRIVERS & C:\Program Files for the .inf file to check the version
```

##### Get local users
```cmd
C:\Users\i4> whoami # This will probably trigger an alert
localmachine\i4
C:\Users\i4> net user
C:\Users\i4> net localgroup Users 
C:\Users\i4> net localgroup Administrators
C:\Users\i4> wmic useraccount where "localaccount=true" list brief
```
##### Get local groups
```cmd
C:\Users\i4> net localgroup # List 
```

#### Powershell
##### Get local users
```ps1
Get-LocalUser
get-WmiObject -Query "select * from Win32_UserAccount where localaccount=true
```
##### Get local groups
```ps1
Get-LocalGroup
Get-LocalGroupMember [groupname]
```



### Automated
#### windows-privesc-check
- [github](https://github.com/pentestmonkey/windows-privesc-check)
```cmd
C:\temp\> windows_privesc_check.exe

Usage: windows_privesc_check.exe (--dump [ dump opts] | --dumptab | --audit) [examine opts] [host opts] -o report-file-stem

Options:
  --version             show program's version number and exit
  -h, --help            show this help message and exit
  --dump                Dumps info for you to analyse manually
  --dumptab             Dumps info in tab-delimited format
  --audit               Identify and report security weaknesses
  --pyshell             Start interactive python shell

  examine opts:
    At least one of these to indicate what to examine (*=not implemented)

    -a, --all           All Simple Checks (non-slow)
    -A, --allfiles      All Files and Directories (slow)
    -D, --drives        Drives
    -e, --reg_keys      Misc security-related reg keys
    -E, --eventlogs     Event Log*
    -f INTERESTING_FILE_LIST, --interestingfiledir=INTERESTING_FILE_LIST
                        Changes -A behaviour.  Look here INSTEAD
    -F INTERESTING_FILE_FILE, --interestingfilefile=INTERESTING_FILE_FILE
                        Changes -A behaviour.  Look here INSTEAD.  On dir per
                        line
    -G, --groups        Groups
    -H, --shares        Shares
    -I, --installed_software
                        Installed Software
    -j, --tasks         Scheduled Tasks
    -k, --drivers       Kernel Drivers
```
##### Dump users
- `--dump` view output
- `-G` list groups
```cmd
windows-privesc-check2.exe --dump -G
```