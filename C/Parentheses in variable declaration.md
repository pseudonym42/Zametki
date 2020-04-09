# What do parentheses in a C variable declaration mean?


Sometimes one can encounter code like

```c
#include <stdio.h>

void main() {
    int (*data)[2] = {6, 4};
}
```

Why do we need parentheses here?

In C brackets `[]` have higher precedence than the asterisk `*`. So if we need to declare a
pointer to an array, we need to supply parentheses to override this.

If we do not put parentheses around `data` above then we end up having an array of
pointers:
```c
#include <stdio.h>

void main() {
    int * data[2];
    int a = 9;
    data[0] = &a;
}
```

So, if we need a pointer to an array we need use parentheses. There is however a  simpler way to create a pointer to an array:
```c
#include <stdio.h>

void main() {
    int b[3] = {4,9,3};
    int * p1 = b;
    printf("%d\n", p1[1]); // -> this will print 9
}
```
In the above code however we introducing additional steps and array name.

---

Let's see where else parentheses could be useful. Imagine you want to create a pointer to an array of pointers:
```c
#include <stdio.h>

void main() {
    int * (* x)[15];
}
```

You can also encounter cases like this:
```c
#include <stdio.h>

void main() {
    int (*data[2])[5];
}
```
Here, data is an array of 2 elements, and each element contains a pointer to an array of 5 `int`s. Here's the usage:

```c
int x1[5];
data[0] = &x1;
data[1] = &x1;

// this will give error: out of bounds, crash data has no 3rd element
data[2] = &x1;

// this will give compiling error: each element of data must point to
// an int[5] not an int[10]
int y1[10];
data[0] = &y1;
```

Sometimes parentheses also used when operating with multi-dimensional arrays. For example:

```c
#include <stdio.h>

void main(void) {
    int x[3][2] = {{9,44}, {14,98}, {7,51}};

    // this is a pointer to an array of 2 int's
    int (*p)[2] = x;
    printf("%d\n", * p[0]); // prints 9
    printf("%d\n", * p[1]); // prints 14
    printf("%d\n", * p[2]); // prints 7
}
```
Very important thing to understand here is that in the above code ` = x` returns a pointer to 1-D array of 2 integers. That is why the left hand side of that expression has to be what it is - pointer to 1-D array of 2 integers - i.e. `int (*p)[2]`.

So why ` = x` returns a pointer to 1-D array of 2 integers one might ask? Array name always points to the first element in the array. In the above array `x` the first element is a 1-D array of 2 integers, i.e. it points to `{9,44}` not `int 9`.

This is why in our above example where we did:
```c
int x[3][2] = {{9,44}, {14,98}, {7,51}};
int (* p)[2] = x;
```
We cannot just write:
```c
int x[3][2] = {{9,44}, {14,98}, {7,51}};
int * p = x; // <-- this fails compilation
```
as it will give us compilation error, because we are trying to create a pointer to `int` but point it to 1-D array of 2 integers.
