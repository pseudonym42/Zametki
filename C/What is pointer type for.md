# What is pointer type for?

---
If you run the following code you will see that the pointer size is the same independent of its type:

```c
#include <stdio.h>

int main(void) {
  int *p1;
  char *p2;

  printf("%lu\n", sizeof(p1)); // 4 or 8 - depends on architecture
  printf("%lu\n", sizeof(p2)); // 4 or 8 - depends on architecture
}
```

So what do `int` and `char` used for here apart from indicating that they point to an integer and a char?

If you run the following code, you will get these results:
```c
#include <stdio.h>

int main(void) {
  int a = 1025;
  int *p;
  p = &a;
  printf("%p, %d\n", p, *p); // 0x7ffd37bf3b3c, 1025
  
  // now create a new pointer, cast it and see how it behaves
  char *p0;
  p0 = (char*)p; // casting, otherwise you'll get an error
  printf("%p, %d\n", p0, *p0); // 0x7ffd37bf3b3c, 1
}
```

So why do you get 1 when you do `*p0`? Lets first look at how 1025 is represented in binary, if say our integers take 4 bytes of memory (i.e. `sizeof(int)` is equal to 4):

`00000000 00000000 00000100 00000001`

So the least significant byte on the right hand side is 1, the next byte is 1024 so in total you get 1025.

Now, we know that `char` occupies only 1 byte of memory, so when we create `char *p0`, we actually tell the program to only dereference 1 byte when we do `*p0`. That is why we get 1 in the above example. Thus, pointer type also used during dereference, it tells how many bytes to dereference.

And here's how ponter arithmetic is affected:

```c
#include <stdio.h>

int main(void) {
  
  // ... the same above code

  printf("%d\n", *(p0+1)); // 4
}
```

Above prints 4 as `00000100` evaluates to 4 in decimal.

Please keep in mind that you still need to be careful with pointers as usual:

```c
#include <stdio.h>

int main(void) {
  int a = 1025;
  int *p;
  p = &a;
  printf("%p, %d\n", p, *p); // 0x7ffd37bf3b3c, 1025
  
  char *p0;
  p0 = (char*)p;
  printf("%p, %d\n", p0, *p0); // 0x7ffd37bf3b3c, 1
  
  // change value
  *p0 = 8;

  // below now prints 1032
  printf("%d\n", *p);
}
```

As both pointers in the above example point to the same address, change made by one of them is changing the value that gets retreived for the other one.
