---
title: Running commands
date: M02-02-2023
author: i4
---

Sometimes the need for a quick and dirty executable is there that just should execute something small.

We can use the `system` function from `stdlib.h`for that purpose.

```C
#include <stdlib.h>

void main() {
	system("whoami");
}
```

Follow [Cross-Compilation](/wiki/Security/Linux/languages/Cross-Compilation.md) for instructions to compile it on GNU/Linux.
