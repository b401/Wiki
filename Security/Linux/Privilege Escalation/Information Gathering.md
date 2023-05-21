---
title: Privilege Escalation
date: M02-02-2023
author: i4
---

## Information Gathering
- [Basics](https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/)

### Manual
#### Hostname
```bash
hostname
uname -a 
```
#### version
```bash
cat /etc/os-release
lsb_release -a
uname -a
cat /etc/issue
uname -r
arch
```

#### Local users
```bash
cat /etc/passwd
```
#### Local groups
```bash
cat /etc/groups
```

## Automated
### unix-prives-check
- [Link](https://pentestmonkey.net/tools/audit/unix-privesc-check)
```bash
./unix-privesc-check standard > output.txt
```