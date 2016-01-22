Modules and Packages
---
The first time a module is loaded into a running Python script, it is initialized by executing the code in the module once.

Two very important functions come in handy when exploring modules in Python - the dir and help functions.

Writing Python modules is very simple. To create a module of your own, simply create a new .py file with the module name, and then import it using the Python file name (without the .py extension) using the import command.

Packages are namespaces which contain multiple packages and modules themselves. They are simply directories, but with a twist.

Each package in Python is a directory which MUST contain a special file called __init__.py. This file can be empty, and it indicates that the directory it contains is a Python package, so it can be imported the same way a module can be imported.

If we create a directory called foo, which marks the package name, we can then create a module inside that package called bar. We also must not forget to add the __init__.py file inside the foo directory.

To use the module bar, we can import it in two ways:

```python
import foo.bar
```

```python
from foo import bar
```

Load module helper
---
```python
def load_module(test_module):
    module = __import__(test_module)
    parts = test_module.split('.')[1:]
    while parts:
        part = parts.pop()
        module = getattr(module, part)
    return module
```
