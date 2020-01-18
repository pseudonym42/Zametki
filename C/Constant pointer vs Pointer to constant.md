# Constant pointer vs Pointer to constant

---

Below code declares pointer `ptr` to `const int` type. You can modify the pointer itself but the object it is pointingto cannot be modified:
```c
const int a = 10;
const int *ptr = &a;
*ptr = 5; // wrong
ptr++;    // right
```
Below code declares `ptr` a `const` pointer to `int` type. You are not allowed to modify `ptr` but can modify the object pointed to by `ptr`:
```c
int a = 10;
int *const ptr = &a;
*ptr = 5; // right
ptr++;    // wrong
```
