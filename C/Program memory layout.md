# How user application uses memory?

Any user application requires some memory to run. Here are the main sections or
segments which are going to be required for any application to run. This is also
sometimes called memory representation or memory layout:

1. Code segment - this is where the executable code is stored

2. Initialized data segment - this is where global and static variables that are
initialized are stored. This segment is divided into read-only (e.g. `const char* str = “some text”` would be stored here) and read & write area (e.g. some globals would be stored here)

3. Un-initialized data segment - this is where global and static variables that are NOT
initialized are stored. E.g. `int i;` would be stored here

4. Stack segment - this where the program stack (LIFO stack) is stored. The stack grows in the direction of the heap memory (see next section for heap memory details). The stack has a stack pointer, which always points to the top of the stack; it is adjusted each time a value is added to the the stack. Most of the time these values are stack frames. Stack frame is a set of values for a function call i.e. each time a function is called, the address of where to return to and other information about the caller is stored in a stack frame; stack frame also contains function automatic and temporary variables. This is how recursive functions in C can work. Each time a recursive function calls itself, a new stack frame is used, so one set of variables does NOT interfere with the variables from another instance of the function.

5. Heap segment - this is where dynamic memory allocation happens. The heap memory grows in the direction of the stack memory explained above. The heap memory is used when malloc, realloc, and free are used. The heap memory is shared by all shared libraries and dynamically loaded modules in a process.
