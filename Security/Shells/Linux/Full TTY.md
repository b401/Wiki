---
title: Full TTY
author: i4
date: M05-21-2023
---

## Description
Usually a shell spawned with `python3` or `ruby` is not a fully fledge shell. Good thing that there is a way to make it to one

## Spawn full TTY
```bash
(inside the nc session) CTRL+Z;stty raw -echo; fg; ls; export SHELL=/bin/bash; export TERM=screen; stty rows 38 columns 116; reset;
```

.**Script**
```bash
script /dev/null -qc /bin/bash #/dev/null is to not store anything
(inside the nc session) CTRL+Z;stty raw -echo; fg; ls; export SHELL=/bin/bash; export TERM=screen; stty rows 38 columns 116; reset;
```
