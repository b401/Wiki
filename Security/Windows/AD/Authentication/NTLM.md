---
title: NTLM
date: M02-15-2023
author: i4
---

![NTLM Simplified](/w/images/ntlm_simplified.png)

- Server actually never gets the "real" hash
- Since the nonce is known the credentials can be bruteforced
- NTLM can be used in rely attacks
- NTLM can be used for [PtH](/wiki/Security/Windows/AD/Lateral%20Movement/Pass-The-Hash.md) attacks

> By using cracking software like Hashcat with top-of-the-line graphic processors, it is possible to test over 600 billion NTLM hashes every second. This means that all eight-character passwords may be tested within 2.5 hours and all nine-character passwords may be tested within 11 days.
