Classes and Modules
---
The following six C functions constitute to principle API for defining Classes,  Modules  and Methods.
1. rb_define_class
2. rb_define_class_under
3. rb_define_module
4. rb_define_module_under
5. rb_define_method
6. rb_define_singleton_method

__Defining of a Class__
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
