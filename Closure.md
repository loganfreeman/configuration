What’s a Closure?
---
A closure is basically a function that:

1. Can be treated like a variable, i.e. assigned to another variable, passed as a method argument, etc.
2. Remembers the values of all the variables that were in scope when the function was defined and is able to access these variables even if it is executed in a different scope.

Put differently, a closure is a first-class function that has lexical scope.

Code Blocks
---
A block is, in effect, an anonymous function that also offers closure-like functionality.
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
