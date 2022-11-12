---
title: QEMU dynamic resolution
author: i4
date: M11-02-2022
---
# QEMU dynamic resolution

*/etc/udev/rules.d/50-x-resize.rules*
```
ACTION=="change",KERNEL=="card0", SUBSYSTEM=="drm", RUN+="/usr/local/bin/x-resize" 
```

*/usr/local/bin/x-resize*
```
#!/bin/sh 
PATH=/usr/bin
desktopuser=$(/bin/ps -ef  | /bin/grep -oP '^\w+ (?=.*vdagent( |$))') || exit 0
export DISPLAY=:0
export XAUTHORITY=$(eval echo "~$desktopuser")/.Xauthority
xrandr --output $(xrandr | awk '/ connected/{print $1; exit; }') --auto
```

