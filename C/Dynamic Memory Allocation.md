# Dynamic Memory Allocation

Functions `malloc`, `calloc`, `realloc` and `free` are used to allocate/deallocate memory on heap. These functions should be used with great caution to avoid memory leaks and dangling pointers.

## malloc()

Provided in `stdlib.h`. This functions allocates the memory and returns the pointer to the allocated memory. For example:
```c
int *ptr = (int*) malloc(10 * sizeof(int));
```
The following is true for `malloc`:

* If the system is not able to allocate the requested memory on heap then malloc returns `NULL`.
* If size is zero (`malloc(0)`), then malloc returns either a `NULL` pointer or a valid pointer which can be passed to free function for successful memory dealocation. The actual value depends on the implementation.
* malloc returns a void pointer which need to casted to appropriate type before dereferencing. (The way we typecasted it to `int*` above)
* Memory returned by malloc is not initialized an holds garbage value

## calloc()

This function also allocates memory on heap like `malloc` does. The only difference is that `calloc` also initialize the memory with zero (`malloc` returns uninitialized memory as explained above)

## realloc()

This function changes the size of memory block on heap and returns a pointer to the new block of memory. It allows to make the change witout loosing the content. However, one needs to be extremly careful with `realloc` so not to create dangling pointers. See example below:
```c
int *ptr1 = (int*) malloc(5 * sizeof(int));
int *ptr2 = ptr1;
ptr2 = (int*) realloc(ptr2, 10 * sizeof(int));
```

Int this case both ptr1 and ptr2 are pointing to the same memory location.

When `realloc` is called, the memory location pointed to by both pointers may get deallocated (in case the contiguous space is not available just after the memory block). Now `ptr2` will now point to the newly shifted location on the heap (returned by `realloc`), but `ptr1` is still pointing to the old location (which is now deallocated). Hence pointer `ptr1` is a dangling pointer.

The following is true for `realloc`:

* If pointer passed to `realloc` is `NULL`, then it will behave exactly like `malloc`
* If the size passed is zero, and passed pointer param is not `NULL` then the call is equivalent to `free` (see more details about his function below)
* If the area is moved to new location then function `free` on the previous location is called
* If contents will not change in the existing region. The new memory (in case you are increasing memory in `realloc`) will not be initialized and will hold garbage value
* If `realloc` fails the original block is left untouched; it is not freed or moved

## free()

Provided in `stdlib.h`. This function frees the memory on the heap, pointed to by a pointer. The following is true for `free`:

* Passed pointer param must be pointing to a memory which is allocated using `malloc`, `calloc` or `realloc`.
* If function is called on a memory which is not on heap or on a dangling pointer, then the behavior is undefined.
* If passed pointer param is `NULL`, then `free` does nothing and returns (So, its ok to call free on null pointers)

Examples:

```c
int x = 2;

int *ptr = &x;
free(ptr); // Undefined behavior

int *ptr2; // Un-initialized, hence dangling pointer
free(ptr2); // Undefined behavior

int *ptr3 = NULL;
free(ptr3); // Ok
```
