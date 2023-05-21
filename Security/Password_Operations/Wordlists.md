---
title: Wordlists
date: M02-05-2023
author: i4
---

- See also [[Brute-Force]]

## Cewl
Cewl can help creating context passwords from websites
```bash
cewl [url] -m [min amount of characters] -w [output]
```

## JohnTheRipper
- Can be used to modify existing password lists
- More about rule generation [here](https://www.openwall.com/john/doc/RULES.shtml)

Change existing rule set:
```bash
$ vim /etc/john/john.conf
... 
926 [List.Rules:Wordlist]
...
979 # Add two numbers at the end of each password
980 $[0-9]$[0-9]
```

Run mutation on a generated wordlist:
```
$ john --wordlist=[path to wordlist] --rules --stdout > mutated_wordlist.txt
```


## Public Wordlists
- [SecLists](https://github.com/danielmiessler/SecLists)


## Tools
- See [Brute-Force](./Brute-Force.md)
