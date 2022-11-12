---
title: Preferred password
author: i4
date: M11-02-2022
---

When testing password fields, my preferred password is:
```
%01%E2%80%AEalert%0D%0A
```

Let's break it down:
`%01` is SOH
`%e2%80%ae` is RTLO
`%0d%0a` is CRLF

Test cases on login:
1. can I log in only using %01?
2. without the CRLF in it?
3. is trela accepted instead of alert? (due to RTLO)
