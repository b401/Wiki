---
title: Exe2Hex
date: M01-29-2023
author: i4
---
#evasion 

Transforms a binary into raw hex data for #LateralToolTransfer.

## Usage
```bash
$ exe2hex

Encodes an executable binary file into ASCII text format
Restore using DEBUG.exe (BATch - x86) or PowerShell (PoSh - x86/x64)

Quick Guide:
 + Input binary file with -s or -x
 + Output with -b and/or -p
Example:
 $ /usr/bin/exe2hex -x /usr/share/windows-binaries/sbd.exe
 $ /usr/bin/exe2hex -x /usr/share/windows-binaries/nc.exe -b /var/www/html/nc.txt -cc
 $ cat /usr/share/windows-binaries/whoami.exe | /usr/bin/exe2hex -s -b debug.bat -p ps.cmd

--- --- --- --- --- --- --- --- --- --- --- --- --- --- ---

Usage: exe2hex [options]

Options:
  -h, --help  show this help message and exit
  -x EXE      The EXE binary file to convert
  -s          Read from STDIN
  -b BAT      BAT output file (DEBUG.exe method - x86)
  -p POSH     PoSh output file (PowerShell method - x86/x64)
  -e          URL encode the output
  -r TEXT     pRefix - text to add before the command on each line
  -f TEXT     suFfix - text to add after the command on each line
  -l INT      Maximum HEX values per line
  -c          Clones and compress the file before converting (-cc for higher
              compression)
  -t          Create a Expect file, to automate to a Telnet session.
  -w          Create a Expect file, to automate to a WinEXE session.
  -v          Enable verbose mode
```

## Example

### CMD output
- `-p` increases the size to almost 2.5x
- `-b` increases the size to almost 4x
```bash
$ exe2hex -x ./nc.exe -p nc.cmd
[*] exe2hex v1.5.1
[+] Successfully wrote (PoSh) /home/i4/ftp/nc.cmd
$ head ./nc.cmd
echo|set /p="">nc.hex
echo|set /p="4d5a90000300000004000000ffff0000b800000000000000400000000000000000000000000000000000000000000000000000000000000000000000800000000e1fba0e00b409cd21b8014ccd21546869732070726f6772616d2063616e6e6f742062652072756e20696e20444f53206d6f64652e0d0d0a2400000000000000">>nc.hex

...

powershell -Command "$h=Get-Content -readcount 0 -path './nc.hex';$l=$h[0].length;$b=New-Object byte[] ($l/2);$x=0;for ($i=0;$i -le $l-1;$i+=2){$b[$x]=[byte]::Parse($h[0].Substring($i,2),[System.Globalization.NumberStyles]::HexNumber);$x+=1};set-content -encoding byte 'nc.exe' -value $b;Remove-Item -force nc.hex;"
```

