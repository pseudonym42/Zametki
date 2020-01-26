# Pointer vs Arrays vs Strings

---

The following link can help us understand the difference between pointer and array [Pointer vs Array](http://www.c-faq.com/aryptr/practdiff.html):

> An array is a single, preallocated chunk of contiguous elements (all of the same type), fixed in size and location
> 
> A pointer is assigned to point to space allocated elsewhere and it can be reassigned. A pointer is a much more general data structure.

Note that pointer that doesn't point to any object is called null pointer.

Arrays are in a sense "second-class citizens" in C and C++. They cannot be assigned, passed as function arguments, or compared for equality. Code that manipulates arrays usually does so using pointers to the individual elements of the arrays, with some explicit mechanism to specify how long the array is (see the next section for more details).

A string is defined as "a contiguous sequence of characters terminated by and including the first null character (null terminator)".

A major source of confusion is the fact that name of an array is often implicitly converted to a pointer value in most contexts (this process often called *decay to pointer*), let's look into this in the next section

---

Ok, now let's consider this example:
```c
int a[7]; 
```
`a` contains space for seven integers, and you can put a value in one of them with an assignment, like this:
```c
a[3] = 9;
```
Now let's look at a pointer:
```c
int *p;
```
`p` doesn't contain any spaces for integers, but it can point to a space for an integer. We can, for example, set it to point to one of the places in the array a, such as the first one:

```c
p = &a[0];
```

Array dereferencing operator in C, `[ ]`, is defined in terms of pointers. `x[y]` means: start with the pointer `x`, step `y` elements forward after what the pointer points to, and then take whatever is there. Using pointer arithmetic syntax, `x[y]` can also be written as `*(x+y)`

For this to work with a normal array, such as our `a`, the name `a` in `a[3]` must first be converted to a pointer (to the first element in `a`). Then we step 3 elements forward, and take whatever is there. In other words: take the element at position 3 in the array. (Which is the fourth element in the array, since the first one is numbered 0)

What can be confusing is that you can also write this:

```c
p = a;
```
This does not copy the contents of the array a into the pointer p (whatever that would mean). Instead, the array name a is converted to a pointer to its first element. So that assignment does the same as the previous one.

Now you can use p in a similar way to an array:
```c
p[3] = 17;
```
So, in summary, array names in a C program are (in most cases) converted to pointers.

But there are some exceptions i.e. cases when array name does not decay to a pointer, for example when we use the `sizeof` operator on an array. If `a` was converted to a pointer in this context, `sizeof` `a` would give the size of pointer and not of the actual array, which would be rather useless, so in that case `a` means the array itself.

---

Now when we understand arrays better let's compare them with strings (char arrays).

Consider below two statements in C. What is the difference between the two?
```c
char s1[] = "somestring";
char *s2  = "somestring";
```

The statement `char s1[] = "somestring"` creates a character array which is like any other array and we can do all array operations. The only special thing about this array is, although we have initialized it with 10 elements, its size is 11 (Compiler automatically adds '`\0`'); so the `printf("%lu", sizeof(s1));` will give you 11.

The statement `char *s2  = "somestring"` creates a string literal. The string literal is stored in the read-only part of memory by most of the compilers. The C and C++ standards say that string literals have static storage duration, any attempt at modifying them gives undefined behaviour. So in this case `s2` is just a pointer and like any other pointer stores address of string literal. The `printf("%lu", sizeof(s2));` will give you 4 or 8, depending on the system.
