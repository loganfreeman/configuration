Nesting
---
The nesting is an internal stack maintained by the interpreter, and it gets modified according to these rules:

- The class object following a class keyword gets pushed when its body is executed, and popped after it.

- The module object following a module keyword gets pushed when its body is executed, and popped after it.

- A singleton class opened with class << object gets pushed, and popped later.

- When instance_eval is called using a string argument, the singleton class of the receiver is pushed to the nesting of the eval'ed code. When class_eval or module_eval is called using a string argument, the receiver is pushed to the nesting of the eval'ed code.

- The nesting at the top-level of code interpreted by Kernel#load is empty unless the load call receives a true value as second argument, in which case a newly created anonymous module is pushed by Ruby.

It is interesting to observe that `blocks` do not modify the stack. 

In particular the blocks that may be passed to `Class.new` and `Module.new` do not get the class or module being defined pushed to their nesting.
