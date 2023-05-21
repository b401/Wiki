---
title: Donut-Maker
author: i4
date: M05-21-2023
---

#evasion 

## Description
Donut generates the representative shellcode out of executables. This can be used for reflective memory injection to evade EDR & AV detection.

If on Windows use https://github.com/TheWover/donut

## Usage
URL: https://github.com/Hackplayers/Salsa-tools/blob/master/Donut-Maker/donut-maker.py

```bash
python3 ./donut-maker.py -i ./ELF-Binary -o out.bin -p "whatever"
```
