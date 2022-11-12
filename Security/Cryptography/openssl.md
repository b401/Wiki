---
title: OpenSSL
author: i4
date: M11-02-2022
---
# OpenSSL
```
# Connect to an endpoint using SSL/TLS
openssl s_client -connect 10.10.10.127:443

# Read certificate
openssl x509 -in ca.cert.pem -text

# Create new RSA2048 key
openssl genrsa -out newuser.key 2048

# Create a CSR
openssl req -new -key newuser.key -out newuser.csr 

# Generate certificate from a private key. 
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes 

# Use a CSR to generate a new signed certificate
openssl x509 -req -in newuser.csr -CA intermediate.cert.pem -CAkey intermediate.key.pem -CAcreateserial -out newuser.pem -days 1024 -sha256 

# Create a pkcs12 certificate private+public in pfx format
openssl pkcs12 -export -out newuser.pfx -inkey newuser.key -in newuser.pem ```

## If you only need to create a client certificate from a Ca certificate and the CA key, you can do it using:
openssl pkcs12 -export -in ca.cert.pem -inkey ca.key.pem -out client.p12
```

# Encrypt with openssl
```
openssl rsa -in key.ssh.enc -out key.ssh
```
## Decrypt
```
openssl enc -aes256 -k <KEY> -d -in backup.tgz.enc -out b.tgz
```
