Using `typedef` allows you to define your own type, thus you can avoid typing `struct`. For example:

```c
typedef struct {
  int x, y;
} Point;

/* this function returns a struct of type Point */
Point point_new(int x, int y) {
  Point a;
  a.x = x;
  a.y = y;
  return a;
}
```
However you should distinguish between a tag and a type name. In C, when defining a new struct type the name appearing right after the `struct` keyword is a tag. So, in the example below `x` is a tag:

```c
struct x {
  // some code here
};
```

Having the above definitions the following will give you an error:

```c
x first;    /* gives error */
x *second;  /* gives error */
```

You have to use the following syntax:

```c
struct x first;
struct x *second;
```

The above applies to names of unions and enumerations as well.

In C, tags are distinct from all other names (e.g. names for functions, types, variables, and enumeration constants). C compilers maintain tags in a symbol table that's conceptually (if not physically) separate from the table that holds all other names. Thus, it is possible for a C program to have both a tag and another name with the same spelling in the same scope. For example,
```c
struct s s;
```
is a valid declaration which declares variable `x` of type `struct x`. It may not be good a practice though.

However, you can define an alias using `typedef`. For example:

```c
struct x {
  // your code here
};
typedef struct x MyStruct;
```
or
```c
typedef struct x {
  // your code here
} MyStruct;
```

The above lets you use `MyStruct` in place of `struct x`, here `MyStruct` is a type name. Now you can use it list this:
```c
MyStruct first;
MyStruct *second;
```

Now you cannot do the following anymore (which is good):
```c
MyStruct MyStruct; /* gives error */
```

The tag name in a `struct`, `union`, or `enum` definition is optional, that is why a lot of developers omit it:

```c
typedef struct {
  // you code here
} MyStruct;
```
