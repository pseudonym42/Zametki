# What is GIL in Python?

---


GIL is a Python Global Interpreter Lock. So what is this lock used for? Why we need one? Before answering these questions let’s first look at some rough definitions of a process and a thread.

A process is an execution environment, which is “self-sufficient” or “self-contained” i.e. it has all the required resources to run a program and does not share it with another process. A program could be your browser or some python script. And when we say resources we mean memory space, unique process ID, environment variables etc. When process is started it has a single primary thread, but process can create more threads if needed.

A thread “lives inside a process”. All threads of a process share its address space and resources i.e. they share the executable code, heap memory and data, but each thread has its own stack, which is just a part of its parent process stack: process stack is divided among its threads, so if we have a process with 5 threads, then the process stack is divided into 5 sections – a section per thread. In addition, each thread has exception handlers, scheduling priority, unique thread ID etc.

Now, when a process runs your python code – it runs a python interpreter to execute your code. Your code now is run by a single primary thread. But If there are more than one thread involved into running your code then each thread has to get a lock (GIL) to run the python interpreter started by the process. Thus, only one thread can be in a state of execution at any point in time.

You normally do not need to worry about GIL, unless you write C extensions or CPU-bound multi-threaded code. There are good visualisation article [The Python GIL Visualized](http://dabeaz.blogspot.com/2010/01/python-gil-visualized.html) and youtube talk about GIL by David Beazley:
<iframe width="560" height="315" src="https://www.youtube.com/embed/Obt-vMVdM8s" frameborder="0" allowfullscreen></iframe>

&NewLine;

CPU-bound operation examples are math related calculations, image processing etc.

Now, let’s answer some of the questions. Why do we need GIL?

Python memory management is using reference counting algorithm. This essentially means that every object created in Python has some number of references that point to the object, and when this number is equal to zero i.e. no more references point to the object then the object gets removed and the the memory occupied by the object is released.

You can check how reference counting works yourself, the below code will print 2 as the dictionary is referenced by mydict variable and reference to it is also passed as argument to sys.getrefcount() function:

```python
import sys
 
mydict = {}
print(sys.getrefcount(mydict)) # prints 2
```

Note that reference counting is used by CPython (de facto Python standard implementation) and PyPy, but not used by IronPython (C# implementation) and Jython (Java implementation).

Now, the reference counting algorithm requires some form of protection from race condition: e.g. multiple threads change value of reference count of an object simultaneously. If you do not handle this race condition there might be cases when

* memory gets released, but should not be released – this is really bad
* memory does not get released but should be – this is not as bad but still might cause a significant memory leak

So, to address this problem the main two approaches are either have a lock on every single object or have a GIL which needs to be acquired and released. The former approach have significant cons – it might very easily lead to deadlocks and a serious overhead related to a large number of lock acquire/release cycles. It was decided that the latter approach was a better solution thus GIL was introduced. But this meant that if a python program is entirely CPU-bound then would be able to only run in single-thread mode.

It is worth to note that GIL does not affect I/O-bound multi-threaded programs, because normally GIL is acquired/released by threads doing I/O tasks while they wait for I/O, where delay might be caused by a user, database, network etc.

I/O-bound operation examples are waiting for a user to input some data, waiting for a DB query to complete, waiting for remote server to response etc.


