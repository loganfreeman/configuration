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
  p local_variables # As the name says, it gives you all local variables in scope

  def some_method # Scope gate
    v2 = 2
    p local_variables
  end # end of def scope gate
end # end of class scope gate

some_class = SomeClass.new
some_class.some_method
```
