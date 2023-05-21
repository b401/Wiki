---
title: Cached Domain logon
author: i4
date: M05-21-2023
---

Windows 10 saves the last 10 hashes as `MSCash` value in the registry.
They differ from the regular NTHash and can't be used for PTH attacks. Instead they need to be cracked first.

## Algorithm
The only value that differs is the username. This means that this hash will be the same across different domains and machines if the username & password are the same.
```
PBKDF2(HMAC-SHA1, 10240, DCC1, username)
```

_Python3 implementation_
```python
>>> from passlib.hash import msdcc2`
>>> hash = msdcc2.encrypt("Password123", user="test2")`
>>> print hash`
'd7f91bcdec7c0df39396b4efc81123e4'`
```

## Dumping
```
impacket-secretsdump
```
## Cracking
### John
```
$ cat hash.txt
test2:d7f91bcdec7c0df39396b4efc81123e4
$ john --format=mscash2 ./hash.txt 
```

### hashcat
```
$ cat hash.txt
test:$DCC2$10240#test#d7f91bcdec7c0df39396b4efc81123e4
$ hashcat -m2100 ./hash.txt ~/usr/share/wordlists/rockyou.txt
```
