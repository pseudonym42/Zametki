# How to convert int to string?

---
The short answer is:

```c
#include <stdio.h>
#include <stdlib.h>

snprintf(str, size, "%d", x);
```

The longer is:

First you need to find out sufficient size. `snprintf` tells you length if you call it with NULL, 0 as first parameters:

`snprintf( NULL, 0, "%d", x );`

Allocate one character more for null-terminator.

```c
int x = -42;
int length = snprintf(NULL, 0, "%d", x);
char* str = malloc(length + 1);
snprintf(str, length + 1, "%d", x);
...
free(str);
```
If works for every format string, so you can convert float or double to string by using "%g", you can convert int to hex using "%x", and so on.

For more information about `snprintf` see [snprintf (C++ Reference)](http://www.cplusplus.com/reference/cstdio/snprintf/)