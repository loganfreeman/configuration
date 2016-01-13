### Ruby thread safety
This page ["How Do I Know Whether My Rails App Is Thread-safe or Not?"](https://bearmetal.eu/theden/how-do-i-know-whether-my-rails-app-is-thread-safe-or-not/) explains all the pitfalls of thread safety in rails

### Nobody understands the GIL
MRI has something called a [global interpreter lock (GIL)](http://www.jstorimer.com/blogs/workingwithcode/8085491-nobody-understands-the-gil). It's a lock around the execution of Ruby code. This means that in a multi-threaded context, only one thread can execute Ruby code at any one time.

### Non-native methods
methods implemented with Ruby code have no atomicity guarantees on MRI. Only methods implemented with native C code have this guarantee.

### Race conditions
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
