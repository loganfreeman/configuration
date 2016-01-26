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

Class and Module Definitions are Constant Assignments
---
```ruby
class C
end
```
Ruby creates a constant C in Object and stores in that constant a class object. The name of the class instance is "C", a string, named after the constant.
```ruby
class PostsController < ApplicationController
  def index
    @posts = Post.all
  end
end
```
Post is a regular Ruby constant. If all is good, the constant is evaluated to an object that responds to all.

Constants are Stored in Modules
---
```ruby
module Colors
  RED = '0xff0000'
end
```
First, when the module keyword is processed, the interpreter creates a new entry in the constant table of the class object stored in the Object constant. Said entry associates the name "Colors" to a newly created module object. Furthermore, the interpreter sets the name of the new module object to be the string "Colors".

Later, when the body of the module definition is interpreted, a new entry is created in the constant table of the module object stored in the Colors constant. That entry maps the name "RED" to the string "0xff0000".

Resolution Algorithms
---
####Resolution Algorithm for Relative Constants
