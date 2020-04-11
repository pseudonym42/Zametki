Programming languages used to be black-and-white - compiled or interpreted, but nowadays that is not quite true. As a lot of languages now compile to bytecode and then this bytecode is interpreted (that is executed) during runtime. Some are still fully compiled like C where source code is compiled to machine code directly. Main fundamental difference between compiled and interpreted languages is that in compiled language the code gets compiled to machine code once and then executed when required, but most interpreted languages convert to bytecode once and then the interpreter needs to execute the bytecode every runtime. That is why compiled languages considered faster as interpreted languages deal with bytecode and perform it every runtime. Performing machine code is much faster.

Python is considered an interpreted language because Python programs are executed by an interpreter, but first .py source code is compiled to bytecode (for e.g. in case of CPython it is .pyc) and then this bytecode is actually executed by an interpreter (specifically - by virtual machine). A virtual machine is part of an interpreter. Compared to Java VM - python VM is more complex, b’cos Java VM gets the compiled bytecode as ‘ready’ that is both typing and binding already done. The python VM though has to pause before the execution of each operation to determine the primitive data types for each variable or data structure involved in the operation. That is why Java has better performance as Python VM has more work to do, Java bytecode is much closer to machine code in this sense, but the reason Java working this way (converting to bytecode first rather than machine code like C) is to achieve platform independence.

Python bytecode is cross platform but it is not not compatible across versions. Python 2.6 cannot execute Python 2.5 bytecode files.

Python interpreter can be written in different languages. The most popular implementations are CPython (in C) which is standard implementation, Jython (in Java), IronPython(in .NET), PyPy (in RPython).
Main difference (apart from obviously being written in different languages) is that:

* CPython converts to Python bytecode that gets interpreted by CPython VM, so CPython is both the compiler and the interpreter
* Jython converts to java bytecode that gets interpreted by Java VM, additionally, it is able to import and use any Java class like a Python module, Jython is just a compiler
* IronPython converts to IL (.NET) bytecode that gets interpreted by CLR, IronPython is just a compiler
* PyPy is a bit different as it has JIT and this gives 5 times faster performance. The advantage of JIT is that it compiles the bytecode before execution to machine code and then unlike other interpreters executes machine code not bytecode.

To check the implementation run the following in your terminal:

    python -c "import platform; print platform.python_implementation()"

or the same can be achieved with:

	echo "import platform; print platform.python_implementation()" | python
