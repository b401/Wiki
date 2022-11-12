---
title: Preventing clickjacking
author: i4
date: M11-02-2022
---
# Clickjacking

Both `X-Frame-Options` and `Content-Security-Policy` response headers define whether or not a browser should be allowed to embed or render a page in an <iframe> element. For example, setting X-Frame-Options: deny will prevent browsers from rendering your web application in an <iframe> element.
```
# Nginx
add_header X-Frame-Options "sameorigin" always;

# Apache
header always sex X-Frame-Options "sameorigin"

# Content Security Policy
Content-Security-Policy: frame-src https://www.uauth.io
```

