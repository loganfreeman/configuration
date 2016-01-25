Class attributes
---
Class attributes are attributes which are owned by the class itself. They will be shared by all the instances of the class. Therefore they have the same value for every instance. We define class attributes outside of all the methods, usually they are placed at the top, right below the class header. 

**But be careful, if you want to change a class attribute, you have to do it with the notation ClassName.AttributeName. Otherwise, you will create a new instance variable. **

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

#### How does Python knows where to find the foo module?

When a module named spam is imported, the interpreter first searches for a built-in module with that name. If not found, it then searches for a file named spam.py in a list of directories given by the variable **sys.path**. **sys.path** is initialized from these locations:
- the directory containing the input script (or the current directory).
- PYTHONPATH (a list of directory names, with the same syntax as the shell variable PATH).
- the installation-dependent default.

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
Custom Metaclasses
---
Everything is an object in Python, including class.
```python
class InterfaceMeta(type):
    def __new__(cls, name, parents, dct):
        # create a class_id if it's not specified
        if 'class_id' not in dct:
            dct['class_id'] = name.lower()
        
        # open the specified file for writing
        if 'file' in dct:
            filename = dct['file']
            dct['file'] = open(filename, 'w')
        
        # we need to call type.__new__ to complete the initialization
        return super(InterfaceMeta, cls).__new__(cls, name, parents, dct)
```
Now we can use it like:
    
```python
class Interface(object):
    __metaclass__ = InterfaceMeta
    file = 'tmp.txt'
    
print(Interface.class_id)
print(Interface.file)
```
