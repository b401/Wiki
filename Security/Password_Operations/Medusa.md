---
title: Medusa
date: M02-05-2023
author: i4
---
#rdp #brute-force 
## Syntax
- `-h` target IP or domain
- `-u` username to attack
- `-P` Path to a wordlist
- `-M` Module name (See Modules)
- `-m` Param to pass to the module
```bash
medusa -h [target] -u [username] -P [path to passwordlist] -M [module] -m [Module parameter1] -m [Module parameter2] 
```

### Modules
|Module | Description |
| -- | -- |
| cvs | CVS targets |
| ftp | FTP targets |
| http | Http targets |
| IMAP | IMAP targets |
| MSSQL | MSSQL targets|
| Mysql | mysql targets|
|nntp | nntp targets |
|pcanywhere | PcAnywhere targets |
| pop3| pop3 targets|
| postgres | PSQL targets|
| rexec | rexec sessions |
| rlogin | rlogin targets |
| rsh | rsh targets |
| smbnt | SMB (LM/NTLM/LMv2/NTLMv2) targets |
| smtp-vrfy|(VRFY/XPN/RCPT TO)|
|SMTP | SMTP auth with TLS |
| SNMP | SNMP community strings |
| ssh | SSH v2 auth|
| svn | Subversion targets |
| telnet | telnet targets |
|vmauthd | VMWare Authentication Daemon (VAMI)|
|vnc| VNC auth|
|web-form| Web form|
| Wrapper | Generic Wrapper|