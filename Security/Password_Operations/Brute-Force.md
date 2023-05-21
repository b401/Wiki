---
title: Brute-Force
date: M02-05-2023
author: i4
---

As soon as we have an understanding what the policy dictates we can create a mask or configure the right settings for brute-forcing.

## Example
```
[Capital Letter]  [2 x lower case letters]  [2 x special chars]  [3 x numeric]
```

### Crunch
Crunch is a [[Wordlists]] generator that can be used with a certain set of rules.
It uses placeholders which translate to different characters for generating the list.

| Placeholder | character translation |
|--|--|
|@|Lower case alpha characters|
|,|Upper case alpha characters|
|%|Numeric characters|
|^|Special characters including space|


To generate a list with a minimum and maximum of 8 characters in length and the example set we can use the following command:

- Be aware that it will take up a lot of space.

```bash
$ crunch 8 8 -t ,@@^^%%%
Crunch will now generate the following amount of data: 172262376000 bytes
164282 MB
160 GB
0 TB
0 PB
Crunch will now generate the following number of lines: 19140264000 
```

To define a specific character set we can use the following syntax:
```bash
$ crunch 4 6 0123456789ABCDEF -o Upper_Number.txt
```


Crunch can also use a charset defined in a file
- Check `/usr/share/crunch/charset.lst` for different options
```
$ crunch 4 6 -f /usr/share/crunch/charset.lst mixalpha -o output.txt
```

## Tools
- [THC-Hydra](https://github.com/vanhauser-thc/thc-hydra)
- [Medusa](http://h.foofus.net/?page_id=51)
- [Crowbar](https://github.com/galkan/crowbar)
- [Spray](https://github.com/Greenwolf/Spray)
- [Patator](https://github.com/lanjelot/patator)