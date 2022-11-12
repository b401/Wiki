---
title: Regex
author: i4
date: M11-02-2022
---
## Extract emails from file
```
grep -E -o "\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,6}\b" file.txt
```

## Extract valid IP addresses

```
grep -E -o "(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)" file.txt
```

## Extract passwords
```
grep -i "pwd\|passw" file.txt
```

## Extract users
```
grep -i "user\|invalid\|authentication\|login" file.txt
```


## Extract hashes
```
#Extract md5 hashes ({32}), sha1 ({40}), sha256({64}), sha512({128})
egrep -oE '(^|[^a-fA-F0-9])[a-fA-F0-9]{32}([^a-fA-F0-9]|$)' #.txt | egrep -o '[a-fA-F0-9]{32}' > md5-hashes.txt
```
### Extract valid MySQL-Old hashes
```
grep -e "[0-7][0-9a-f]{7}[0-7][0-9a-f]{7}" #.txt > mysql-old-hashes.txt
```
### Extract blowfish hashes
```
grep -e "$2a\$\08\$(.){75}" #.txt > blowfish-hashes.txt
```
### Extract Joomla hashes
```
egrep -o "([0-9a-zA-Z]{32}):(w{16,32})" #.txt > joomla.txt
```
### Extract VBulletin hashes
```
egrep -o "([0-9a-zA-Z]{32}):(S{3,32})" #.txt > vbulletin.txt
```
### Extraxt phpBB3-MD5
```
egrep -o '$H$S{31}' #.txt > phpBB3-md5.txt
```
### Extract Wordpress-MD5
```
egrep -o '$P$S{31}' #.txt > wordpress-md5.txt
```
### Extract Drupal 7
```
egrep -o '$S$S{52}' #.txt > drupal-7.txt
```
### Extract old Unix-md5
```
egrep -o '$1$w{8}S{22}' #.txt > md5-unix-old.txt
```
### Extract md5-apr1
```
egrep -o '$apr1$w{8}S{22}' #.txt > md5-apr1.txt
```
### Extract sha512crypt, SHA512(Unix)
```
egrep -o '$6$w{8}S{86}' #.txt > sha512crypt.txt
```

### Extract e-mails from text files
```
grep -E -o "\b[a-zA-Z0-9.#?$#_-]+@[a-zA-Z0-9.#?$*_-]+.[a-zA-Z0-9.-]+\b" *.txt > e-mails.txt
```

### Extract HTTP URLs from text files
```
grep http | grep -shoP 'http.#?[" >]' *.txt > http-urls.txt
```
### For extracting HTTPS, FTP and other URL format use
```
grep -E '(((https|ftp|gopher)|mailto)[.:][^ >"	]#|www.[-a-z0-9.]+)[^ .,;	>">):]' *.txt > urls.txt
#Note: if grep returns "Binary file (standard input) matches" use the following approaches # tr '[\000-\011\013-\037177-377]' '.' < #.log | grep -E "Your_Regex" OR # cat -v *.log | egrep -o "Your_Regex"

```
### Extract Floating point numbers
```
grep -E -o "^[-+]?[0-9]#.?[0-9]+([eE][-+]?[0-9]+)?$" *.txt > floats.txt
```

### Extract credit card data
#### Visa
```
grep -E -o "4[0-9]{3}[ -]?[0-9]{4}[ -]?[0-9]{4}[ -]?[0-9]{4}" #.txt > visa.txt
```
#### MasterCard
```
grep -E -o "5[0-9]{3}[ -]?[0-9]{4}[ -]?[0-9]{4}[ -]?[0-9]{4}" #.txt > mastercard.txt
```
#### American Express
```
grep -E -o "\b3[47][0-9]{13}\b" #.txt > american-express.txt
```
#### Diners Club
```
grep -E -o "\b3(?:0[0-5]|[68][0-9])[0-9]{11}\b" #.txt > diners.txt
```
#### Discover
```
grep -E -o "6011[ -]?[0-9]{4}[ -]?[0-9]{4}[ -]?[0-9]{4}" #.txt > discover.txt
```
#### JCB
```
grep -E -o "\b(?:2131|1800|35d{3})d{11}\b" #.txt > jcb.txt
```
#### AMEX
```
grep -E -o "3[47][0-9]{2}[ -]?[0-9]{6}[ -]?[0-9]{5}" #.txt > amex.txt
```

