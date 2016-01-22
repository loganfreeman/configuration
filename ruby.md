Ruby thread safety
---
This page ["How Do I Know Whether My Rails App Is Thread-safe or Not?"](https://bearmetal.eu/theden/how-do-i-know-whether-my-rails-app-is-thread-safe-or-not/) explains all the pitfalls of thread safety in rails

Nobody understands the GIL
---
MRI has something called a [global interpreter lock (GIL)](http://www.jstorimer.com/blogs/workingwithcode/8085491-nobody-understands-the-gil). It's a lock around the execution of Ruby code. This means that in a multi-threaded context, only one thread can execute Ruby code at any one time.

Non-native methods
---
methods implemented with Ruby code have no atomicity guarantees on MRI. Only methods implemented with native C code have this guarantee.

Race conditions
---
GIL doesn't prevent ract conditions
```
class Sheep
  def initialize
    @shorn = false
  end

  def shorn?
    @shorn
  end

  def shear!
    puts "shearing..."
    @shorn = true
  end
end

sheep = Sheep.new

5.times.map do
  Thread.new do
    unless sheep.shorn?
      sheep.shear!
    end
  end
end.each(&:join)
```
Ruby Strings - Creation and Basics
---
* Quoting Ruby Strings
```ruby
myString = "This is also my string.\nGet your own string"
```
* General Delimited Strings
Ruby allows you to define any character you want as a string delimiter simply by prefixing the desired character with a %. For example, we could use the ampersand to delimit our string:
```ruby
myString = %&This is my String&
```
* Ruby Here Documents
A Here Document (or heredoc as it is more commonly referred to) provides a mechanism for creating free format strings, preserving special characters such as new lines and tabs.

A here document is created by preceding the text with << followed by the delimiter string you wish to use to mark the end of the text. The following example uses the string "DOC" as the delimiter:
```ruby
myText = <<DOC
Please Detach and return this coupon with your payment.
Do not send cash or coins.

Please write your name and account number on the check and
make checks payable to:

        Acme Corporation

Thank you for your business.
DOC
```
Local Variable
---
The Ruby interpreter will put a local variable in scope whenever it sees it being assigned to something.

It doesn’t matter if the code is not executed, the moment the interpreter sees an assignment a local variable, it puts it in scope:
```ruby
if false # the code below will not run
  a = 'hello' # the interpreter saw this, so the local var. is in scope from now on
end
p a # nil, since that code didn't execute, thus the variable wasn't initialized
```
Local Variable Naming Conflicts
---
In Ruby, methods can be called without an explicit receiver and any parentheses, just like local variables. 

If you have a local variable being initialized and a method call with a same name in the same scope ,the local variable will “shadow” the method and take precedence. That doesn’t mean that the method is gone and cannot be accessed at all. You can easily get access to it by adding either parentheses at the end (with something()) or adding an explicit self receiver before it (with self.something).
```ruby
def something
  'hello'
end

p something
==> hello
something= 'Ruby'
p something
==> Ruby #'hello' is not printed
```

```ruby
def some_var; 'I am a method'; end
public :some_var # Because all methods defined at the top level are private by default
some_var = 'I am a variable'
p some_var # I am a variable
p some_var() # I am a method
p self.some_var # I am a method
```
Scope Gates: An Essential Concept to Understanding Scope
---
1. Define a class (with class SomeClass)
2. Define a module (with module SomeModule)
3. Define a method (with def some_method)?

Every time you do any of these three things, you enter a new scope. It’s like Ruby opening a gate for you and taking you to an entirely different context with entirely different variables.

Every method/module/class definition is known as a scope gate, because a new scope is created. The old scope is no longer available to you, and all variables available in it are replaced with the new ones.
```ruby
v0 = 0
class SomeClass # Scope gate
  v1 = 1
  p local_variables # As the name says, it gives you all local variables in scope. this prints out [:v1]

  def some_method # Scope gate
    v2 = 2
    p local_variables # this prints out [:v2]
  end # end of def scope gate
end # end of class scope gate

some_class = SomeClass.new
some_class.some_method
```
Module/method/class definitions, as we’ve seen previously, limit the visibility of variables. If you have local variables in a class, when a new method is defined in the class the local variables in the class are no longer available, as we’ve seen. What if you wanted to still have access to these variables despite the method definition? How can you “break” those scope gates?

The key to this is simple: Replace scope gates with method calls. More specifically, replace:

1. class definitions with **Class.new**
2. module definitions with **Module.new**
3. method definitions with **define_method**
```ruby
v0 = 0
SomeClass = Class.new do
  v1 = 1
  p local_variables # this prints out [:v1, :v0, :some_class]

  define_method(:some_method) do
    v2 = 2
    p local_variables # this prints out [:v2, :v1, :v0, :some_class]
  end
end

some_class = SomeClass.new
some_class.some_method
```
After you run this, you’ll see 2 lines printed: [:v1, :v0, :some_class] and [:v2, :v1, :v0, :some_class]. We successfully broke each and every scope gate and made the outside variables available. 

How Can Two Methods Share the Same Variable?
```ruby
def let_us_define_methods
  shared_variable = 0

  Kernel.send(:define_method, :increase_var) do
    shared_variable += 1
  end

  Kernel.send(:define_method, :decrease_var) do
    shared_variable -= 1
  end
end

let_us_define_methods # methods defined now!
p increase_var # 1
p increase_var # 2
p decrease_var # 1
```

Are Blocks Scope Gates?
---
```ruby
sample_list = [1,2,3]
hi = '123'
sample_list.each do |item| # the block scope begins here
  puts hi # will this print 123 or produce an error?
  hello = 'hello' # declaring and initializing a variable
end

p hello # undefined local variable or method "hello"
```
As you can see with “hello”, variables inside a particular block are local to that block and are not available anywhere else.

Not only can you access outside variables, but change their content as well! Try putting hi = '456' inside do/end and its content will be changed.

What if you don’t want blocks to modify outside variables? Block-local variables can help. To define block-local variables, put a semicolon at the end of the block’s parameters (the block below has only 1 parameter, i) and then just list them:
```ruby
hi = 'hi'
hello ='hello'
3.times do |i; hi, hello|
  p i
  hi = 'hi again'
  hello = 'hello again'
end
p hi # "hi"
p hello # "hello"
```
Did you expect 1 2 2 2? The answer is: 1 2 1 2. Each iteration using times is a new block definition that resets the local variables inside it. 
```ruby
2.times do
  i ||= 1
  print "#{i} "
  i += 1
  print "#{i} "
end
```
[Ruby Closure](http://www.sitepoint.com/understanding-scope-in-ruby/)
---
A closure is simply code containing behavior that can:
- be passed around like an object (which can be called later)
- remember the variables that were in scope when the closure (lambda in this example) was defined.
```ruby
def foo
  x = 1
  lambda { x }
end

x = 2

p foo.call # The answer is 1. 
# The reason for this is, blocks and block objects (procs, lambdas) see the scope in their definition and not in their invocation.
```
This can come in handy in various cases, like defining an infinite number generator:
```ruby
def increase_by(i)
  start = 0
  lambda { start += i }
end

increase = increase_by(3)
start = 453534534 # won't affect anything
p increase.call # 3
p increase.call # 6
```

The Top-Level Scope
---
In Ruby, everything is an object. Even when you’re at the top-level, you are in an object (called main, belonging to an Object class).
```ruby
p self # main
p self.class # Object
```
Lambda vs. Proc
---
A block created with lambda behaves like a method when you use return and simply exits the block, handing control back to the calling method.

A block created with Proc.new behaves like it’s a part of the calling method when return is used within it, and returns from both the block itself as well as the calling method.
```ruby
def a_method
 lambda { return "we just returned from the block" }.call
 return "we just returned from the calling method"
end
```
Output: we just returned from the calling method
```ruby
def a_method
 Proc.new { return "we just returned from the block" }.call
 return "we just returned from the calling method"
end
```
Output: we just returned from the block
Exception
---
Exception is the root of Ruby's exception hierarchy, so when you rescue Exception you rescue from everything, including subclasses such as SyntaxError, LoadError, and Interrupt.

Rescuing Interrupt prevents the user from using CTRLC to exit the program.

Rescuing SignalException prevents the program from responding correctly to signals. It will be unkillable except by kill -9.

Rescuing SyntaxError means that evals that fail will do so silently.

All of these can be shown by running this program, and trying to CTRLC or kill it:
```ruby
loop do
  begin
    sleep 1
    eval "djsakru3924r9eiuorwju3498 += 5u84fior8u8t4ruyf8ihiure"
  rescue Exception
    puts "I refuse to fail or be stopped!"
  end
end
```
Rescuing from Exception isn't even the default. Doing
```ruby
begin
  # iceberg!
rescue
  # lifeboats
end
```
does not rescue from Exception, it rescues from StandardError. 

If you have a situation where you do want to rescue from StandardError and you need a variable with the exception, you can use this form:
```ruby
begin
  # iceberg!
rescue => e
  # lifeboats
end
```
One of the few common cases where it’s sane to rescue from Exception is for logging/reporting purposes, in which case you should immediately re-raise the exception:

```ruby
begin
  # iceberg?
rescue Exception => e
  # do some logging
  raise e  # not enough lifeboats ;)
end
```
Binding
---
Objects of class Binding encapsulate the execution context at some particular place in the code and retain this context for future use. Binding objects can be created using Kernel#binding, and are made available to the callback of Kernel#set_trace_func.

These binding objects can be passed as the second argument of the Kernel#eval method, establishing an environment for the evaluation.
```ruby
class Demo
  def initialize(n)
    @secret = n
  end
  def get_binding
    return binding()
  end
end

k1 = Demo.new(99)
b1 = k1.get_binding
k2 = Demo.new(-3)
b2 = k2.get_binding

eval("@secret", b1)   #=> 99
eval("@secret", b2)   #=> -3
eval("@secret")       #=> nil
```
Dynamic property
---
Ruby doesn’t have the concept of properties, so in the following snippet of Ruby code,
```ruby
widget.name = "Wobble"
```
Ruby is actually looking for a method called name= and passing the string “Wobble” as a parameter.
- attr_accessor
```ruby
class Widget
end

widget = Widget.new
class Widget
  attr_accessor :name
end
widget.name = "Wobble"
puts widget.name
```
- Using method_missing
```ruby
class Widget
  def method_missing(name, *args, &block)
    if name =~ /.*=$/      
      instance_variable_set("@" + name.to_s.chop, args[0])
      return args[0]
    end
    if name =~ /.*[^=]$/
      return instance_variable_get("@" + name.to_s)
    end
    raise "I can't figure out what you're trying to do"
  end
end

widget.surname = "Bobble"
puts widget.surname
```
Constant Resolution
---
Ruby constant is anything that starts with a capital.

When Ruby tries to resolve a constant, it starts looking in current lexical scope by searching the current module or class. If it can’t find it there, it searches the enclosing scope and so on.

It’s easy to see the lexical scopes search chain with Module::nesting method:
```ruby
module A
  A_CONSTANT = 'I am defined in module A'
  module B
    module C
      def self.inspect_nesting

        puts Module.nesting.inspect
        puts A_CONSTANT
      end
    end
  end
end

A::B::C.inspect_nesting
# => [A::B::C, A::B, A]
# => I am defined in module A
```
Nesting modules using alternative syntax
```ruby
module Screen
  DEFAULT_RESOLUTION = [1024, 768]
  module Widgets
    module MacOS
    end
  end
end

# Alternative syntax

module Screen::Widgets::MacOS::Button
  def self.inspect_nesting
    puts Module.nesting.inspect
    puts DEFAULT_RESOLUTION
  end
end

Screen::Widgets::MacOS::Button.inspect_nesting
# => [Screen::Widgets::MacOS::Button]
NameError: uninitialized constant Screen::Widgets::MacOS::Button::DEFAULT_RESOLUTION
  from (irb):26:in `inspect_nesting'
  from (irb):29
```
