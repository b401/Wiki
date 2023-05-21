---
title: THC-Hydra
date: M02-05-2023
author: i4
---

#ssh #brute-force #http-auth

## Attacking SSH
```bash
hydra -l [user] -P [path to wordlist] ssh://127.0.0.1
```

## Attacking FTP
```
hydra ftp://[target] -l [user] -P [path to wordlist]
```

## Attacking POST
Syntax:
```bash
hydra [target] http-post-form "[path to form]:[param1]=[value]&[param2]=^PASS^:[Text to check for]" -l [username] -P [path to wordlist] -vV -f
```

```bash
hydra http-form-post -U
Module http-post-form requires the page and the parameters for the web form.

By default this module is configured to follow a maximum of 5 redirections in
a row. It always gathers a new cookie from the same URL without variables
The parameters take three ":" separated values, plus optional values.
(Note: if you need a colon in the option string as value, escape it with "\:", but do

Syntax:   <url>:<form parameters>:<condition string>[:<optional>[:<optional>]
First is the page on the server to GET or POST to (URL).
Second is the POST/GET variables (taken from either the browser, proxy, etc.
 with url-encoded (resp. base64-encoded) usernames and passwords being replaced in the
 "^USER^" (resp. "^USER64^") and "^PASS^" (resp. "^PASS64^") placeholders (FORM PARAME
Third is the string that it checks for an *invalid* login (by default)
 Invalid condition login check can be preceded by "F=", successful condition
 login check must be preceded by "S=".
 This is where most people get it wrong. You have to check the webapp what a
 failed string looks like and put it in this parameter!
The following parameters are optional:
 C=/page/uri     to define a different page to gather initial cookies from
 (h|H)=My-Hdr\: foo   to send a user defined HTTP header with each request
                 ^USER[64]^ and ^PASS[64]^ can also be put into these headers!
                 Note: 'h' will add the user-defined header at the end
                 regardless it's already being sent by Hydra or not.
                 'H' will replace the value of that header if it exists, by the
                 one supplied by the user, or add the header at the end
Note that if you are going to put colons (:) in your headers you should escape them wi
 All colons that are not option separators should be escaped (see the examples above a
 You can specify a header without escaping the colons, but that way you will not be ab
 in the header value itself, as they will be interpreted by hydra as option separators
```
