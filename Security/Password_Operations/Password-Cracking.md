---
title: Password-Cracking
date: M02-05-2023
author: i4
---
## John The Ripper 

- Save hashes into a text file
```
cat hashes.txt
Administrator:2892d26cdf84d7a70e2eb3b9f05c425e
```
- Start cracking
```
sudo john hashes.txt --format=NT
```

- If possible include a wordlist `--wordlist=[path to wordlist]`
- If passwords are still remain uncracked use `--rules`

### Linux
- If you have a linux shadow file you need first to unshadow (combine passwod & shadow) them.
- Can be left out if you don't have any md5 hashes. 
```
unshadow hashes.txt shadow-file.txt > unshadow.txt
```


## Hashcat
### Kerberoast
```
hashcat -m 13100 --force -a 0 hashes.kerberoast passwords_kerb.txt
```
