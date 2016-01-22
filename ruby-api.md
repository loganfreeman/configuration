Classes and Modules
---
The following six C functions constitute to principle API for defining Classes,  Modules  and Methods.

1. rb_define_class
2. rb_define_class_under
3. rb_define_module
4. rb_define_module_under
5. rb_define_method
6. rb_define_singleton_method

__Defining a Class__

Rb_define_class defines a new Top-Level Class with the a given name and superclass.   
Classes and Superclasses for built-in types are of the form:  _rb_cXxxxxx_,  _rb_mXxxxxx_ and _rb_eXxxxxx_.   
For example, to inherit from Class Object  use _rb_cObject_.   
Top-level Classes are Classes that are NOT nested inside another Class. 

The function rb_define_class_id(...)  creates a new class object and metaclass object. 

1. The first task is to request a Object Sized (20 Bytes)  block
of memory in ObjectSpace.
2. The block is set to zero.
3. The Class Object is initalized: xx->klass is set to "rb_cClass" 
and xx->flags is set to "T_CLASS".
4. The xx->super entry is set to the input parameter super.
5. xx->iv_tbl is set to zero.
6. xx->m_tbl is initialized as empty numeric Hash Table.
7. The xx->flags is or'ed with the Super->Flags and the Taint  
bit is set.
8. Create a Metaclass and insert it between this class and the 
defined Klass Entry.
9. Set "__classid__" in xx->iv_tbl with the value of rb_id2name(id).
10. Add name/id pair to *rb_class_tbl* and rb_cObject's iv_tbl.

Metaclasses are similarly constructed:

1. Requests a Object Sized  block of memory from ObjectSpace.
2. The block is set to zero.
3. The Class Object is initalized: xx->klass is set to "rb_cClass" 
and xx->flags is set to "T_CLASS".
4. The xx->super entry is set to the input parameter super.
5. xx->iv_tbl into is set to zero.
6. xx->m_tbl is initialized as empty numeric Hash Table.
7. The *FL_SINGLETON* Flag is set in xx->flags.
8. The Constant  "__attached__"  is inserted into the 
Singleton's iv_tbl.  It's value is the ID of the associated class.
9. Return address of created Metaclass.

__Defining a module__

- The first task is to request a Object Sized (20 Bytes)  block
of memory in ObjectSpace.
- The block is set to zero.
- The Class Object is initalized: xx->klass is set to "rb_cModule" 
and xx->flags is set to "T_MODULE".
- The xx->super entry is set to zero.
- xx->iv_tbl into is set to zero.
- xx->m_tbl is initialized as empty numeric Hash Table.
- Set "__classid__" in xx->iv_tbl with the value of rb_id2name(id).
- Add name/id pair to rb_class_tbl and rb_cObject's iv_tbl.

__Defining a method__

```c
rb_define_method(rb_cArray, "concat", rb_ary_concat, 1);
```

```ruby
Class Array < Object
  def concat (str)
    method implementation code
  end
end
```

__Class Initialization__

When a Class or Module is Instantiated,  it needs to be initialized.  In the case of user defined Classes and Modules,  the "Initialization" method handles this chore.  Built-in Classes and Modules must also be initialized and they all have a init_xxxx()  function that performs the initialization task.

__Rb_define_singleton_method__

```c
void 
rb_define_singleton_method(VALUE obj, const char *name, VALUE(*func), int argc);
{
    rb_define_method(rb_singleton_class(obj), name, func, argc);
}
```

As explained,  rb_define_method  is the normal method definition function.  The difference is the replacement of the Class Name parameter with the rb_singleton_class  function.  Briefly,  this function creates a Singleton Class for the indicated Class,  providing it does not already exist,  then populates it with the method specified.

A Unique Class  is formally referred to as a Singleton Class.  It should be understood that it is a virtual class,  it can not be sub-classed or instatiated.   It's only purpose is to provide a place to mount methods  and instance variables  that are uniquely associated with the specified Class Name.

Loading and requiring files
___
We cannot place all the code in just one single file, otherwise it would be too long and complicated for reading. Usually we put a class per file and use a few different methods in order to 'concatenate' it. Here they are: require, require_relative, load, autoload. Let's start with the first one.

Kernel#require(name) loads the given name, returning true if successful and false if the feature is already loaded. If the filename does not resolve to an absolute path, it will be searched for in the directories listed in $LOAD_PATH ($:). Any constants or globals within the loaded source file will be available in the calling program's global namespace. However, local variables will not be propagated to the loading environment. With this method you can load even native extension(.so, .dll or the others depending on current platform). The absolute path of the loaded file is added to $LOADED_FEATURES ($"). A file will not be loaded again if its path already appears in $". Kernel.require_relative(name) is almost the same as require but it looks for a file in the current directory or directories that is relative to current.
