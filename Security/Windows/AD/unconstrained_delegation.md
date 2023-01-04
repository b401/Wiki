---
title: Unconstrained Delegation
author: i4
date: M12-06-2022
---

## Review
Get a list of machines that have "Trust this computer for delegation to any Service" set.
```
Get-ADComputer -Filter {TrustedForDelegation -eq $true} -prop trustedfordelegation,serviceprincipalname,description
```
