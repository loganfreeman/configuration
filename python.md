Class attributes
---
Class attributes are attributes which are owned by the class itself. They will be shared by all the instances of the class. Therefore they have the same value for every instance. We define class attributes outside of all the methods, usually they are placed at the top, right below the class header. 

___But be careful, if you want to change a class attribute, you have to do it with the notation ClassName.AttributeName. Otherwise, you will create a new instance variable.___

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
__Registering subclasses__
```python
class DBInterfaceMeta(type):
    # we use __init__ rather than __new__ here because we want
    # to modify attributes of the class *after* they have been
    # created
    def __init__(cls, name, bases, dct):
        if not hasattr(cls, 'registry'):
            # this is the base class.  Create an empty registry
            cls.registry = {}
        else:
            # this is a derived class.  Add cls to the registry
            interface_id = name.lower()
            cls.registry[interface_id] = cls
            
        super(DBInterfaceMeta, cls).__init__(name, bases, dct)
```
bound method
---
A function is created by the def statement, or by lambda. When a function appears within the body of a class statement (or is passed to a type class construction call), it is transformed into an unbound method. When a function is accessed on a class instance, it is transformed into a bound method, that automatically supplies the instance to the method as the first self parameter.

Defines descriptors
---
If any of those methods `__get__(), __set__(), and __delete__()` are defined for an object, it is said to be a descriptor.

The default behavior for attribute access is to get, set, or delete the attribute from an object’s dictionary. For instance, a.x has a lookup chain starting with `a.__dict__['x'], then type(a).__dict__['x']`, and continuing through the base classes of type(a) excluding metaclasses.
If the looked-up value is an object defining one of the descriptor methods, then Python may override the default behavior and invoke the descriptor method instead.

vars
---
Python objects store their instance variables in a dictionary that belongs to the object. vars(x) returns this dictionary (as does `x.__dict__`).

`dir(x)`, on the other hand, returns a dictionary of x's "attributes, its class's attributes, and recursively the attributes of its class's base classes."

```python
class C(object):
    def m(self):
        print "m"

x=C()
x.m()
```
The method m is not stored in `x.__dict__`. It is an attribute of the class C. When you call `x.m()`, python will begin by looking for m in `x.__dict__`, but it won't find it. However, it knows that x is an instance of C, so it will next look in `C.__dict__`, find it there, and call m with x as the first argument.

`dict.items()` and `dict.iteritems()`
---
Originally, Python items() built a real list of tuples and returned that. That could potentially take a lot of extra memory.

Then, generators were introduced to the language in general, and that method was reimplemented as an iterator-generator method named iteritems(). The original remains for backwards compatibility.

One of Python 3’s changes is that  items() now return iterators, and a list is never fully built. The iteritems() method is also gone, since items() now works like iteritems() in Python 2.


Closure
---
A closure occurs when a function has access to a local variable from an enclosing scope that has finished its execution.
```python
def make_printer(msg):
    def printer():
        print msg
    return printer

printer = make_printer('Foo!')
printer()
```
When make_printer is called, a new frame is put on the stack with the compiled code for the printer function as a constant and the value of msg as a local. It then creates and returns the function. Because the function printer references the msg variable, it is kept alive after the make_printer function has returned.

`bound` and `unbound` methods, [First Class Everything](http://python-history.blogspot.com/2009/02/first-class-everything.html)
---
```python
class Test(object):
  def method_one(self):
    print "Called method_one"

  def method_two():
    print "Called method_two"
```
a call to a member function (like method_one), a bound function
```
a_test.method_one()
```
is translated to
```
Test.method_one(a_test)
Test.__dict__['method_one'].__get__(a_test, Test)
```
The method object binds the first parameter of a function to the instance of the class.
>In reality, the same internal object type is used to represent bound and unbound methods. One of the attributes of this object contains a reference to an instance. If set to None, the method is unbound. Otherwise, the method is bound.
