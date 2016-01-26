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

At any given place in the code, let's define cref to be the first element of the nesting if it is not empty, or Object otherwise.

- If the nesting is not empty the constant is looked up in its elements and in order. The ancestors of those elements are ignored.

- If not found, then the algorithm walks up the ancestor chain of the cref.

- If not found and the cref is a module, the constant is looked up in Object.

- If not found, `const_missing` is invoked on the cref. The default implementation of const_missing raises NameError, but it can be overridden.

####Resolution Algorithm for Qualified Constants

`Billing::Invoice`

Billing::Invoice is composed of two constants: Billing is relative and is resolved using the algorithm of the previous section.

*Leading colons would make the first segment absolute rather than relative: ::Billing::Invoice. That would force Billing to be looked up only as a top-level constant*.

Let's define parent to be that qualifying class or module object, that is, Billing in the example above. 

- The constant is looked up in the parent and its ancestors.

- If the lookup fails, const_missing is invoked in the parent. The default implementation of const_missing raises NameError, but it can be overridden.

Rails autoloading
---
**Rails autoloads files with Kernel#load when config.cache_classes is false, the default in development mode, and with Kernel#require otherwise, the default in production mode.**

####autoload_paths
```ruby
require 'erb'
```
Ruby looks for the file in the directories listed in `$LOAD_PATH`. That is, Ruby iterates over all its directories and for each one of them checks whether they have a file called "erb.rb", or "erb.so", or "erb.o", or "erb.dll". If it finds any of them, the interpreter loads it and ends the search. Otherwise, it tries again in the next directory of the list. If the list gets exhausted, LoadError is raised.

Rails has a collection of directories similar to `$LOAD_PATH` in which to look up post.rb. That collection is called autoload_paths and by default it contains:

- All subdirectories of app in the application and engines. For example, app/controllers. They do not need to be the default ones, any custom directories like app/workers belong automatically to autoload_paths.

- Second level directories app/{controllers,models}/concerns in the application and engines.

- The directory test/mailers/previews.

**config.autoload_paths is accessible from environment-specific configuration files, but any changes made to it outside config/application.rb don't have an effect.**

```ruby
module Admin
  class BaseController < ApplicationController
    @@all_roles = Role.all
  end
end
```
to autoload Role we are going to check if it is defined in the current or parent namespaces, one at a time. So, conceptually we want to try to autoload any of
```ruby
Admin::BaseController::Role
Admin::Role
Role
```
in that order. To do so, Rails looks in autoload_paths respectively for file names like these:
```
admin/base_controller/role.rb
admin/role.rb
role.rb
```

####Automatic Modules
> When a module acts as a namespace, Rails does not require the application to defines a file for it, a directory matching the namespace is enough.

> Suppose an application has a back office whose controllers are stored in app/controllers/admin. If the Admin module is not yet loaded when Admin::UsersController is hit, Rails needs first to autoload the constant Admin.

> If autoload_paths has a file called admin.rb Rails is going to load that one, but if there's no such file and a directory called admin is found, Rails creates an empty module and assigns it to the Admin constant on the fly.
