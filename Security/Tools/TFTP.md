---
title: TFTP
date: M01-29-2023
author: i4
---

- Client is not installed by default on systems running Windows 7+, W2k8+
- File will be saved at `/srv/tftp`

## Server
```bash
apt update && apt install tftpd-hpa
vim /etc/default/tftpd-hpa
 	TFTP_OPTIONS="--secure --create"
systemctl start tftpd-hpa
```

## Client
```cmd
C:\> tftp -i [ip] put [file]
```
