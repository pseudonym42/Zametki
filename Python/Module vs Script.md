Whenever you run any `.py` file, Python interpreter treats it like a script or a module:

When you run a python file like this: `python file.py` - it runs as a script (top-level script)

When you run a python file like this: `python -m file.py` - it runs like a module. So what is the difference?

Before answering this question let’s look at some examples. Say you have a structure:

	my_folder/
        module/
            module.py
            __init__.py
		script/
            script.py

Now say, you have a function in module.py and you’d like to import it in script.py and use it, so in script.py you do:

	import module

If you run your script inside folder `my_folder` like:

    python script/script.py

then you will get `ImportError`, as Python is looking for modules in `sys.path` but your `module.py` is not there. To get the module (in our case `module.py`) added to `sys.path` you either run script as top-level (and this is what you doing with `script.py` when you run `python script/script.py`, and this obviously does not help as `module.py` is not getting added to `sys.path` this way), or install the module as python eggs, or add directory `my_folder` to PYTHONPATH somehow yourself. The only solution here is to put all your scripts in the same folder (e.g. folder `script`)

	my_folder/
        script/
            module.py
            __init__.py
            script.py

and now if you run `python script/script.py` your `script.py` parent folder will be added to `sys.path` and both `script.py` and `module.py` are recognized as top-level scripts.

Note that even if you run script from parent folder of my_folder, still - only parent folder of the top-level script (in our case `script.py`) will be added to `sys.path` (in our case folder `script/`). This Python’s normal behaviour - when some script run directly i.e. as script not as module then its parent directory is added to `sys.path`.
Let’s consider another scenario when all your scripts in the same directory and you try to import a module that is available in `sys.path` but you get `AttributeError` when you run file as script. For example you have a structure like below and you are located in the parent folder of pkg:

	pkg/
        __init__.py
        main.py
        string.py (this file is empty)

Here you have a module `string.py` which replicates a name of in-built module “string” but is empty. So if you do the following inside your `main.py` and run it as script i.e. `python pkg/main.py`

	import string
    print(string.ascii_uppercase)

Now your imports are working but you really want to import built-in Python’s string module. You will get an AttributeError:

    AttributeError: 'module' object has no attribute 'ascii_uppercase'

And even adding absolute imports (so Python searches for module in top-level not in the current package) is not going to help here:

    from __future__ import absolute_import

And here’s why:

Whenever you run a script like script (i.e. `python pkg/main.py`), Python adds directory `pkg` to the `sys.path` automatically and then Python searches for module `string.py` inside that directory and obviously finds your empty `string.py` module and imports it but then throws error as your `string.py` module is actually empty and does not contain “ascii_uppercase” variable.
To stop Python from adding current directory to `sys.path` you need to run your script as module:

    python -m pkg.main

inside `pkg` parent directory and have

    __future__ import absolute_import

at the top of your `main.py` file, if you don't add

    __future__ import …

then it will be importing local `string.py`, which is empty.

Note - special case when you run your code in Python console. In this case the name of that interactive session is `__main__`, for e.g. if you go into console (remember you located in parent folder of pkg), start Python and run:

	import pkg.main

The above will print: ABCDEFGHIJKLMNOPQRSTUVWXYZ, note that without absolute_import you will get `AttributeError` again.

So launching Python and then importing `pkg.main` is equivalent to using the `-m` switch that is running that script as module.

So why running scripts as modules resolve the problem. Because running as a module is not adding scripts parent folder to `sys.path` as described above. The thing is that in Python there should be only one `main-entry-point`  script which should initiate/import all the other scripts as required. If you run a script as top-level one and that script is not supposed to be run like that - all relative imports are going to break. When file is run as top-level script Python assumes that all the required modules will be in the sub-folders of current location of the top-level script.

When a file is loaded, it is given a name (which is stored in its `__name__` attribute). If it was loaded as the top-level script, its name is `__main__`. If it was loaded as a module, its name is the filename, preceded by the names of any packages of which it is a part, separated by dots. So for instance you have structure:

    main.py
    package/
        __init__.py
        moduleA.py
        subpackage1/
            __init__.py
            moduleX.py

and you are inside the parent directory of the directory `package`. If you imported `moduleX` inside you `main.py` (note: imported, not directly executed), its name would be:

    package.subpackage1.moduleX

If you imported `moduleA`, its name would be

    package.moduleA

However, if you directly run `moduleX` as script, its name will instead be `__main__`, or if you directly run `moduleA` as script, its name will be `__main__`. When a module is run as the top-level script, it loses its normal name and its name is instead `__main__` and so all the relative imports (e.g. `package.subpackage1.moduleX`) inside other modules break.
So the bottom line is - always create a "main script" (e.g. like `main.py` above) in the root directory of your project and put all modules to be imported in subdirectories. `main.py` can then access all modules directly through their package names (i.e. the names of the respective folders they're in).
Alternatively, if your structure is like:

    project_1/
        __init__.py
        celery.py
        run_tasks.py  < - - - let’s say this is you main script
        another_module.py
        tasks.py
    project_2/
        __init__.py
        other_tasks.py

and inside `run_tasks.py` (or even inside `another_module.py`, doesn't matter) you’d like to use relative imports and say use some tasks from `project_2.other_tasks` then you would need to switch to parent folder of directory `project_1` and run (this will make `__name__` of run_tasks - `__main__`):

	python -m project_1.run_tasks

now inside all modules within directory `project_1` you can do imports like

    from project_1.tasks import some_task

or

    from project_2.other_tasks import some_other_task
