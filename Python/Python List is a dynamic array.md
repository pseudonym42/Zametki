# Python List is a dynamic array

---


Sometimes dynamic arrays called mutable array, or array list. List in python is a dynamic array.

Note that there’s also array data structure in python:
```python
from array import array
```

Python arrays and lists (dynamic arrays) are different as arrays are homogeneous that is all elements should be of the same type, but lists are heterogeneous. Time-wise lists are faster but memory-wise arrays are much better as they are very much like C arrays, so arrays mostly used when you need to interface with C code.

Dynamic array is built on a fixed size array with some additional reserved free space. Elements can be added at the end of a dynamic array in constant time by using the reserved space, until this space is completely consumed. When all space is consumed, and an additional element is to be added, then the underlying fixed-sized array needs to be increased in size. Typically resizing is expensive because it involves allocating a new underlying array and copying each element from the original array. Elements can be removed from the end of a dynamic array in constant time, as no resizing is required. The number of elements used by the dynamic array contents is its logical size, while the size of the underlying array is called the dynamic array's physical size, which is the maximum possible size without relocating data. Many dynamic arrays also deallocate some of the underlying storage if its logical size drops below a certain threshold, such as 30% of the physical size. Note that the ratio by which the physical size is increased (when resizing is required) is called growth ratio and varies from language to language. For e.g. in Python it is 1.125, in Java (ArrayList) it is 1.5.

Also note that tuples in Python are just immutable lists

