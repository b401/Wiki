---
title: WFUZZ
author: i4
date: M01-15-2023
---

## POST Auth
- `--sc 200` only show 200 return codes
- Use `FUZnZ` if you have multiple `-z` arguments
	- Example: `wfuzz -z file,pw.txt -z file,user.txt -d "pw=FUZZ&user=FUZ1Z"`
```
wfuzz -c -z file,pw.txt -d "username=admin&password=FUZZ&debug=0" --sc 200 http://192.168.111.52/portal/login.php
```
