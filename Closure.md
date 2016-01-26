What’s a Closure?
---
A closure is basically a function that:

1. Can be treated like a variable, i.e. assigned to another variable, passed as a method argument, etc.
2. Remembers the values of all the variables that were in scope when the function was defined and is able to access these variables even if it is executed in a different scope.

Put differently, a closure is a first-class function that has lexical scope.

Code Blocks
---
using a block allows us to cross a scope gate
```ruby
outer = 1

def m
  inner = 99
  yield inner
  puts "inner var = #{inner}"
end

m {|inner| outer += inner}
puts "outer var = #{outer}"
```
Procs
---
blocks used with the yield statement are not true closures. Blocks can be true closures but they have to be treated as Procs.
```ruby
outer = 1

def m &a_block
  inner = 99
  a_block.call(inner)
  puts "inner var = #{inner}"
  puts "argument is a #{a_block.class}"
end

m {|inner| outer += inner}
puts "outer var = #{outer}"
```
