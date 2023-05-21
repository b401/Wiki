---
title: Unqoted Service Paths
date: M02-03-2023
author: i4
---

- Paths for services always need to be quoted. 

```
wmic service get name,pathname,displayname,startmode | findstr /i auto | findstr /i /v "C:\Windows\\" | findstr /i /v """

```

Consider a service called `MyService` with the following executable:
```cmd
C:\Program Files\My Service\service.exe
```

This would result in the following lookup:
```
C:\Program.exe
C:\Program Files\My.exe
C:\Program Files\My Service\service.exe
```