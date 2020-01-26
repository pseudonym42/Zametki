---
title: What is __pycache__ in Python?
---

# What is \_\_pycache\_\_ in Python?

--- 


In Python 3.2 and later, when you run a python program, the interpreter compiles your python code to bytecode and places all these bytecode files (\*.pyc or \*.pyo) in the \_\_pycache\_\_ folder. This bytecode folder gets created in the same place where your source code is.

As noted above, there are normally two types of bytecode files python interpreter creates: bytecode-compiled (\*.pyc) and optimized bytecode-compiled (\*.pyo) files.

These bytecode files allow starting python programs a bit faster, but you can delete them if you prefer – keep in mind though that they will get re-generated again next time you run your python program. These bytecode files also get re-created when your source code files change.

If you use CPython interpreter then there are two ways to stop interpreter from creating the bytecode files. First option is to use `-B` flag:

`$ python -B myscript.py`

Your second option is to set the environment variable PYTHONDONTWRITEBYTECODE to any non-empty string:

`PYTHONDONTWRITEBYTECODE="thanks"`
